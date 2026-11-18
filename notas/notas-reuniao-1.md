# Notas condensadas — 1ª reunião com o HSL

Consolida as notas de Guilherme e Maurício, mais as abas "Anotações entrevista" e "Falas" do Google Doc. Itens marcados com (?) estavam incertos na anotação original.

## Escala e organização

- 25 colaboradores na TI do hospital.
- Cerca de 3500 funcionários no total.
- 60 sistemas ou subsistemas em operação, todos orbitando o MV.
- Cerca de 20 sistemas vêm de startups parceiras.
- Cerca de 80 servidores sob a matriz de responsabilidades com o GTIT.
- Servidores locais ficam na PUCRS.

## MV e arquitetura de dados

- O MV é o módulo central de gestão hospitalar. Reúne o histórico dos pacientes e concentra os demais módulos.
- O prontuário é integrado ao MV, incluindo o módulo administrativo, a conta e o PEP (Prontuário Eletrônico do Paciente).
- Dados de pacientes ficam em base Oracle do MV, com backup em nuvem e segregação entre dados de pacientes e os demais.
- Dados textuais ficam no banco. Imagens de exame ficam em file server, segregado do administrativo.
- Dados administrativos são guardados inclusive em mídia física.
- O módulo de imagens de exames é integrado ao MV. O CDI é o centro de diagnóstico de imagem.
- O hospital está transferindo sistemas legados para um serviço centralizado, para reduzir a fragmentação.
- O SAM (serviço de arquivo médico) sofre com prontuário fragmentado. Há obrigatoriedade de prontuário único, então entregar o prontuário de um paciente exige reunir informações de várias origens.
- O pacote "Green" é usado para subir e baixar documentos e disponibilizá-los em formato digital.
- É possível importar exames externos, dependendo do tipo de dado. O acesso a exames feitos em outros lugares é difícil e pode exigir refazer o exame.
- Dados são compartilhados entre operadoras, incluindo SUS e Unimed.
- Sinais vitais são reportados manualmente. Há projeto de integração com o MV.
- Aplicações em Java. Stack citada: nginx e Tomcat.
- Sistemas mais críticos: MV e PACS.
- O MV roda em HTTP. Há previsão de migração para HTTPS.
- Migração para HTML5 em 2025.
- "zero glosa" (?).

## Classificação de dados

- Dados de saúde são todos sensíveis. Religião e sexualidade também.
- Identificações não são sensíveis, mas são pessoais.
- Distinção entre dado identificável e dado identificado orienta os papéis.

## Identidade e provisionamento

- O fluxo começa no RH, que gera a matrícula. A matrícula vai para o AD da Microsoft.
- A criação de usuários nos sistemas é manual: abre-se chamado para a TI, que cadastra o profissional. Ainda não há integração com o AD.
- Senha sem exigência de caracteres especiais, por conveniência, para reduzir esquecimento. Há restrições de conteúdo, como proibir nome de familiares.
- Funcionários desligados têm o usuário desabilitado.
- Funcionários em férias ou afastados têm acesso bloqueado temporariamente.

## Acesso clínico e auditoria de acesso

- Todo profissional de saúde consegue acessar os dados de qualquer paciente. As credenciais dão esse alcance.
- É preciso estar na rede do hospital. Um aplicativo permite acesso externo, e alguns médicos têm VPN.
- Existe rastreio dos acessos, mas a verificação de acesso indevido é manual.
- A trilha de auditoria de modificação depende do módulo. Cada sistema tem a própria auditoria.
- Audita-se o acesso e a modificação de laudos.
- Todo colaborador assina termo de confidencialidade e termo de conduta na integração.
- Existem sistemas de ocorrência para justificar acessos em casos específicos, como prontuários de pacientes que o profissional realmente atendeu.
- Não há aviso automático de acesso anômalo.
- O acesso não pode ser restrito por andar. A rotatividade de profissionais entre andares é alta e a cobertura de turnos torna o acesso dinâmico.

## Rede e perímetro

- Cada prédio tem a própria segmentação de rede. Não há segmentação por andar.
- A rede interna do hospital, HSLCorp, dá acesso ao MV. De fora, só por VPN.
- O acesso a essa rede é rastreável.
- Acesso à internet é limitado por política de gateway, com bloqueio de sites.
- Pen drive não é permitido.
- Firewall de borda externa e firewall de datacenter.

## Terceiros

- Call center terceirizado usa o MV.
- Terceiros acessam o MV para dados de cadastro. Têm login alternativo com escopo definido por papel, por exemplo perfil só de agendamento. O processo de concessão ainda é manual.
- A restrição por módulo vale também para o pessoal do hospital. O setor financeiro acessa só o módulo financeiro.
- Canais no WhatsApp com integração feita por terceiros. Riscos levantados: vazamento de dados e compartilhamento de documentos com malware.
- Os termos de partilha de dados do bot de atendimento do WhatsApp dependem dos termos da Meta.
- Orpen (grafia a confirmar) é o novo serviço de atendimento. Guarda dados de pacientes em nuvem AWS e é um ponto de falha terceirizado.
- Startups precisam cumprir exigências estritas para integrar ao hospital.
- Há auditoria quando se fecham contratos e parcerias.

## Equipamentos e legado

- Equipamentos médicos antigos não ficam conectados à rede.
- Outros equipamentos antigos ficam em sub-rede dedicada, segmentados inclusive fisicamente com switches, permitindo apenas fax.
- Alguns usam Windows XP ou Windows 7. Há esforço de atualizar vários PCs para Windows 11.

## Responsabilidades e operação

- Existe matriz de responsabilidades entre o GTIT e a equipe interna.
- O GTIT cuida da infraestrutura, mantém disponibilidade e faz backups. A administração de infraestrutura é da universidade.
- A operação dos servidores é do hospital, incluindo reinícios.
- Equipe de segurança compartilhada. O GTIT tem 1 especialista e 3 analistas.
- O Tomcat usado pelo MV precisa ser reiniciado cerca de uma vez por semana para liberar recursos, porque os logs consomem muito espaço. O reinício do MV é crítico e atrasa atendimentos.

## Monitoramento e incidentes

- Há alerta de eventos de segurança. Um caso de ransomware foi avisado rapidamente pela equipe do GTIT.
- Logs acessíveis por até 1 ano. A qualidade dos logs é baixa (?).
- O monitoramento é manual.
- Existem protocolos do hospital e da universidade para ataque e vazamento, conforme a LGPD e os contratos.
- "autotrend" é citado como ferramenta de busca de vulnerabilidade (?).

## Treinamento

- Integração de 2 a 7 dias, com curso sobre cuidado no compartilhamento de informações.
- Programa de segurança continuada.
- Testes de phishing já realizados. Em um caso, 30 pessoas caíram, incluindo gestores.
- Moodle usado para treinamento.

## IA

- A política de uso e acesso a IA ainda não está definida.
- Alguns profissionais têm acesso e outros não, por conta do bloqueio geral de internet. O uso não é controlado.

## Pesquisa e ensino

- Dados usados em ensino e pesquisa passam por comitê de ética antes de serem usados ou compartilhados com parceiros.
- É exigido consentimento do paciente.

## Site

- O primeiro site foi feito em WordPress. O site será refeito por completo.

## Expectativa do cliente

- Diagnóstico de possíveis vulnerabilidades em alto nível, incluindo a presença de múltiplos domínios.
- Apontar fragilidades e, se possível, as soluções.
- Pontos citados: ausência de gestão de termos, domínios, vulnerabilidades, arquivos criados por membros do hospital que estejam públicos, e ações recomendadas.
