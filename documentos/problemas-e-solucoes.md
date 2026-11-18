# Problemas e riscos levantados — HSL

Fonte: Google Doc "Diário de Campo sobre Hospital São Lucas", aba "Problemas e Soluções". Consolida as observações da entrevista em riscos a investigar no diagnóstico.

| Área | Observação da entrevista | Risco a investigar |
| --- | --- | --- |
| Comunicação do MV | Sistema relatado como HTTP, com migração para HTTPS prevista | Exposição de credenciais e dados em trânsito |
| Controle de acesso | Médicos conseguem consultar prontuários de diferentes pacientes | Acesso sem necessidade assistencial legítima |
| Autenticação | Senhas de até 12 caracteres, sem exigência de especiais | Credenciais potencialmente fracas |
| Auditoria | Logs distribuídos por sistemas e verificações manuais | Demora na detecção de acessos indevidos |
| Gestão de identidades | Criação manual de usuários em alguns sistemas | Contas órfãs e privilégios excessivos |
| Terceirizados | Call center e parceiros acessam módulos do MV | Exposição de dados por terceiros |
| Compartilhamento | Integrações com WhatsApp e serviços externos | Vazamento de informações e arquivos maliciosos |
| Equipamentos legados | Máquinas com Windows XP e Windows 7 | Exploração de sistemas sem suporte |
| Arquivos públicos | Preocupação com documentos publicados inadvertidamente | Exposição de informações institucionais |
| Infraestrutura | MV e PACS são críticos para a operação | Indisponibilidade com impacto assistencial |

## Próximo passo de pesquisa

- MITRE ATT&CK: pesquisar APTs e os ataques mais comuns para o segmento de saúde.
