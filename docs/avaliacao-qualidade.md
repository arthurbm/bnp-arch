# Avaliação de qualidade da arquitetura — BNP Wildlife App

Critérios definidos pelo grupo, avaliados por **cenários concretos** (mini-ATAM): cada critério declara os atributos de qualidade clássicos que cobre, propõe cenários "acontece X → o sistema deve Y" e verifica como o design responde, com o ADR como evidência. A seção final consolida os trade-offs aceitos — porque avaliação sem trade-off declarado é propaganda.

## Mapa critérios × atributos clássicos

| Critério do grupo | Atributos cobertos (tríade e além) |
|---|---|
| 1. Disponibilidade do caminho crítico | Disponibilidade, confiabilidade, **desempenho** (latência do ack) |
| 2. Tolerância à rede real do parque | Confiabilidade, **desempenho sob degradação**, resiliência |
| 3. Evolutibilidade | **Modularidade**, manutenibilidade, **escalabilidade** (crescimento) |
| 4. Simplicidade operacional | Operabilidade, custo de propriedade |
| 5. Privacidade & segurança | Segurança, conformidade |

## Critério 1 — Disponibilidade do caminho crítico

**Cenário 1.1** — *Sábado de pico: o parque inteiro fotografa um leão, o broker de massa degrada sob carga. Um visitante aperta o pânico.*
O pânico não passa pelo broker de massa: viaja pelo cluster MQTT dedicado até o serviço de Emergência, que tem banco próprio (ADRs 0002, 0003, 0009). A tempestade de telemetria e o socorro não compartilham nenhum componente. **Atende.**

**Cenário 1.2 (desempenho)** — *O ack do pânico deve chegar ao visitante em ~5s mesmo em pico.*
O caminho do ack atravessa só o cluster dedicado (ocioso por definição — emergências são raras) e uma escrita no banco pequeno da Emergência. Não há fila compartilhada onde o pânico espere atrás de posições. **Atende por construção.**

**Cenário 1.3** — *O próprio serviço de Emergência cai.*
Risco residual honesto: o serviço é singular. Mitigação: instância(s) redundante(s) + o fallback SMS do app dispara após o timeout — o canal degradado não depende de nenhum componente nosso. **Atende com mitigação declarada.**

## Critério 2 — Tolerância à rede real do parque

**Cenário 2.1** — *Carro fica 40 minutos sem sinal numa área remota.*
O app continua: mapa com último estado conhecido (tópico retained), avisos de Rota e frequência adaptativa avaliados localmente (política está no bolso — princípio "primeira linha"), posições acumuladas em buffer. Na reconexão: sessão persistente do MQTT entrega o que desceu; o buffer sobe com timestamps **da medição**, então a história fica íntegra. O Centro de Controle manteve a Última Posição Conhecida com horário honesto o tempo todo. **Perde-se apenas frescor, nunca dados — e a defasagem é visível, não silenciosa.**

**Cenário 2.2** — *Guia designado está com rede oscilando.*
QoS 1 + sessão persistente reentregam no nível de protocolo; sem ack de máquina o serviço escala para SMS; sem Compromisso em X segundos o operador vê "não respondeu" e redesigna. A oscilação vira **estado visível no console**, não risco silencioso (ADR 0003). **Atende.**

**Cenário 2.3 (desempenho)** — *Promessa de frescor: spotting no mapa em segundos–minuto.*
Caminho: MQTT → Kafka → Core → retained state. Todos os saltos são push; o gargalo honesto é a rede do próprio visitante — exatamente o que a promessa "quase-tempo-real **com entrega eventual**" reconhece. **Atende à promessa como formulada.**

## Critério 3 — Evolutibilidade (modularidade + escalabilidade)

**Cenário 3.1 (modularidade)** — *O parque muda a regra de aglomeração (limiar por horário, exceções por espécie).*
Muda **um** módulo (Spottings & Aglomeração, no Core). Telemetria intocada — ela não conhece Supressão (ADR 0005). Deploy no serviço barato, não no funil de carga. **1 componente afetado.**

**Cenário 3.2 (escalabilidade)** — *O parque dobra de visitantes.*
O fluxo que dobra é a telemetria — exatamente o serviço isolado por carga (ADR 0002), escalável horizontalmente (Redis particionável, Kafka particionado por deviceId). O Core mal sente: dezenas de spottings/hora viram centenas. **A fronteira foi desenhada onde a escala acontece.**

**Cenário 3.3** — *O parque quer análises novas (trilhas de animais, padrões sazonais).*
`telemetry.positions` com retenção longa permite materializar qualquer vista nova por replay — inclusive a base PostGIS descartada no ADR 0009, se fizer falta. **Evolução sem refazer nada** (ADR 0004).

**Cenário 3.4** — *Assinaturas crescem a ponto de merecer serviço próprio.*
Schema próprio + eventos já no Kafka = extração barata (ADR 0006). **Caminho preparado.**

## Critério 4 — Simplicidade operacional

**Cenário 4.1** — *Uma TI pequena opera isso?*
Inventário: 3 serviços, 2 clusters MQTT, Kafka, 2 Postgres, 1 Redis, 2 integrações externas. É **mais** que um monólito — custo pago conscientemente por isolamento de falha e de carga (ADR 0002). Mitigações estruturais: só 2 tecnologias de banco, JSON em todo fluxo depurável-no-olho (Protobuf confinado a 1 mensagem estável — ADR 0008), e o número de serviços travado em 3 pelo critério "fronteira nova só com número que a justifique". **Atende com custo declarado** — e foi este critério que vetou os microserviços por capacidade e quase vetou o Redis.

## Critério 5 — Privacidade & segurança

**Cenário 5.1** — *Visitante questiona o rastreamento contínuo.*
Consentimento explícito no onboarding (ADR 0001); dado quente identificado vive dias e serve a segurança dele próprio (emergência); histórico frio é pseudonimizado (viagem, não pessoa) antes de virar ativo de pesquisa. **Atende.**

**Cenário 5.2** — *App de visitante comprometido tenta espionar a operação.*
ACL por identidade no broker: visitante não assina `emg/down/#` nem tópicos de outros; a visão não suprimida só existe na API do Console (autenticada). Supressão por **ausência no canal público** — não há dado para vazar por bug de filtro no cliente. Três bases de código eliminam por construção o endpoint que vaza pelo bundle errado. **Atende.**

**Risco residual:** spotting falso malicioso (alguém inventa um leão). Mitigação parcial pelo modelo Waze (negações aceleram expiração); moderação ativa ficou fora do escopo — registrado como limitação conhecida.

## Trade-offs aceitos (consolidado)

| Decisão | Ganhamos | Pagamos | Reversão |
|---|---|---|---|
| 3 serviços, não monólito (ADR 0002) | Isolamento de falha/carga do caminho crítico | Falhas parciais, contratos versionados | Cara |
| Sem agregação automática de spottings (ADR 0007) | Zero heurística errando supressão | Dependência da cooperação humana | Barata (heurística pode ser adicionada depois) |
| Kafka, não RabbitMQ (ADR 0004) | Replay + histórico como ativo de pesquisa | Peso operacional maior | Cara |
| Híbrido Protobuf/JSON (ADR 0008) | Bytes onde dói, legibilidade onde itera | Dois ferramentais (com guardas) | Média |
| Redis, não PostGIS na Telemetria (ADR 0009) | Store casado com natureza efêmera do dado | Geometria em código próprio; +1 tecnologia | Barata (replay do Kafka materializa PostGIS) |
| Broadcast, não tópicos regionais | Simplicidade + mapa completo offline | Bytes desnecessários a cada app | Barata (migração incremental por células) |

**Síntese do grupo:** a arquitetura concentra seu orçamento de complexidade nos dois pontos onde o enunciado não perdoa — o caminho de emergência e a rede instável — e compra simplicidade em todo o resto. Os cenários mostram os atributos da tríade (modularidade nos cenários 3.1/3.4, escalabilidade em 3.2, desempenho em 1.2/2.3) atendidos por decisões rastreáveis a ADRs, com riscos residuais e limitações declarados em vez de escondidos.
