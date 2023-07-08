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
13. O outro arquivo é um arquivo de TXT para realizar operações de RDD simples no spark

# Trabalhando com arquivo TXT simples
1. Primeiro mandei o arquivo txt para o HDFS
```
hdfs dfs -put /home/hadoop/sample.txt /arquivos/txt
```
2. Agora iniciei um novo notebook e importei o pyspark e criei um SparkContext
```
import pyspark
sc = pyspark.SparkContext()
```
3. Com o sparkContex criado, posso importar o arquivo de texto do HDFS
```
txt_file = sc.textFile("hdfs://localhost:9000/arquivos/txt/sample.txt")
txt_file.collect()
```
### Saida do processamento:
```
['Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed euismod sem sed est ultricies, a faucibus nunc condimentum. Vestibulum ac commodo justo. Nullam faucibus massa ac elit malesuada, vel varius nisi condimentum. Nulla facilisi. Praesent venenatis metus justo, id interdum ligula ullamcorper a. Mauris tempus elit ut mi facilisis, ac commodo lacus commodo. Proin id augue vel dolor aliquet fringilla. Nulla facilisi. Mauris mattis tellus eget ante eleifend, non lobortis lacus feugiat. Donec sed diam nec nisl tempus auctor. Fusce rutrum purus eu mauris efficitur feugiat. Phasellus at arcu eget dui scelerisque egestas. Nullam at posuere elit. Donec pellentesque turpis id mi dictum, ut pharetra enim imperdiet. Sed efficitur purus a nunc eleifend, ac venenatis odio efficitur. Duis suscipit mauris sapien, sed iaculis nibh feugiat sit amet. Aliquam interdum fermentum risus, sed cursus leo molestie at. Phasellus condimentum, elit in egestas congue, purus purus sagittis leo, ut luctus quam turpis at metus. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Sed sed ipsum dui. Vivamus ut est sit amet nibh cursus lacinia. Nam blandit nisl vel sem aliquet viverra. Aliquam in turpis ac turpis fringilla cursus. Aenean id augue feugiat, aliquam quam in, cursus massa. Nulla facilisi']
```
4. Agora eu dividi o texto acima em um vetor, para realizar essa tarefa, usei a função split(" ") para poder criar um vetor. A função flatmap realiza o processamento sem alterar alterar a estrutura.
```
vetorizacao = txt_file.flatMap(lambda line: line.split(" "))
vetorizacao.collect()
```
### Saida do processamento
```
['Lorem',
 'ipsum',
 'dolor',
 'sit',
 'amet,',
 'consectetur',
 'adipiscing',
 'elit.',
 'Sed',
 'euismod',
 'sem',
 'sed',
 'est',
 'ultricies,', ....
```
5. Com o texto vetorizado, podemos percorrer cada uma das posições do texto e criar um index de 1 para cada posição, formando uma estrutura (chave,valor) para cada um dos elementos do vetor
```
contador = vetorizacao.map(lambda x: (x,1))
contador.collect()
```
### Saida do processamento
```
[('Lorem', 1),
 ('ipsum', 1),
 ('dolor', 1),
 ('sit', 1),
 ('amet,', 1),
 ('consectetur', 1), .....
```
6. Com o conjunto (chave.valor) criado, podemos reduzir as chaves iguais para apenas uma e tratar os valores, no nosso caso, somamos os valores. Para realizar isso usamos o método reduceByKey
```
wordCount = contador.reduceByKey(lambda x,y: x+y)
wordCount.collect()
```
### Saida do processamento
```
[('Lorem', 1),
 ('ipsum', 2),
 ('consectetur', 1),
 ('elit.', 2),
 ('Sed', 3),
 ('sed', 5), ....
```
7. Agora temos uma lista contada de palavras, na qual podemos aplicar ainda mais operações. Aqui nesse exemplo apliquei um filtro buscando as palavras que possuiam mais de 3 aparições.
```
palavrasMaisPresentes = wordCount.filter(lambda linha: linha[1]>3)
palavrasMaisPresentes.collect()
```
### Resultado do processamento
```
[('sed', 5), ('purus', 4), ('ac', 6), ('id', 4), ('ut', 4), ('turpis', 5)]
```
