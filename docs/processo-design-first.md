# Relato do processo — Design-First Collaboration aplicado

Evidência de como a arquitetura foi construída, seguindo [Design-First Collaboration (Rahul Garg, martinfowler.com)](https://martinfowler.com/articles/reduce-friction-ai/design-first-collaboration.html): níveis progressivos com aprovação humana em cada checkpoint, **nenhum artefato de nível seguinte antes de fechar o atual**. LLM utilizado: **Claude (Fable 5)** via Claude Code, em sessão interativa com a equipe em call. A transcrição completa da sessão é o anexo de prompts/evidências.

## Linha do tempo por nível

### Nível 1 — Capabilities (4 perguntas)
- **Escopo**: sistema completo (3 clientes), não só o app do visitante — o fluxo de emergência exige as três pontas.
- **Capacidade escondida**: o rastreamento contínuo não está escrito no enunciado, mas duas features morrem sem ele. Promovido a capacidade de primeira classe, com store-and-forward exigido pela equipe (rede instável).
- **Contribuição da equipe**: relato adaptativo por zonas (inspiração Citta Mobbi) + detecção de saída de rota — extensão proativa além do enunciado.
- **Ambiguidade resolvidas**: "tempo razoável" virou promessa explícita (quase-tempo-real com entrega eventual); a tensão "encaminha a todos × oficial seleciona" virou dois estágios (Ciência × Designação); supressão definida como reversível e nunca aplicada ao Centro de Controle.

### Nível 2 — Components (5 perguntas)
- Decomposição por **carga e criticidade** (3 serviços) escolhida contra monólito modular e microserviços por capacidade — todas as opções com defesa real (regra da casa: sem espantalhos).
- Debate do broker: a preocupação da equipe ("e se o receptor estiver com rede oscilando?") derrubou a proposta inicial de descida síncrona e levou à formulação "estado de entrega pertence ao serviço de Emergência" + cluster dedicado.
- Tecnologias consolidadas mapeadas ao problema: MQTT (QoS, sessões persistentes) na borda; Kafka no miolo, justificado por requisito de dados confirmado pela equipe (pesquisa/conservação).
- Preocupação da equipe com o Core "fazendo tudo" → monólito modular com schema por módulo e proibição de leitura cruzada.

### Nível 3 — Interactions (4 perguntas)
- **Momento-chave da colaboração**: o LLM propôs agregação automática de relatos ("Presença"); a equipe contrapropôs o **modelo Waze** (confirmação humana) — mais simples e mais preciso. O design final é da equipe, não do LLM.
- Distribuição de estado por broadcast retained (dimensionamento honesto venceu particionamento prematuro).
- Princípio cunhado pela equipe: **"o cliente é a primeira linha, não a única"** (avaliação local para agir, servidor como verdade).
- **Segunda correção da equipe**: o "recebi" manual foi questionado e morto — substituído por ack de máquina (Alcançabilidade) + ato humano único (Compromisso). O ADR 0003 foi emendado em sessão.

### Nível 4 — Contracts (4 perguntas)
- Formato por fluxo: a equipe rejeitou Protobuf uniforme; o LLM criticou a própria recomendação ao notar que a fronteira do híbrido coincide com a fronteira de carga do ADR 0002. Decisão conjunta com duas guardas anti-apodrecimento.
- Tópicos, payloads e as 6 regras de contrato aprovados em duas levas (borda, miolo).
- Armazenamento fechado com argumento da equipe: "aproveita o que já temos do Kafka".

### Nível 5 — Entregáveis
- Critérios de qualidade definidos pelo grupo (5), com a tríade modularidade/escalabilidade/desempenho mapeada explicitamente após questionamento da equipe.

## Artefatos produzidos

| Artefato | Onde |
|---|---|
| Glossário canônico (14 termos) | [`CONTEXT.md`](../CONTEXT.md) |
| 9 ADRs com alternativas rejeitadas | [`docs/adr/`](./adr/) |
| Documento de arquitetura + diagramas | [`docs/arquitetura.md`](./arquitetura.md) |
| Avaliação por cenários (5 critérios do grupo) | [`docs/avaliacao-qualidade.md`](./avaliacao-qualidade.md) |
| Notas para o documento de opinião | [`docs/notas-comparativo.md`](./notas-comparativo.md) |

## O que o processo mudou (resumo honesto)

Três decisões do LLM foram **revertidas ou refinadas por intervenção humana** (Presença→Waze, recebi→Alcançabilidade/Compromisso, Protobuf uniforme→híbrido); duas contribuições da equipe **entraram no design sem terem sido propostas pelo LLM** (adaptatividade por zonas, saída de rota); e nenhuma linha de contrato foi escrita antes do nível correspondente fechar. O checkpoint por nível funcionou como o artigo descreve: desacordos custaram minutos, não retrabalho.
