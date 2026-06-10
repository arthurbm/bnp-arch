# Notas para o documento comparativo (opinião do grupo)

Observações coletadas durante a sessão Design-First para usar no entregável de opinião/comparação com a atividade anterior.

> **Atenção (do enunciado):** o professor quer a opinião *do grupo*, não a do LLM. Este arquivo guarda matéria-prima e um esqueleto de estrutura — o texto final deve ser escrito por vocês, com as suas palavras e as suas discordâncias inclusive.

## Esqueleto sugerido para o documento de opinião

1. **Como trabalhamos das duas vezes** — prompt direto (atividade anterior) × sessão por níveis com checkpoints (esta).
2. **O que mudou no resultado** — usar as observações abaixo; comparar os dois diagramas lado a lado.
3. **Onde nós mudamos o design** — Waze, "recebi", formato híbrido: o que sentimos ao discordar da IA e ela ceder (ou resistir com bons argumentos).
4. **O que achamos do processo** — custo (mais lento?) × benefício (entendemos cada decisão?); usaríamos de novo? Em que tipo de tarefa não vale a pena (o próprio artigo admite isso)?
5. **Opinião sobre a arquitetura final** — confiamos nela? O que ainda nos incomoda?

## Matéria-prima (coletada em sessão)

- **Modelo de Spotting (Nível 3):** o LLM propôs agregação automática de relatos por heurística (raio + janela + espécie, entidade "Presença"); a equipe contrapropôs o modelo Waze — confirmação humana sobre um Spotting compartilhado — que elimina a heurística e sua taxa de erro mantendo a entidade ancorada de que a Supressão precisa. Caso concreto de colaboração mudando o design para melhor (e exemplo de que a opinião humana corrigiu o LLM, não o contrário). Ver ADR 0007.

- **Opinião do grupo — decomposição (Nível 2):** na atividade anterior (prompt "cru"), o LLM recomendou uma arquitetura em camadas parecida com um monólito modular e, em cima disso, ainda empilhou vários microserviços — fronteiras por "domínio bonito", sem justificativa de carga ou criticidade. Nesta sessão, com o processo Design-First, a decomposição saiu com 3 serviços, cada fronteira justificada por um critério mensurável (volume de escrita da telemetria; isolamento de falha do caminho de emergência). Bom argumento de evolução para apresentar ao professor.
