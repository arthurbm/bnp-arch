# Rastreamento contínuo e adaptativo de localização

O controle de aglomeração precisa contar carros perto de animais e a designação de emergência precisa da proximidade da Equipe — ambos exigem que o sistema conheça posições continuamente, não apenas quando alguém reporta algo. Decidimos que os apps reportam localização de forma contínua e **adaptativa**: um piso mínimo de frequência em todo o parque (garante Última Posição Conhecida útil para emergências) e frequência elevada dentro de Zonas de Interesse, criadas dinamicamente ao redor de spottings recentes e emergências ativas. A política de frequência é ditada pelo servidor, que é quem conhece as zonas. Como a cobertura de rede no parque é instável, o app armazena posições localmente e envia quando há conexão (store-and-forward).

## Considered Options

- **Localização apenas em eventos** (spotting/pânico): mais simples e mais privado, mas torna a contagem de carros uma estimativa cega (carros que não reportam nada ficam invisíveis) e degrada o despacho por proximidade.
- **Frequência fixa para todos**: previsível, mas gasta bateria e rede onde a precisão não importa e entrega contagem fraca exatamente onde importa.

## Consequences

- Sair da Rota torna-se detectável e gera alerta ao Centro de Controle (extensão além do enunciado, decidida pelo grupo). Premissa explícita: o BNP possui as rotas mapeadas digitalmente.
- O consentimento de rastreamento passa a fazer parte do onboarding.
