# Protobuf no funil de telemetria, JSON com JSON Schema em todo o resto

A mensagem de posição é o único fluxo de alto volume (centenas/segundo em horário de pico) e atravessa rede celular fraca, onde cada byte custa bateria e probabilidade de falha — e seu schema é física (lat, lon, timestamp, velocidade, bateria), estável por anos. Os eventos de domínio (Spotting, Supressão, Designação) são baixo volume e alta volatilidade. Decidimos formato por fluxo, reaplicando o critério de carga do ADR 0002 e o critério volátil×estável do ADR 0005: **Protobuf** nas mensagens do funil de telemetria; **JSON** em todo o resto.

Duas guardas obrigatórias para o híbrido não apodrecer:

1. **JSON não significa "sem schema"**: toda mensagem JSON tem JSON Schema versionado no repositório, com a mesma regra de evolução do Protobuf (campos novos não quebram apps antigos — versões velhas vivem meses nas lojas).
2. **A regra é a do funil, não a do gosto**: mensagem nova é JSON por padrão; só entra no Protobuf se tiver o perfil de volume da Telemetria.

## Considered Options

- **Protobuf de ponta a ponta**: uniformidade e contratos formais em tudo; rejeitado por distribuir o custo de ferramental (codegen, depuração binária) sobre fluxos de dezenas de eventos/hora, onde o binário não compra nada.
- **JSON de ponta a ponta**: simplicidade máxima; rejeitado porque pagar ~6× mais bytes no fluxo dominante, em rede fraca, contradiz a própria razão de termos escolhido MQTT.
