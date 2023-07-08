# Referencia
1. Esse projeto é desenvolvido pelo ProjectPro.io
2. Os dados utilizados são de responsabilidade da plataforma, por isso não serão indexados aqui, apenas o resultado do processamento.

# Conhecendo os Dados
1. Ao todo o dataset contem 2 milhões de registros de voos
2. Existem colunas que indentificam o tipo de companhia (Charter, Low cost e scheduler)
3. As companhias podem ser dividas em domesticas e internacionais
4. Possui o aeroporto de origem e o de destino, as escalas contam como destino e origem.
5. Possui hora de partida (Departure Time) e hora de chegada (Arrival time)
6. Possui hora de partida e de chegada CRS, que é uma métrica para saber se ele esta no horario (quando o voos está a menos de 15 minutos do programado no sistema de reserva), esses campos são indentificados como CRS Departure Time e CRS Arrival Time
7. Existe colunas para atrasos de chegada (Arrival Delay) e atraso de partida (Departure Delay). Isso é especialmente importante, pois se houver um atraso, a alocação de slots no aeroporto é feita para qual voo foi programado primeiro.
8. Existe uma coluna que indica se o atraso ou cancelamento foi por motivo da operadora do avião (Air Carrier)
9. Caso ocorra atraso por causa do tempo, essa indicação é feita na coluna (Weather Delay)
10. Existe uma forma de atraso/cancelamento de voos que é indicado como NAS, esses atrasos ocorrem por problemas climáticos que poderiam ser mitigados pelos aeroportos. Essa informação está indicada como (NAS delay)
11. A coluna (Distance) define 3 tipos de distancias:
    shorter -> Menor que 600 e 800 milhas nauticas
    long-haul -> Mais que 2100-2600 milhas nauticas
    medium-haul -> Estando entre os 2 anteriores
12. A coluna (Security Delay) indica atrasos que por motivos de segurança (evacuações de emergencia e volta a aeronave) e motivos de equipamento (Inoperancia) e filas maiores que 29 minutos na triagem.
