# Spotting compartilhado com confirmação humana (modelo Waze), sem agregação automática

Trinta carros vendo o mesmo leão não podem virar trinta pins e trinta notificações — e a Supressão precisa de uma entidade ancorada para contar "carros na proximidade do animal". Decidimos o modelo Waze: o primeiro relato **cria** o Spotting (a entidade compartilhada — vai ao mapa, dispara uma única notificação por assinante, ancora Zona de Interesse e Supressão); quem chega depois **Confirma** ("ainda está aqui", renovando frescor) ou nega ("não está mais aqui", acelerando expiração); um animal genuinamente diferente vira um Spotting novo, a critério de quem está olhando para o animal. A deduplicação é humana — não existe clustering automático no servidor. Eventos brutos (criações, confirmações, negações) seguem para o Kafka para pesquisa.

## Considered Options

- **Cada relato é um pin independente com TTL**: modelo honesto e sem heurística, mas gera spam de notificações e explosão de Zonas de Interesse — e a Supressão fica sem referente.
- **Agregação automática por raio + janela + espécie ("Presença")**: resolve a ancoragem, mas a heurística erra nas bordas (dois leões a 300m viram um; um leopardo em movimento vira rastro de fantasmas) e o erro contamina a Supressão. A confirmação humana resolve a mesma deduplicação com mais precisão e menos máquina.

## Consequences

- O modelo depende de cooperação dos visitantes; a expiração por tempo sem Confirmação segura a linha de base sozinha.
