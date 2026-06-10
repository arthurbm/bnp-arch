# MQTT na borda de campo, Kafka no miolo

Os apps (visitante e Equipe) falam com o sistema via **MQTT**: QoS 1 (reentrega até o dispositivo confirmar) e sessões persistentes (o broker retém mensagens de quem caiu da rede e entrega na reconexão) resolvem no protocolo a realidade de rede instável do parque — nas duas direções. O caminho de emergência usa um cluster MQTT dedicado (ver ADR 0003). Entre os serviços, o fluxo de massa (posições, spottings, supressão, fan-out de notificações) passa por **Kafka**: a retenção e o replay transformam a telemetria em ativo de dados — trilhas de movimentação, padrões de aglomeração, pesquisa e conservação — que o parque declarou querer explorar.

## Considered Options

- **RabbitMQ no miolo**: ack por mensagem, retry, prioridades e metade do peso operacional — seria a escolha se apenas o "agora" importasse. Rejeitado porque o grupo confirmou o histórico de movimentação como requisito de dados (pesquisa e melhorias do parque), e replay/retenção é exatamente o forte do Kafka.
- **HTTP/REST direto dos apps**: dispensaria brokers na borda, mas empurraria o tratamento de rede oscilante (retry, buffer offline, dedup) para código nosso em cada app.
