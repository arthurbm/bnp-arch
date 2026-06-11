# Opinião do grupo e comparativo com a atividade anterior

> **[RASCUNHO — a ser escrito pelo grupo.]** O professor pediu explicitamente as opiniões *de vocês*, não as do LLM. Este arquivo traz a estrutura sugerida e, em cada seção, a matéria-prima coletada durante a sessão — fatos que vocês podem citar. O texto final deve sair das palavras de vocês, incluindo discordâncias.

## 1. Como trabalhamos das duas vezes

*[Escrever: na atividade anterior, prompt direto pedindo a arquitetura; nesta, sessão estruturada em níveis (Capabilities → Components → Interactions → Contracts), com 17 perguntas, opções com trade-offs e aprovação nossa em cada checkpoint, seguindo o artigo Design-First Collaboration.]*

## 2. O que mudou no resultado

Matéria-prima:

- Na atividade anterior, o LLM recomendou uma arquitetura em camadas parecida com um monólito modular e, em cima disso, ainda empilhou vários microserviços — fronteiras por "domínio bonito", sem justificativa de carga ou criticidade. Nesta sessão, a decomposição saiu com 3 serviços, cada fronteira justificada por um critério mensurável (volume de escrita da telemetria; isolamento de falha do caminho de emergência — ADR 0002).
- Desta vez cada decisão tem alternativa rejeitada documentada (9 ADRs), glossário canônico de 15 termos e critérios de qualidade avaliados por cenários.
- *[Anexar o diagrama da atividade anterior lado a lado com o novo e comentar as diferenças.]*

## 3. Onde NÓS mudamos o design (a colaboração de verdade)

Matéria-prima — três reversões e duas contribuições:

- **Modelo Waze (ADR 0007):** o LLM propôs agregação automática de relatos por heurística (raio + janela + espécie, entidade "Presença"); nós contrapropusemos o modelo Waze — confirmação humana sobre um Spotting compartilhado — que elimina a heurística e sua taxa de erro. *[Opinião: como foi discordar e ver o design melhorar?]*
- **O "recebi" morto:** questionamos a utilidade do ack manual; a discussão produziu algo melhor — ack de máquina (Alcançabilidade) + ato humano único (Compromisso), e o ADR 0003 foi emendado em sessão.
- **Formato híbrido (ADR 0008):** rejeitamos Protobuf uniforme; o LLM criticou a própria recomendação ao perceber que a fronteira do híbrido coincide com a fronteira de carga já decidida.
- **Contribuições nossas que não vieram do LLM:** relato adaptativo por zonas (inspiração no Citta Mobbi) e detecção de saída de rota; exigência de store-and-forward para a rede instável.
- Sobre a decomposição: concordamos que para o contexto do parque — algo pontual, sem necessidade de microserviços em excesso — os 3 serviços por carga/criticidade fazem mais sentido que a proposta da atividade anterior.

## 4. O que achamos do processo

*[Escrever: custo × benefício. O processo foi mais lento que pedir a arquitetura pronta? Entendemos cada decisão em vez de receber um bloco para decifrar? Em que tipo de tarefa NÃO vale a pena (o próprio artigo admite que para tarefas triviais o overhead não se justifica)? Usaríamos de novo?]*

## 5. Opinião sobre a arquitetura final

*[Escrever: confiamos nela? O que ainda nos incomoda? Pontos honestos para citar: o serviço de Emergência como ponto singular (mitigado por redundância + SMS); a dependência de cooperação dos visitantes no modelo Waze; as decisões deixadas em aberto (hospedagem, auth) — falta de informação ou falha nossa em perguntar?]*
