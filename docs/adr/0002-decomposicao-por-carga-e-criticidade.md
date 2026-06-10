# Decomposição em poucos serviços, separados por carga e criticidade

O backend é decomposto em três serviços, com fronteiras justificadas por características mensuráveis e não por afinidade de domínio: (1) **Ingestão de Telemetria** — escrita contínua e volumosa de posições de milhares de carros, que não pode enfileirar atrás de nada; (2) **Emergência** — volume baixíssimo, criticidade máxima, precisa sobreviver à queda de todo o resto; (3) **Core do Parque** — spottings, assinaturas, conteúdo contextual e contas: volume moderado, criticidade média. O argumento decisivo: o caminho de emergência não pode compartilhar destino com o mapa de spottings — quando o parque inteiro estiver reportando um leão, o botão de pânico tem que continuar funcionando.

## Considered Options

- **Monólito modular + broker**: operacionalmente mais barato e adequado a uma TI pequena; rejeitado porque dentro de um único processo a isolação de falha do caminho de emergência nunca é total.
- **Microserviços por capacidade de negócio** (~6 serviços): melhor para crescimento de equipe e deploys independentes; rejeitado porque o custo operacional chega antes do benefício na escala de um parque.
