# BNP Wildlife App

Sistema do Bainomugisha Nature Park para rastrear vida selvagem, informar visitantes e garantir segurança no parque. Cobre o app do visitante, o console do centro de controle e o app da equipe (guias e médicos).

## Language

**Visitante**:
Pessoa com conta no app, telefone registrado e vinculada à placa de um carro dentro do parque.
_Avoid_: usuário, turista, cliente

**Carro**:
Veículo identificado pela placa, vinculado a um ou mais visitantes. É a unidade contada pelo controle de aglomeração.
_Avoid_: veículo, safari-car

**Spotting**:
Entidade compartilhada que representa um animal avistado: criada pelo primeiro relato (espécie, hora, localização GPS), validada por Confirmações de outros visitantes (modelo Waze). Ciclo de vida: ativo → suprimido (reversível) → expirado. Ancora a Zona de Interesse e a Supressão.
_Avoid_: avistamento, sighting, report, presença

**Confirmação**:
Ato de um visitante validar um Spotting ativo ("ainda está aqui": renova o frescor e incrementa o contador) ou negá-lo ("não está mais aqui": acelera a expiração). A deduplicação é humana — quem vê o animal decide se é o mesmo; não há agregação automática no servidor.
_Avoid_: validação, like, upvote

**Última Posição Conhecida**:
A posição mais recente que o sistema recebeu de um carro ou membro da equipe, com o respectivo horário. Pode estar defasada quando o emissor está sem conexão.
_Avoid_: posição atual, localização em tempo real

**Supressão**:
Estado reversível de um spotting que excedeu o limiar de carros na proximidade: deixa de aparecer no mapa e de gerar notificações para visitantes até a contagem baixar. Nunca se aplica ao Centro de Controle. O limiar é configurável pelo parque (por espécie ou zona).
_Avoid_: bloqueio, remoção, apagamento

**Rota**:
Trajeto pré-definido pelo parque que os carros devem seguir. Sair da rota gera alerta ao Centro de Controle. Premissa: o BNP possui as rotas mapeadas digitalmente.
_Avoid_: trilha, caminho, percurso

**Zona de Interesse**:
Área criada dinamicamente ao redor de um spotting recente ou emergência ativa, na qual os apps elevam a frequência de relato de localização. Definida pelo parque, não pelo app.
_Avoid_: geofence, hotspot

**Centro de Controle**:
Posto operado por oficiais do parque que monitora emergências e seleciona quem as atende.
_Avoid_: central, dispatch

**Equipe**:
Guias e equipe médica do parque, candidatos a atender emergências.
_Avoid_: staff, funcionários

**Emergência**:
Pedido de socorro disparado por um visitante (botão de pânico). Tem ciclo de vida: aberta → designada → resolvida. Chega por dados, por SMS (fallback) ou fica retida no app até haver conexão.
_Avoid_: alerta, incidente, pânico (o pânico é o botão; emergência é o caso)

**Ciência**:
Notificação imediata de uma emergência a todos os guias e ao posto médico mais próximo, para conhecimento. Não obriga ninguém a se deslocar.
_Avoid_: broadcast, alerta geral

**Designação**:
Ato do oficial do Centro de Controle de escolher, por proximidade, status e Alcançabilidade, quais membros da Equipe atendem uma emergência.
_Avoid_: despacho, atribuição, dispatch

**Alcançabilidade**:
Confirmação automática, no nível de protocolo, de que uma mensagem chegou ao aparelho de um membro da Equipe — sem nenhuma interação humana. Diz que o aparelho recebeu, não que a pessoa viu. Insumo da Designação.
_Avoid_: recebi, ack manual, confirmação de leitura

**Compromisso**:
Resposta única do designado a uma Designação: "aceito" ou "não posso" (com motivo curto). É o único ato humano do protocolo de emergência; a ausência dele em X segundos aparece ao operador como "não respondeu" e dispara redesignação.
_Avoid_: aceite, recebimento
