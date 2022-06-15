# MATF42 - Processamento Massivo Paralelo

## Tutorial Docker + Jupyter + Scala no Windows

### Grupo: Diego, Gilson, Gregori, Luiz e Tain

# Requisitos para o Tutorial

Sua máquina Windows deve atender aos seguintes requisitos para instalar o Docker Desktop com êxito:

### WSL 2 backend

* Windows 11 64-bit: Pro versão 21H2 ou superior, ou Enterprise ou Education verssão 21H2 ou superior.
* Windows 10 64-bit: Home ou Pro 21H1 (build 19043) ou superior, ou Enterprise ou Education 20H2 (build 19042) ou
  superior.
* Habilitado o recurso WSL 2 no Windows. Para obter instruções detalhadas,
  consulte [Microsoft documentation](https://docs.microsoft.com/en-us/windows/wsl/install).
* Os seguintes pré-requisitos de hardware são necessários para executar com êxito o WSL 2 no Windows 10 ou Windows 11:
    - processador 64-bit com Tradução de Endereço de segundo nível (SLAT)
    - 4GB RAM
    - Suporte à virtualização de hardware no nível da BIOS, deve ser ativado nas configurações da BIOS. Para mais
      informações
      acesse [Virtualization](https://docs.docker.com/desktop/windows/troubleshoot/#virtualization-must-be-enabled).
* Baixar e instalar
  o [Linux kernel update package](https://docs.microsoft.com/pt-br/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)
  .

### Hyper-V backend and Windows containers

* Windows 11 64-bit: Pro versão 21H2 ou superior, ou Enterprise ou Education verssão 21H2 ou superior.
* Windows 10 64-bit: Home ou Pro 21H1 (build 19043) ou superior, ou Enterprise ou Education 20H2 (build 19042) ou
  superior.

Para Windows 10 e Windows 11 Home, consulte os requisitos de sistema no **WSL 2 backend**.

* Os recursos do Hyper-V e Containers Windows devem estar habilitados.
* Os seguintes pré-requisitos de hardware são necessários para executar com êxito o Client Hyper-V no Windows 10:
    - processador 64-bit com Tradução de Endereço de segundo nível (SLAT)
    - 4GB RAM
    - Suporte à virtualização de hardware no nível da BIOS, deve ser ativado nas configurações da BIOS. Para mais
      informações
      acesse [Virtualization](https://docs.docker.com/desktop/windows/troubleshoot/#virtualization-must-be-enabled).

# Passo 1:

Faça do download do Docker Desktop em:

- https://docs.docker.com/desktop/windows/install/

Clicando no link acima será iniciado o download do arquivo **Docker Desktop Installer.exe**, após o fim do download do
arquivo execute-o para dá inicio a instalação.

# Passo 2:

Após iniciar a instalação do Docker será exibido a tela abaixo, clique em **OK** para dá inicio ao processo de
instalação.

![p1.jpg](imagens/p1.jpg)

![p2.jpg](imagens/p2.jpg)

![p3.jpg](imagens/p3.jpg)

Clique no botão **Close and restart** para reiniciar o Windows e completar a instalação do Docker.

![p4.jpg](imagens/p4.jpg)

Na tela seguinte é preciso aceitar os termos selecionando a opção **I accept the terms** e clicar no botão **Accept**.

![p5.jpg](imagens/p5.jpg)

Após esse passo teremos nosso Docker Desktop instalado.

![p6.jpg](imagens/p6.jpg)

Após o Docker instalado, é necessário reiniciar o Windows.

# Passo 3:

Após a instalação o Docker Desktop solicitará a instalação do WSL 2.

![p7.jpg](imagens/p7.jpg)

Basta clicar sempre em **Next** para proseguir com a instalação e ao final clicar em **Finish**.

![p8.jpg](imagens/p8.jpg)

![p9.jpg](imagens/p10.jpg)

Após o Docker Desktop instalado, para testar se tudo ocorreu bem abra o terminal do Windows e execute o comando abaixo:

<code>docker run -d -p 80:80 docker/getting-started</code>

![p11.jpg](imagens/p11.jpg)

Após o finalizar o processo no terminal abra o navegador com o [link](http://localhost).

![p12.jpg](imagens/p12.jpg)

Caso tudo ocorra bem será apresentada a página **Getting Started** do Docker.
Agora precisamos para o container usado para teste com os comandos abaixo.

Para verificar os container que está rodando execute o comando abaixo e pegue o **CONTAINER ID**.

<code>docker ps</code>

Com o conteiner id em mãos execute o comando abaixo para parar a execução do container.

<code>docker stop CONTAIER_ID</code>

![p13.jpg](imagens/p13.jpg)

# Passo 4:

Configuração do Docker Compose.

É preciso criar o arquivo docker-compose.yml com o código abaixo. Crie uma diretório e coloque o arquivo
docker-compose.yml dentro desse diretório.

<code>
version: '3'
services:
  jupyter-notebook:
    image: jupyter/all-spark-notebook
    ports:
      - "8888:8888"
      - "4040:4040"
    volumes:
      - ./work/:/home/jovyan/work
</code>


Docker Compose é o orquestrador de containers da Docker, é escrito em YAML (acrônimo recursivo para YAML Ain’t Markup
Language) é um formato de codificação de dados legíveis por humanos, o que torna fácil de ler e entender o que um
Compose faz!

No nosso caso, estamos rodando o Jupyter dentro de um container, então no nosso docker compose, configuramos o endereço
da imagem, a informação das portas e volume da nossa imagem.

Após o Docker Desktop baixado e o compose configurado, vamos até a pasta onde está nosso arquivo do docker-compose e
executamos o seguinte comando:

<code>docker-compose up</code>

Com esse comando baixamos a imagem do jupyter e inicializamos nosso container, após isso conseguimos acessar nosso
ambiente já dentro do container através do que é apresentado no fim lá do terminal.

![p14.jpg](imagens/p14.jpg)

![p15.jpg](imagens/p15.jpg)

Acessando o link acima teremos a seguinte página do Jupyter.

![p16.jpg](imagens/p16.jpg)

# Exemplo de Notebook Jupyter com Spark e Scala

Este notebook tem um exemplo de aplicação Spark feita em Scala no notebook jupyter com o kernel spylon através da imagem
docker jupyter/all-spark-notebook. Para mais detalhes, ver o arquivo de instalação presente neste repositório.

Esta aplicação irá gerar um modelo de floresta aleatória para prever o time vencedor em uma partida de League of
Legends.
Os dados são obtidos através da API oficial da Riot Games, e foram compilados no kaggle
neste [link](https://www.kaggle.com/datasets/datasnaek/league-of-legends) pelo
usuário [Mitchel J](https://www.kaggle.com/datasnaek).

```scala
## Importar as bibliotecas necessárias
```

## Definir a SparkSession

```scala
// Para definir um cluster externo master,
// basta trocar "local[*]" por "spark://<host>:<port>"
// com os dados de host e port do master.
val spark = SparkSession.builder.master("local[*]").getOrCreate
```

## Carregar dados dos jogos

```scala
val gamesDF = spark.read
  .option("header", value = true)
  .option("inferSchema", value = true)
  .csv("games.csv")
  .persist()
```

## Analisar schema e dados no dataframe

```scala
gamesDF.printSchema
```

```scala
gamesDF.show(5)
```

## Separar em dados de treino e teste

```scala
val seed = 11011990
val Array(dataTrain, dataTest) = gamesDF.randomSplit(Array(0.8, 0.2), seed)
```

## Filtrar colunas relevantes ao modelo

```scala
val columns = gamesDF.columns.filter(
  !Array("gameId", "creationTime", "seasonId", "winner").contains(_)
)
```

## Gerar assembler para criar vetor de features com as colunas desejadas

```scala
val assembler = new VectorAssembler()
  .setInputCols(columns)
  .setOutputCol("features")
```

## Indexador para definir a coluna interesse

```scala
val indexer = new StringIndexer()
  .setInputCol("winner")
  .setOutputCol("label")
```

## Definindo o classificador de Floresta Aleatória

```scala
val randomForestClassifier = new RandomForestClassifier()
  .setImpurity("gini")
  .setMaxDepth(3)
  .setNumTrees(20)
  .setFeatureSubsetStrategy("auto")
```

## Definindo o pipeline de operações

```scala
val stages = Array(assembler, indexer, randomForestClassifier)
val pipeline = new Pipeline().setStages(stages)
```

## Definindo o avaliador que será usado na validação cruzada

```scala
val evaluator = new BinaryClassificationEvaluator()
  .setLabelCol("label")
  .setMetricName("areaUnderROC")
```

## Definindo a matriz de parâmetros que serão modificados ao se realizar várias predições. O validador cruzado irá escolher o conjunto de parâmetros com a melhor predição, de acordo com o classificador definido previamente.

```scala
val paramGrid = new ParamGridBuilder()
  .addGrid(randomForestClassifier.maxBins, Array(25, 28, 31))
  .addGrid(randomForestClassifier.maxDepth, Array(4, 6, 8))
  .addGrid(randomForestClassifier.impurity, Array("entropy", "gini"))
  .build()
```

## Definindo o validador cruzado

```scala
val cv = new CrossValidator()
  .setEstimator(pipeline)
  .setEvaluator(evaluator)
  .setEstimatorParamMaps(paramGrid)
  .setNumFolds(5)
```

## Treinando o modelo

```scala
val cvModel: CrossValidatorModel = cv.fit(dataTrain)
```

## Avaliando a previsão

```scala
val cvPredictionDf = cvModel.transform(dataTest)
cvPredictionDf.show
```

## Salvando o modelo para poder ser utilizado depois em novas previsões

```scala
cvModel.write.overwrite().save("model/lol")

```

```scala
cvModel.write.overwrite().save("model/lol")

```

## Definir a SparkSession

```scala
// Para definir um cluster externo master,
// basta trocar "local[*]" por "spark://<host>:<port>"
// com os dados de host e port do master.
val spark = SparkSession.builder.master("local[*]").getOrCreate
```

## Carregar dados dos jogos

```scala
val gamesDF = spark.read
  .option("header", value = true)
  .option("inferSchema", value = true)
  .csv("games.csv")
  .persist()
```

## Analisar schema e dados no dataframe

```scala
gamesDF.printSchema
```

```scala
gamesDF.show(5)
```

## Separar em dados de treino e teste

```scala
val seed = 11011990
val Array(dataTrain, dataTest) = gamesDF.randomSplit(Array(0.8, 0.2), seed)
```

## Filtrar colunas relevantes ao modelo

```scala
val columns = gamesDF.columns.filter(
  !Array("gameId", "creationTime", "seasonId", "winner").contains(_)
)
```

## Gerar assembler para criar vetor de features com as colunas desejadas

```scala
val assembler = new VectorAssembler()
  .setInputCols(columns)
  .setOutputCol("features")
```

## Indexador para definir a coluna interesse

```scala
val indexer = new StringIndexer()
  .setInputCol("winner")
  .setOutputCol("label")
```

## Definindo o classificador de Floresta Aleatória

```scala
val randomForestClassifier = new RandomForestClassifier()
  .setImpurity("gini")
  .setMaxDepth(3)
  .setNumTrees(20)
  .setFeatureSubsetStrategy("auto")
```

## Definindo o pipeline de operações

```scala
val stages = Array(assembler, indexer, randomForestClassifier)
val pipeline = new Pipeline().setStages(stages)
```

## Definindo o avaliador que será usado na validação cruzada

```scala
val evaluator = new BinaryClassificationEvaluator()
  .setLabelCol("label")
  .setMetricName("areaUnderROC")
```

## Definindo a matriz de parâmetros que serão modificados ao se realizar várias predições. O validador cruzado irá escolher o conjunto de parâmetros com a melhor predição, de acordo com o classificador definido previamente.

```scala
val paramGrid = new ParamGridBuilder()
  .addGrid(randomForestClassifier.maxBins, Array(25, 28, 31))
  .addGrid(randomForestClassifier.maxDepth, Array(4, 6, 8))
  .addGrid(randomForestClassifier.impurity, Array("entropy", "gini"))
  .build()
```

## Definindo o validador cruzado

```scala
val cv = new CrossValidator()
  .setEstimator(pipeline)
  .setEvaluator(evaluator)
  .setEstimatorParamMaps(paramGrid)
  .setNumFolds(5)
```

## Treinando o modelo

```scala
val cvModel: CrossValidatorModel = cv.fit(dataTrain)
```

## Avaliando a previsão

```scala
val cvPredictionDf = cvModel.transform(dataTest)
cvPredictionDf.show
```

## Salvando o modelo para poder ser utilizado depois em novas previsões

```scala
cvModel.write.overwrite().save("model/lol")

```

```scala
cvModel.write.overwrite().save("model/lol")
```

# Referências

Documentação Docker: https://docs.docker.com/desktop/windows/install/

Documentação Docker: https://docs.docker.com/desktop/windows/troubleshoot/#virtualization-must-be-enabled

Documentação Microsoft: https://docs.microsoft.com/en-us/windows/wsl/install

Documentação
Microsoft: https://docs.microsoft.com/pt-br/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package
