# Avaliação de vulnerabilidades em ambiente de laboratório

Documento base para a apresentação de Segurança de Sistemas. O conteúdo cobre o
processo de identificar serviços expostos, determinar versões de software e
associar essas versões a vulnerabilidades conhecidas. Todos os alvos são
aplicações e máquinas feitas para treino, com permissão explícita de teste.

## 1. Escopo e ambiente

Varredura de vulnerabilidades só é legal contra sistemas próprios ou com
autorização por escrito do responsável. Scan ativo contra um alvo de terceiro
sem esse consentimento é crime (no Brasil, Lei 12.737/2012) e, em
infraestrutura crítica como hospital, o risco operacional é real. Por isso o
laboratório usa alvos intencionalmente vulneráveis:

- **OWASP Juice Shop** — aplicação web moderna (Node.js/Express + Angular) com
  as falhas do OWASP Top 10 embutidas. Alvo para a camada de aplicação.
- **Metasploitable 2** — máquina virtual Linux com serviços de rede
  desatualizados. Alvo para enumeração de portas e versões.
- **scanme.nmap.org** — host mantido pelo projeto Nmap para testar o próprio
  Nmap. Alvo para demonstrar a ferramenta sem montar VM.

Montagem rápida dos dois primeiros com Docker:

```sh
# Juice Shop em http://localhost:3000
docker run -d -p 3000:3000 bkimminich/juice-shop

# Metasploitable 2 (imagem community); alternativa: VM oficial na VirtualBox
docker run -d tleonjza/metasploitable2
```

## 2. Metodologia

O processo tem quatro etapas. Cada etapa produz a entrada da seguinte.

1. **Reconhecimento** — descobrir quais hosts e portas respondem.
2. **Fingerprinting** — identificar o software e a versão por trás de cada
   serviço.
3. **Detecção de versão vulnerável** — associar versão a CVE conhecido.
4. **Confirmação** — validar se a falha é explorável, sem causar dano.

A distinção entre detecção passiva e ativa importa. Leitura de cabeçalhos HTTP
que o navegador já recebe é passiva. Um scan de portas é ativo e deixa registro
no alvo. O laboratório permite as duas; contra um sistema real sem autorização,
nenhuma.

## 3. Reconhecimento e enumeração de serviços

O `nmap` com `-sV` identifica porta aberta e versão do serviço. Os scripts NSE
(`-sC` ou `--script`) acrescentam checagens específicas.

```sh
# Enumeração de serviços e versões no Metasploitable
nmap -sV -sC -p- 10.0.2.15

# Scripts de vulnerabilidade conhecidos sobre os serviços encontrados
nmap -sV --script vuln 10.0.2.15
```

Saída típica do Metasploitable 2, com as versões relevantes:

| Porta | Serviço  | Versão         | Vulnerabilidade associada        |
|-------|----------|----------------|----------------------------------|
| 21    | vsftpd   | 2.3.4          | Backdoor, CVE-2011-2523          |
| 22    | OpenSSH  | 4.7p1          | Versão antiga, chaves fracas     |
| 139   | Samba    | 3.0.20         | Usermap script, CVE-2007-2447    |
| 445   | Samba    | 3.0.20         | Mesmo CVE-2007-2447              |
| 6667  | UnrealIRCd | 3.2.8.1      | Backdoor, CVE-2010-2075          |
| 8180  | Tomcat   | 5.5            | Credenciais padrão no manager    |

A tabela é o núcleo da etapa: cada linha é uma versão que pode ser pesquisada
em base de vulnerabilidades.

## 4. Fingerprinting da camada web

Para aplicação web, a identificação é da stack (servidor, framework, CMS) e das
bibliotecas de frontend.

```sh
# Identifica servidor, framework e tecnologias da página
whatweb http://localhost:3000

# Cabeçalhos HTTP revelam servidor e, às vezes, versão
curl -sI http://localhost:3000
```

No Juice Shop, o `whatweb` reporta Node.js com Express e frontend Angular. O
cabeçalho `X-Powered-By: Express` confirma o servidor. Bibliotecas JavaScript
desatualizadas aparecem na análise do bundle; o próprio Juice Shop expõe
dependências com CVE como um dos desafios.

A extensão Wappalyzer faz o mesmo fingerprint pelo navegador, sem enviar tráfego
extra ao alvo além do carregamento normal da página.

## 5. Scan de aplicação

Duas ferramentas cobrem a varredura automatizada.

```sh
# Nikto: arquivos, configurações inseguras e versões conhecidas no servidor web
nikto -h http://localhost:3000

# Nuclei: templates de CVE e exposições conhecidas
nuclei -u http://localhost:3000
```

O `nikto` aponta cabeçalhos de segurança ausentes, métodos HTTP permissivos e
arquivos sensíveis expostos. O `nuclei` roda milhares de templates mantidos pela
comunidade e marca cada acerto com o CVE ou a categoria correspondente.

## 6. Da versão ao exploit

Com a versão em mãos, `searchsploit` consulta a base local do Exploit-DB.

```sh
# Exploits conhecidos para uma versão específica
searchsploit vsftpd 2.3.4
searchsploit unrealircd 3.2.8.1
searchsploit samba 3.0.20
```

O resultado liga a versão a um módulo ou prova de conceito. Para o vsftpd 2.3.4,
a entrada é o backdoor que abre shell na porta 6200 quando o usuário de login
contém `:)`. A confirmação em laboratório valida que a versão é de fato
vulnerável; em um teste autorizado, esse é o limite entre detecção e exploração,
e exige escopo explícito.

## 7. Defesa e mitigação

As mesmas versões que o scan encontra indicam o que corrigir:

- **Atualização de software** — a maioria dos achados do Metasploitable
  desaparece com versões atuais dos pacotes.
- **Ocultar versões** — remover ou alterar banners e cabeçalhos (`X-Powered-By`,
  `Server`) dificulta o fingerprinting, sem substituir a correção.
- **Cabeçalhos de segurança** — `Content-Security-Policy`, `X-Frame-Options`,
  `Strict-Transport-Security` fecham classes inteiras de ataque na camada web.
- **Gestão de dependências** — `npm audit`, Dependabot ou Renovate detectam
  bibliotecas com CVE antes de irem a produção.
- **Segmentação e firewall** — reduzir as portas expostas diminui a superfície
  que um scan alcança.

## 8. Resumo do fluxo

```
Reconhecimento   nmap -p-               portas abertas
      ↓
Fingerprinting   nmap -sV, whatweb      software + versão
      ↓
Detecção         nikto, nuclei          versão vulnerável + CVE
      ↓
Confirmação      searchsploit, NSE      falha explorável (em escopo)
      ↓
Mitigação        patch, headers, audit  superfície reduzida
```
