# Roteiro de Entrevista Técnica — Avaliação de Segurança da Informação

**Instituição (cliente):** Hospital São Lucas da PUCRS
**Disciplina:** Segurança de Sistemas — 2026/2 (trilha de Testes de Segurança, Prof. Daniel Dalalana)
**Reunião com o cliente:** 19/10/2026
**Integrantes do grupo:** Endrew Soares, Guilherme Hoffmann, João Sbardelotto, Maurício Alcantra
**Metodologia:** Security Assessment — validação de achados de reconhecimento
**Referências:** CIS Controls v8, NIST SP 800-115, OWASP WSTG, OWASP ASVS, modelagem de ameaças STRIDE

---

## Objetivo da entrevista

Compreender a arquitetura tecnológica, os mecanismos de proteção e as práticas de segurança adotadas pelo Hospital São Lucas da PUCRS, e esclarecer os resultados preliminares obtidos no reconhecimento (Nmap e DIRB), separando possíveis vulnerabilidades de comportamentos esperados da infraestrutura. As respostas orientam o escopo autorizado, as hipóteses de vulnerabilidade e os procedimentos de validação.

As perguntas técnicas estão associadas aos CIS Controls v8 correspondentes, indicados ao final de cada seção.

## Como usar este roteiro

- Cada pergunta tem um identificador (P1.1, P1.2, ...) para ser citada nas anotações e no relatório final.
- As perguntas marcadas com ★ são as essenciais: com uma hora de reunião, são elas que precisam ser respondidas. As demais são complementares e podem ser enviadas por escrito depois.
- Cada seção traz um tempo estimado. A soma das seções essenciais fecha em torno de 58 minutos.
- O espaço **Resposta:** abaixo de cada bloco é para anotação durante a reunião.

---

## 0. Preparação — o que levar e o que pedir

**Objetivo:** garantir que a reunião termine com os insumos necessários para iniciar a avaliação.

**Levar para a reunião:**

- Termo de autorização para testes, para assinatura ou ajuste.
- Resumo dos achados preliminares (seção 12), com data, horário e comandos executados.
- Proposta de janela de testes e de categorias de teste pretendidas.

**Pedir ao cliente até o fim da reunião:**

| Item | Para quê | Responsável no hospital | Prazo |
|------|----------|-------------------------|-------|
| Autorização formal por escrito | Requisito legal para qualquer teste ativo | | |
| Faixa de endereços IP de origem | Separar infraestrutura do hospital de serviços da Cloudflare | | |
| Lista de domínios e subdomínios no escopo | Delimitar os alvos autorizados | | |
| Janela de testes e restrições de horário | Evitar impacto no atendimento | | |
| Contato de emergência durante os testes | Interromper a execução se houver impacto | | |
| Contas de teste por perfil de acesso | Validações controladas de controle de acesso | | |

---

## 1. Contexto e negócio

**Tempo estimado:** 5 min
**Objetivo:** entender o contexto institucional, as obrigações regulatórias e a criticidade dos sistemas, para priorizar a avaliação pelo impacto no atendimento.

- ★ **P1.1** Quais sistemas e serviços são mais críticos para o funcionamento do hospital? Qual indisponibilidade afeta diretamente o atendimento ao paciente?
- ★ **P1.2** Quais obrigações regulatórias e de conformidade se aplicam (LGPD, normas do setor de saúde, exigências de órgãos reguladores)?
- **P1.3** Como o hospital trata o consentimento e a finalidade de uso dos dados pessoais e de saúde?
- ★ **P1.4** Quem é o responsável pela segurança da informação na instituição? Há uma área ou profissional dedicado?
- **P1.5** Existe orçamento e processo definido para tratar os riscos de segurança identificados?
- **P1.6** Já houve incidentes de segurança relevantes? Como foram tratados?
- **P1.7** Existem contratos ou SLAs de segurança com fornecedores e prestadores de serviço?
- ★ **P1.8** Qual é a expectativa do hospital com esta avaliação?

**Resposta:**

**CIS Controls:** 3 (Data Protection), 15 (Service Provider Management), 17 (Incident Response Management).

---

## 2. Escopo e autorização

**Tempo estimado:** 8 min
**Objetivo:** fixar o que pode ser testado antes de qualquer validação ativa, evitando impacto em produção e definindo os limites legais do trabalho.

- ★ **P2.1** Quais domínios, subdomínios, endereços IP e aplicações estão no escopo autorizado?
- ★ **P2.2** Existem sistemas que não devem ser submetidos a testes?
- ★ **P2.3** Há ambiente de homologação para testar sem afetar a produção?
- ★ **P2.4** Quais categorias de teste são permitidas (enumeração, análise de configuração, validação controlada de vulnerabilidades)?
- ★ **P2.5** Existem restrições de horário para a execução dos testes?
- ★ **P2.6** Qual é o procedimento para autorizar formalmente cada etapa da avaliação?

**Resposta:**

**CIS Controls:** 18 (Penetration Testing).

---

## 3. Arquitetura e inventário de ativos

**Tempo estimado:** 4 min
**Objetivo:** entender a superfície exposta e como os ativos são conhecidos e controlados pela equipe.

- ★ **P3.1** Quais serviços estão publicados na internet e quais pertencem à infraestrutura de origem do hospital?
- ★ **P3.2** Existe um inventário atualizado de ativos (servidores, serviços, aplicações) expostos externamente?
- **P3.3** Como novas máquinas e serviços são provisionados? Há um padrão de configuração base (baseline)?
- **P3.4** Existe monitoramento para identificar serviços expostos indevidamente à internet?
- **P3.5** Onde e como é mantida a documentação de arquitetura e especificação dos sistemas?

**Resposta:**

**CIS Controls:** 1 (Inventory and Control of Enterprise Assets), 4 (Secure Configuration of Enterprise Assets and Software).

---

## 4. Aplicações web e dependências

**Tempo estimado:** 5 min
**Objetivo:** confirmar a tecnologia das aplicações e como o código e as bibliotecas de terceiros são protegidos.

Contexto: a enumeração com DIRB sinalizou caminhos como `/site/controllers/`, `/site/models/`, `/site/views/`, `/vendor/composer/` e `/vendor/phpmailer/`, compatíveis com uma aplicação PHP. Os nomes, sozinhos, não comprovam a existência dos diretórios nem o framework em uso.

- ★ **P4.1** Quais linguagens e frameworks são usados nas aplicações web?
- ★ **P4.2** Os diretórios identificados correspondem à estrutura real de alguma aplicação?
- **P4.3** Há separação entre arquivos públicos e componentes internos?
- ★ **P4.4** Como é impedido o acesso HTTP a código-fonte, bibliotecas e arquivos de configuração?
- **P4.5** As dependências de terceiros são inventariadas e monitoradas quanto a vulnerabilidades conhecidas?
- **P4.6** As aplicações passam por revisão de segurança antes de ir a produção?

**Resposta:**

**CIS Controls:** 16 (Application Software Security), 2 (Inventory and Control of Software Assets), 7 (Continuous Vulnerability Management).

---

## 5. Proteção de arquivos e informações sensíveis

**Tempo estimado:** 4 min
**Objetivo:** verificar se arquivos sensíveis podem ser expostos publicamente e onde os controles são aplicados.

Contexto: o DIRB retornou HTTP 403 para caminhos como `/id_rsa`, `/authorized_keys`, `/access_log`, `/error_log` e `/web.config`, todos com o mesmo tamanho de resposta (SIZE:5483) — indício de bloqueio genérico, não de arquivos presentes. Também houve HTTP 200 para `/Makefile`, com SIZE:10, cujo conteúdo não foi validado.

- **P5.1** Existem políticas para impedir a exposição pública de configuração, logs, backups e chaves privadas?
- ★ **P5.2** Esses controles ficam no servidor web, na aplicação ou na Cloudflare?
- **P5.3** Como é verificada a publicação acidental de arquivos durante a implantação?
- ★ **P5.4** A resposta HTTP 200 para `/Makefile` corresponde a um arquivo real ou a uma resposta genérica?
- **P5.5** São feitas auditorias periódicas para identificar exposições acidentais?

**Resposta:**

**CIS Controls:** 3 (Data Protection), 4 (Secure Configuration of Enterprise Assets and Software).

---

## 6. Cloudflare, WAF e proteção de borda

**Tempo estimado:** 6 min
**Objetivo:** atribuir os comportamentos HTTP observados aos mecanismos de borda e entender o que é bloqueio versus erro de origem.

Contexto: durante a enumeração surgiram respostas HTTP 302, 403, 520 e 525 e comportamento inconsistente para caminhos inexistentes. O mesmo caminho respondeu 200 no navegador e 403 no curl, e as respostas trazem o cabeçalho `server: cloudflare` com identificador `cf-ray`.

- ★ **P6.1** Quais proteções da Cloudflare estão habilitadas para as aplicações do hospital?
- ★ **P6.2** Existe um WAF com regras específicas para requisições suspeitas?
- **P6.3** Como são tratados acessos automatizados e tentativas de enumeração de diretórios?
- **P6.4** Há limitação de requisições (rate limiting)?
- ★ **P6.5** As respostas HTTP 403 observadas podem ser atribuídas a regras do WAF?
- **P6.6** Os erros HTTP 525 (falha de handshake TLS) são monitorados?
- ★ **P6.7** É possível correlacionar horários e identificadores `cf-ray` das requisições com os registros da Cloudflare e da origem?

**Resposta:**

**CIS Controls:** 13 (Network Monitoring and Defense), 15 (Service Provider Management).

---

## 7. Segurança de rede e acesso remoto

**Tempo estimado:** 3 min
**Objetivo:** entender a segmentação interna e como o acesso de fora da rede é autorizado e controlado.

- ★ **P7.1** Existe uma sub-rede separada da rede pública? Como a rede é segmentada?
- ★ **P7.2** É possível acessar a rede do hospital de fora? Há VPN? De que tipo?
- **P7.3** Como um usuário é autenticado ao acessar a rede interna?
- **P7.4** O sistema é monitorado contra intrusos? Por quais protocolos? Monitoramento passivo ou ativo?

**Resposta:**

**CIS Controls:** 12 (Network Infrastructure Management), 13 (Network Monitoring and Defense), 6 (Access Control Management).

---

## 8. Autenticação e controle de acesso

**Tempo estimado:** 4 min
**Objetivo:** avaliar como a identidade é verificada e como os privilégios são atribuídos e revisados.

- ★ **P8.1** Como é feita a autenticação dos usuários nos sistemas internos e externos?
- ★ **P8.2** Há autenticação multifator para funcionários e administradores?
- **P8.3** Como senhas são compartilhadas e armazenadas?
- **P8.4** Como são definidas as permissões de médicos, enfermeiros, administrativos e prestadores?
- **P8.5** Existem mecanismos para impedir acesso a recursos fora das permissões do usuário?
- **P8.6** Como ocorre a revogação de acessos quando um funcionário sai da instituição?
- **P8.7** As permissões são revisadas periodicamente?
- ★ **P8.8** É possível disponibilizar contas de teste com diferentes níveis de acesso para validações controladas?

**Resposta:**

**CIS Controls:** 5 (Account Management), 6 (Access Control Management).

---

## 9. Proteção de dados de saúde e backups

**Tempo estimado:** 3 min
**Objetivo:** verificar como dados pessoais e clínicos são protegidos, auditados e recuperados.

- ★ **P9.1** Quais sistemas armazenam ou processam dados pessoais e informações de saúde?
- ★ **P9.2** Como os dados são protegidos em armazenamento e em transmissão?
- **P9.3** O acesso aos prontuários eletrônicos é registrado e auditado?
- **P9.4** Como se garante que cada profissional acesse apenas o necessário às suas atribuições?
- **P9.5** Existe política de retenção, descarte e anonimização de dados?
- **P9.6** Como são feitos os backups e os testes de recuperação?
- **P9.7** Quais procedimentos se aplicam a incidentes envolvendo dados pessoais ou clínicos?

**Resposta:**

**CIS Controls:** 3 (Data Protection), 11 (Data Recovery).

---

## 10. Gestão de vulnerabilidades e processo de correção

**Tempo estimado:** 3 min
**Objetivo:** entender o ciclo de identificação, priorização e correção de falhas, incluindo o desenvolvimento.

- ★ **P10.1** O hospital faz avaliações periódicas de vulnerabilidades? Com que frequência?
- **P10.2** Quais ferramentas ou metodologias são usadas?
- **P10.3** Como são identificadas vulnerabilidades em componentes e bibliotecas de terceiros?
- ★ **P10.4** Como é definida a prioridade de correção?
- **P10.5** Há prazos para corrigir vulnerabilidades críticas, altas e médias?
- **P10.6** Qual é o processo de software para correção de bugs e implementação de novas funcionalidades?
- **P10.7** Como são feitos os testes de segurança após uma correção?
- **P10.8** Existe processo formal para receber e tratar vulnerabilidades reportadas por terceiros?

**Resposta:**

**CIS Controls:** 7 (Continuous Vulnerability Management), 16 (Application Software Security).

---

## 11. Monitoramento e resposta a incidentes

**Tempo estimado:** 3 min
**Objetivo:** confirmar a capacidade de detectar, registrar e responder a eventos de segurança.

- ★ **P11.1** Quais eventos de segurança são registrados e por quanto tempo os logs são retidos?
- **P11.2** Existe correlação centralizada de logs (SIEM)?
- ★ **P11.3** Há um plano formal de resposta a incidentes com papéis definidos?
- **P11.4** Como são classificados e comunicados os incidentes?

**Resposta:**

**CIS Controls:** 8 (Audit Log Management), 13 (Network Monitoring and Defense), 17 (Incident Response Management).

---

## 12. Validação dos achados preliminares

**Tempo estimado:** 6 min
**Objetivo:** confirmar ou descartar, com a equipe do hospital, os resultados obtidos no reconhecimento. Nenhum achado abaixo está comprovado; todos dependem de validação autorizada.

A coluna de alvo e coleta permite que a equipe do hospital correlacione cada observação com os próprios registros da Cloudflare e da origem.

| ID | Ferramenta | Alvo e coleta | Observação | Validação necessária |
|----|-----------|---------------|------------|----------------------|
| REC-01 | Nmap 7.99 | `hospitalsaolucas.pucrs.br` (104.18.20.134), 14/10/2026 02:05 -0300, comando `nmap hospitalsaolucas.pucrs.br` | Portas TCP 80, 443, 8080 e 8443 abertas; 995 portas filtradas | Determinar quais serviços são da infraestrutura de origem |
| REC-02 | DIRB | `hospitalsaolucas.pucrs.br`, das 13:41:04 às 15:44:52 (data a confirmar), 52.181 requisições | Possíveis diretórios `/site/`, `/vendor/`, `/_admin/` e `/_files/` | Confirmar existência e controles de acesso |
| REC-03 | DIRB | mesma coleta de REC-02 | HTTP 200 para `/Makefile`, SIZE:10 | Determinar a natureza e o conteúdo da resposta |
| REC-04 | DIRB | mesma coleta de REC-02 | HTTP 403 com SIZE:5483 idêntico para arquivos potencialmente sensíveis | Identificar a origem dos bloqueios (WAF?) |
| REC-05 | DIRB | mesma coleta de REC-02 | Ocorrência de HTTP 525 | Investigar os registros de erro TLS |
| REC-06 | DIRB | mesma coleta de REC-02 | Redirecionamentos e respostas inconsistentes; 200 no navegador e 403 no curl para o mesmo caminho | Identificar regras de roteamento e proteção |

**Resposta:**

---

## 13. Encerramento

**Tempo estimado:** 4 min
**Objetivo:** alinhar prioridades, limitações e o fluxo de comunicação após a entrevista.

- ★ **P13.1** Quais sistemas a equipe considera prioritários para a avaliação?
- **P13.2** Há riscos conhecidos ou limitações técnicas a considerar durante o trabalho?
- **P13.3** Quais testes adicionais a equipe considera apropriados para validar os resultados?
- ★ **P13.4** Quais evidências técnicas podem ser compartilhadas, respeitando a confidencialidade?
- ★ **P13.5** Qual será o procedimento para comunicar, classificar e acompanhar as vulnerabilidades identificadas?

**Resposta:**

### Ações acordadas

| # | Ação | Responsável | Prazo | Situação |
|---|------|-------------|-------|----------|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |

---

## Resultados esperados

- Definição formal do escopo e das restrições da avaliação.
- Compreensão da arquitetura e dos mecanismos de proteção.
- Confirmação ou descarte dos achados preliminares.
- Identificação de riscos e hipóteses de vulnerabilidade.
- Definição de procedimentos seguros para validação técnica.
- Subsídios para o relatório final (evidências, classificação de riscos e recomendações).
