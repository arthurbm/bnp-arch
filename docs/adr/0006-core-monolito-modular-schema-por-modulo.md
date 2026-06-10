# Core do Parque: monólito modular com schema por módulo

O Core concentra capacidades de domínio heterogêneas (Contas, Spottings & Aglomeração, Assinaturas & Notificações, Conteúdo Contextual, Rotas) atendendo majoritariamente o App do Visitante. Para que ele não degenere num emaranhado, decidimos: o Core é internamente um monólito modular — cada módulo tem seu **próprio schema no banco**, e **módulo não lê tabela de outro módulo**; a comunicação entre módulos acontece por interface interna ou evento. Se um módulo crescer a ponto de merecer virar serviço, a extração fica barata: o schema já é dele e os eventos já fluem pelo Kafka.

Este ADR existe para barrar a "simplificação" futura óbvia: um JOIN atravessando schemas de módulos diferentes. Essa fronteira é deliberada.

## Consequences

- Consultas que cruzam módulos (ex.: notificação que precisa de dados de Conta e de Spotting) são compostas na camada de aplicação ou via dados replicados por evento — nunca por leitura direta do schema alheio.
