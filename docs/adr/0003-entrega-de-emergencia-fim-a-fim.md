# Entrega de emergência: caminho dedicado com confirmação humana fim-a-fim

A emergência tem duas pernas com necessidades opostas: na subida (visitante → sistema) importa o ack imediato — o app só considera o pânico enviado ao receber confirmação síncrona, senão escala para SMS; na descida (sistema → Equipe) importa chegar, custe o tempo que custar — o receptor com rede oscilante é o caso normal. Decidimos: a emergência é persistida no serviço de Emergência no instante em que chega (ela é dado mestre, não mensagem em trânsito); a descida usa um cluster de mensageria **dedicado** (separado do broker de massa, coerente com o ADR 0002); e a confirmação tem **duas camadas, cada uma garantindo só o que é capaz de garantir**:

- **Ack de máquina (automático, sem interação):** o protocolo confirma que a mensagem chegou ao aparelho. Basta para a Ciência — o operador vê a Alcançabilidade de toda a Equipe ("12 alcançáveis, 3 incomunicáveis") sem fadiga de ack. Sem ack de máquina, o serviço reenvia e escala para SMS após N tentativas.
- **Resposta humana (um único ato, só para designados):** o designado responde "aceito" ou "não posso" — o Compromisso, a única coisa que máquina nenhuma confirma. Sem resposta em X segundos, o console mostra "não respondeu" e o operador redesigna.

Durabilidade de transporte não é garantia de entrega: nenhum broker faz o celular sem sinal receber, e nenhum QoS confirma que o guia *viu*. Por isso o estado de entrega pertence ao serviço de Emergência, qualquer que seja o transporte. Um "recebi" manual foi considerado e descartado: para os não-designados não há o que aceitar (o ack de máquina já responde "quem está alcançável"), e para os designados seria um toque redundante antes do "aceito".

## Considered Options

- **Broker único em HA com filas de prioridade**: uma só tecnologia e durabilidade de fábrica; rejeitado porque o caminho crítico passaria a compartilhar destino com a tempestade de telemetria, e o rastreamento de confirmação humana teria que ser construído de qualquer forma.
- **Caminho direto síncrono também na descida**: ack imediato ponta a ponta; rejeitado porque a oscilação de rede dos receptores exige reentrega durável, não falha rápida.
