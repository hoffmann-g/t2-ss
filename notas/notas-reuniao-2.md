# Notas condensadas — 2ª reunião com o HSL

Consolida as notas de Guilherme, Maurício e João. Itens marcados com (?) estavam incertos na anotação original. O que já havia sido levantado na 1ª reunião está em [notas-reuniao-1.md](notas-reuniao-1.md) e aqui só aparece quando foi detalhado ou corrigido.

## Contas, email e desligamento

- O email institucional é desativado logo após o desligamento. Perder o email implica perder os demais acessos.
- O email dos médicos usa o domínio @pucrs.br.
- Nem todos têm email. Estagiários podem não ter (?).
- Alunos de estágio têm acesso ao MV.
- O email é de uso pessoal e único.

## Sessão e senhas

- O login no MV é direto, com timeout de 35 minutos. A assistência usa esse mesmo timeout.
- Orienta-se fazer logout ao sair.
- Se o usuário entra em uma máquina, a sessão da outra é encerrada.
- Senhas são trocadas a cada 3 meses.
- Risco relatado: médicos às vezes emprestam senha uns aos outros.
- Está em planejamento exigir senha ou justificativa para abrir prontuário.

## Acesso físico e crachás

- Dois sistemas tratam crachá e acesso: o controlador de acesso e o sistema do RH que gera o crachá.
- Os crachás são comprados pré-impressos e a matrícula é registrada.
- Não há controle de acesso a portas e catracas por funcionário. O funcionário não tem andar ou setor atribuído digitalmente, porque trocas de turno e transferências para cobrir escala são comuns.
- O resultado é trânsito livre pelo prédio. Existem auditoria e logs de acesso, mas o controle é passivo, não ativo.
- Reforçou-se na entrevista a limitação de todos os profissionais terem acesso aos dados de todos os andares. O ideal apontado seria o acesso restrito ao andar do funcionário.

## Auditoria e detecção

- Não existe automação para detectar picos de acesso a prontuários. Este é o ponto de maior interesse para o foco do trabalho.
- O sistema não tem tracing adequado, então é preciso monitorar e investigar integrações manualmente e cruzar os dados à mão.
- O mesmo vale para funcionários acessando setores anormais.
- O relatório de auditoria do MV é exportado em Excel. A filtragem de ações é quase toda manual.
- Várias ações são registradas, de cliques a ações concretas como acesso e alteração.
- É possível distinguir quem apenas consultou de quem executou uma ação sobre o paciente.
- O sistema identifica se o prontuário já estava aberto por outro funcionário e informa o acesso simultâneo. O acesso simultâneo não deveria ocorrer e, quando ocorre, pode ser investigado.
- As tabelas principais têm auditoria. A restrição é mais rígida do que foi possível anotar.

## Logs e retenção

- Os logs ficam armazenados sem tempo limite definido.
- Registros de prontuário são mantidos indefinidamente. Questões legais limitam a retenção em certos casos, por exemplo 25 anos.
- Existem comissões para decidir o descarte de logs.

## Banco de dados

- Apenas a TI acessa os bancos de dados de cadastro. O acesso é restrito.

## HTTP e instalação do MV

- A instalação do MV levou 14 horas e foi feita em HTTP em vez de HTTPS, por erro de instalação.
- Corrigir exigiria reinstalar o sistema inteiro, com custo de tempo equivalente.

## Terceiros e startups

- Antes de implementar integrações com terceiros é preenchido um formulário de LGPD.
- O formulário é reassinado a cada mudança de contrato. Não há revalidação constante fora disso.
- Serviços que interagem com os sistemas do hospital usam servidor intermediário.
- Os terceiros são avisados quando há atualização de firewall ou equivalente.

## IA e LLM

- A entrada de dados sensíveis em LLMs de mercado é uma preocupação declarada.
- Há tentativa de mitigar o uso.
- A política de uso de IA está em desenvolvimento, definindo o que é permitido.

## Rede e internet

- A rede é segmentada por prédio em toda a PUCRS, como já havia sido informado.
- O acesso à internet é fortemente limitado. Muitos sites são bloqueados por padrão.
- A internet é bloqueada nos PCs de atendimento e liberada para gerentes e setores altos.
- Equipamentos pessoais em geral não são conectados à rede. Quando conectados, ficam segmentados com acesso à internet bloqueado.
- As VLANs são geridas pelo GTIT.
- A VPN usa Check Point (anotado como "checkpoint microsoft").

## Infraestrutura e desenvolvimento

- Cerca de 20 servidores do hospital (?).
- As aplicações são todas web.
- As aplicações das equipes de TI do hospital usam Oracle APEX, de baixa codificação.
- A imagem de sistema operacional é simples e não inclui pacote de escritório.

## Dispositivos dos profissionais

- Alguns sistemas podem ser acessados pelo celular pessoal do funcionário.
- Médicos são PJ e usam os próprios celulares.
- Há histórico de acesso a itens pessoais de funcionários.

## Resposta a incidentes

- Os problemas são comunicados à equipe, acionando GTIT e equipe do hospital.
- O GTIT isola o ativo da rede e a equipe do hospital toma a ação.
- As notificações saem por email ou outros meios.
- O comitê de LGPD mantém cobertura permanente. Quando ocorre um evento, é preciso haver alguém disponível para agir, com uma pessoa de confiança substituindo a principal. Os dois não tiram férias ao mesmo tempo.
- Incidente relatado: acesso a conteúdo pornográfico no hospital, que emitiu alerta ao sistema.

## Engenharia social por telefone

- Há muitos golpes telefônicos de pessoas se passando por familiares de pacientes.
- Telefones do próprio hospital chegam a ser usados nesses golpes.
- Atacantes conseguem informações sobre pacientes quando o funcionário está sob estresse ou desatento.
- Ligações diretas para o leito são um vetor (?).
- A central telefônica é de 1980.
- As chamadas de alguns setores, como o call center, são gravadas.

## Pessoal

- A rotatividade antes se concentrava na assistência. Hoje afeta todas as áreas.

## Hipótese de problema para o trabalho

- O limiar de divisão de responsabilidade entre a PUCRS e a equipe do hospital. A matriz de responsabilidades existe, mas pode tornar a delegação ineficiente na reação a um problema.
