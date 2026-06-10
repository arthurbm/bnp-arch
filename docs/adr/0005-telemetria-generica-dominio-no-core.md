# Telemetria é serviço espacial genérico; lógica de aglomeração mora no Core

A consulta de proximidade é necessária a três features distintas (contagem de carros perto de animais, Designação por proximidade, política adaptativa de frequência) — o que prova que ela é capacidade genérica de infraestrutura, não regra de negócio. Decidimos: o serviço de Telemetria mantém o índice espacial de Últimas Posições Conhecidas e oferece capacidades genéricas (contagens por Zona de Interesse registrada, consultas de proximidade, avaliação de dentro/fora de geometrias registradas), sem conhecer Supressão, limiar ou espécie. Toda a regra de aglomeração — criar zonas a partir de spottings, limiar por espécie/zona, decidir e publicar Supressão — mora no Core.

Critério de decisão: regra de aglomeração é política do parque e mudará sempre; índice espacial é física e fica estável por anos. A parte volátil mora no serviço de domínio (barato de mudar); a estável, no serviço de maior carga (onde deploys frequentes são indesejados).

## Considered Options

- **Decidir a Supressão dentro da Telemetria** (decisão onde o dado nasce, sem hop): rejeitado porque conceitos de domínio vazariam para o funil de escrita de alto volume, e cada ajuste de política exigiria deploy no serviço mais sensível do sistema.
