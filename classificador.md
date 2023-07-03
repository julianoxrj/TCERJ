---
title: "Projeto de elaboração de modelo para classificação automática de editais públicos"
output:
    html_document:
      hightlight: espresso
      toc: yes
      toc_float: yes
      collapsed: yes
---

```{r message=FALSE, warning=FALSE}
library('tidyverse')
library('tm')
library('textrecipes')
library('tidytext')
library('readxl')
library('glmnet')
library('tidymodels')
library('kableExtra')
```

# Introdução

Os tribunais de contas são órgãos de controle externo vinculados ao poder legislativo responsáveis pela fiscalização da execução orçamentária e financeira do Estado e por contribuir com o aperfeiçoamento da Administração Pública em benefício da sociedade. A Secretaria-Geral de Controle Externo (SGE) do Tribunal de Contas do Estado do Rio de Janeiro (TCE-RJ) tem em sua estrutura uma metodologia de trabalho baseada em políticas públicas, na qual cada uma de suas coordenadorias é responsável por um campo de atuação específico como saúde, saneamento e mobilidade urbana, além de uma visão orientada à avaliação de políticas públicas.

Uma das atividades essenciais dos tribunais é o controle de editais de licitação, que são instrumentos utilizados para tornar pública a realização de um processo licitatório. A licitação é um procedimento pelo qual a Administração Pública contrata serviços ou adquire produtos. De acordo com a legislação de licitações, cada licitação requer a publicação de um edital contendo informações sobre o processo, permitindo que as empresas avaliem se são elegíveis para participar e se possuem expertise para atender à demanda. Esses editais são disponibilizados em meios de informação acessíveis ao público, como sites governamentais ou jornais.

# 2 Objetivo

O objetivo deste projeto é automatizar a distribuição dos editais cadastrados às suas respectivas coordenadorias. Para alcançar esse objetivo, será adotada uma abordagem baseada em técnicas de aprendizado de máquina para a classificação automática dos editais. Isso resultará em economia de tempo e redução da necessidade de classificação manual por parte dos servidores.

Será desenvolvido um classificador automático de editais utilizando técnicas de aprendizado de máquina. O foco será na automatização do processo de classificação, tratando o problema como uma tarefa de classificação de documentos. Dessa forma, será possível aplicar técnicas de aprendizado de máquina em dados textuais.

O projeto incluirá a implementação e teste de técnicas e algoritmos baseados em Regressão Logística. Será realizado o treinamento e a avaliação dessas técnicas utilizando um conjunto de editais pré-classificados provenientes do Tribunal de Contas.

# 3 Teorias e Médotos

## 3.1 Processamento de Linguagem natural

A área de Processamento de Linguagem Natural (NLP) situa-se no âmbito da ciência da computação e inteligência artificial, dedicando-se à interação entre computadores e linguagem humana. Seu objetivo é capacitar as máquinas a compreender, interpretar e gerar texto e fala de maneira semelhante aos seres humanos.

O NLP abrange um conjunto diversificado de técnicas e algoritmos que permitem aos computadores processar e analisar dados linguísticos em formato natural, abrangendo palavras, frases e textos completos. Essa área inclui diversas tarefas, como reconhecimento de fala, compreensão de linguagem, tradução automática, geração de texto, análise de sentimentos, sumarização de texto, extração de informações, entre outras.

## 3.2 Mineração de texto

A mineração de texto é um processo essencial para obter informações valiosas e insights acionáveis a partir de dados textuais. Ela se baseia no uso de técnicas de processamento de linguagem natural (NLP), recuperação de informações e aprendizado de máquina para analisar dados não estruturados e transformá-los em uma forma mais organizada, permitindo a identificação de padrões e insights úteis para os usuários.

A análise de texto abrange um conjunto diversificado de técnicas de aprendizado de máquina, linguística e estatística, que são aplicadas para modelar e extrair informações a partir do texto, visando diferentes propósitos de análise, como inteligência de negócios, análise exploratória, descritiva e preditiva. Abaixo estão algumas das principais técnicas e operações utilizadas na análise de texto:

1.  Classificação de texto: categoriza documentos em diferentes classes ou categorias com base no seu conteúdo.

2.  Clusterização de texto: agrupa documentos semelhantes com base em características comuns, permitindo a identificação de padrões e temas.

3.  Sumarização de texto: extrai informações-chave e resumos concisos de documentos longos, facilitando a compreensão e a análise.

4.  Análise de sentimentos: determina a polaridade dos sentimentos expressos em textos, identificando se são positivos, negativos ou neutros.

5.  Extração de entidades e reconhecimento: identifica entidades nomeadas, como nomes de pessoas, locais e organizações, bem como informações relevantes, como datas e números.

6.  Análise de similaridade e modelagem de relações: mede a similaridade entre textos e identifica relações entre entidades ou conceitos.

Essas técnicas são fundamentais para explorar e compreender melhor os dados textuais, permitindo uma análise mais completa e um aproveitamento mais eficaz das informações contidas neles.

## 3.3 Aprendizado de Máquina

O Aprendizado de Máquina é uma disciplina da Inteligência Artificial que possibilita aos computadores aprenderem a partir de dados. Por meio desse processo, o computador adquire informações prévias e utiliza esse conhecimento para fazer previsões ou inferências em situações desconhecidas. O objetivo principal do Aprendizado de Máquina é aprimorar a capacidade de predição dos computadores.

O campo do Aprendizado de Máquina é caracterizado pela capacidade dos computadores de aprenderem de forma autônoma, sem a necessidade de programação explícita. Ele se baseia na análise e interpretação de dados, identificando padrões e relações que podem ser utilizados para fazer generalizações e tomar decisões informadas.

Ao treinar um modelo de Aprendizado de Máquina, são utilizados conjuntos de dados de treinamento, nos quais o computador é exposto a exemplos e é capaz de aprender a partir deles. Esse processo envolve a aplicação de algoritmos específicos que ajustam os parâmetros do modelo com base nos dados fornecidos. Uma vez treinado, o modelo pode ser usado para fazer previsões ou classificações em novos dados, utilizando o conhecimento adquirido durante o treinamento.

O Aprendizado de Máquina tem aplicações em diversas áreas, como reconhecimento de padrões, processamento de linguagem natural, visão computacional, recomendação de produtos, detecção de fraudes, entre outras. É uma abordagem poderosa que permite aos computadores adquirirem conhecimento a partir dos dados e melhorarem sua capacidade de tomada de decisão de forma automatizada.

## 3.4 Normalização de Texto.

A estratégia adotada para construir o modelo de classificação utilizou a abordagem de Processamento de Texto. Essa abordagem envolve diversas etapas de pré-processamento para preparar os dados textuais antes de alimentar o modelo.

A primeira etapa foi a Tokenização, que consiste em dividir o texto em unidades significativas chamadas de tokens, como palavras ou frases. Em seguida, foi realizada a remoção de Stopwords, que são palavras comuns e sem muito significado, para eliminar ruídos desnecessários. Além disso, foi aplicado o Stemming, que reduz as palavras flexionadas para sua forma base, facilitando a análise.

Dentro do processo de Processamento de Texto, é necessário converter os dados textuais em variáveis numéricas para que possam ser processados pelo modelo. Para isso, foi utilizado o Modelo de Saco de Palavras, também conhecido como Modelo de Espaço Vetorial. Esse modelo representa os documentos como vetores, onde cada posição do vetor representa uma palavra do vocabulário e o valor indica se a palavra está presente ou não no documento.

O TF-IDF, que significa Term Frequency-Inverse Document Frequency, é uma medida numérica que determina a importância de uma palavra em um documento em relação ao conjunto de documentos. Ele atribui pesos ou relevância às palavras com base em sua frequência no documento e sua raridade no corpus.

# 4 Pré-Processamento

A etapa de pré-processamento de dados no campo da ciência de dados refere-se à preparação e limpeza dos dados brutos antes de serem usados em análises e modelos. Essa etapa é essencial para garantir a qualidade e a confiabilidade dos dados, bem como para melhorar a eficácia das análises posteriores.

## 4.1 Organização dos dados

```{r message=FALSE, warning=FALSE,fig.width=10}

dados <- read_excel('Distribuições_dos_Editais.xlsx')


df_editais <- dados  |> 
  select( 
    cad   = `Predição Classificação`, 
    texto = Objeto)  |> 
  drop_na(cad)

textoA <- 'aquisição de material para pavimentação na rua jardins e na rua IV.'

textoB <- 'aquisição de material para construção de escola na rua da paz e na rua jardins e construção de muro.'

editais_exemplo <- 
  tibble(edital = c(1:2),texto = c(textoA, textoB), cad =c('Mobilidade','Obras'))


estilo <-   c('striped','hover','condensed','responsive')

editais_exemplo |> 
  kable() |> 
  kable_styling(bootstrap_options = estilo)

```

### 4.2 Treino e teste

Antes de construir o modelo, dividiremos os dados em um conjunto que será usado para desenvolver o modelo, pré-processar os preditores e explorar as relações entre os preditores e a resposta (o conjunto de treinamento) e outro que será o árbitro final do preditor desempenho da combinação conjunto/modelo (o conjunto de teste). Para particionar os dados, a divisão do conjunto de dados original será feita de maneira *estratificada* , fazendo divisões aleatórias em cada uma das classes de resultado. Isso manterá a proporção de coordenadorias aproximadamente a mesma. Na divisão, 70% dos dados foram alocados para o conjunto de treinamento.

```{r message=FALSE, warning=FALSE}


df_editais <- slice_sample(df_editais,n = 1000 ,by = cad)

# Divisão em treine e teste
base_split <- initial_split(df_editais, strata = cad)

editais_treino <- training(base_split)
editais_teste <- testing(base_split)



```

### 4.3 Normalização do texto.

A criação de uma função personalizada é uma abordagem eficaz para remover elementos que possam causar problemas no processamento de texto. Esses elementos problemáticos podem incluir caracteres especiais, pontuação, stopwords (palavras comuns que podem ser irrelevantes para a análise) e outros elementos indesejados. Ao criar uma função para realizar essa remoção, é possível garantir que o texto esteja limpo e pronto para as etapas subsequentes de análise de texto. Essa função pode ser personalizada de acordo com as necessidades específicas do projeto, permitindo a remoção de elementos específicos que possam interferir na qualidade dos resultados obtidos.

#### 4.3.1 Limpeza de texto

Está etapa irá remover simbolos, números e pontuções do texto, elementos que trazem pouquissa informação.

```{r}

# Criação de função para remover simobolos
limpar_texto <-\(x){
  x<-removeNumbers(x)
  x<-removePunctuation(x, ucp = TRUE)
  x <- gsub("\\b[IVXLCDM]+\\b", "", x)
  return(x)}
 
editais_exemplo$texto <- limpar_texto(editais_exemplo$texto )

editais_exemplo$texto
```

#### 4.3.2 tokenização

Para criar variáveis utilizáveis em algoritmos de aprendizado de máquina supervisionado a partir de texto, é preciso de algum método capaz de representar o texto bruto em formato numérico para que seja possível realizar a computação neles. Normalmente, uma das primeiras etapas nessa transformação de texto em variáveis é a tokenização . A Tokenização consiste em dividir o texto em unidades significativas chamadas de tokens, como frases, palavras ou até letras.

```{r}


editais_exemplo2 <- editais_exemplo   |> 
  mutate(edital = row_number()) |>
  unnest_tokens(palavra, texto) |> 
  count(edital, palavra, cad)

editais_exemplo2 |> 
  kable() |> 
  kable_styling(bootstrap_options = estilo)
```

#### 4.3.3 Remoção de Stopwords

Após realizar a tokenização do texto, fica claro que nem todas as palavras possuem a mesma relevância para uma tarefa de modelagem preditiva, se é que possuem alguma informação significativa. Essas palavras comuns, que carregam pouca ou nenhuma informação relevante, são chamadas de stopwords. É comum e recomendado remover essas palavras de parada em várias tarefas de Processamento de Linguagem Natural (PLN).

```{r message=FALSE, warning=FALSE}


editais_exemplo3 <- editais_exemplo2  |> 
  anti_join( tibble(palavra = stopwords('pt')))



editais_exemplo3 |> 
  kable() |> 
  kable_styling(bootstrap_options = estilo)


```

### 4.3.4 O Engenharia de recurso (Feature Engineering)

A Engenharia de recurso refere-se à criação e transformação de variáveis (também conhecidas como features) com o objetivo de melhorar a performance dos modelos de machine learning e análises subsequentes. Envolve a extração de informações relevantes dos dados brutos e a criação de novas variáveis que capturem melhor os padrões e relações presentes nos dados.

Uma das estruturas mais comuns com as quais os pacotes de mineração de texto trabalham é a [matriz de termos de documento](https://en.wikipedia.org/wiki/Document-term_matrix) (ou DTM). Esta é uma matriz onde:

-   cada linha representa um documento (como um livro ou artigo),

-   cada coluna representa uma palavra/termo, e

-   cada valor (normalmente) contém o número de aparições desse termo nesse documento.

```{r}


dtm_editais <- cast_dtm(editais_exemplo3, 
         term = palavra,
         document = edital, 
         value = n)

inspect(dtm_editais)

```

Ao analisarmos a matriz, podemos observar a contagem de palavras em relação a cada um dos documentos. Nessa configuração, temos variáveis preditoras representadas pelas palavras e uma variável alvo representada pelo documento em questão.

No entanto, a simples contagem da ocorrência de cada palavra não é a melhor maneira de mensurar sua importância em um determinado documento. É necessário levar em consideração a relação entre a ocorrência dessa palavra no documento em questão e a sua ocorrência em todos os outros documentos.

O TF-IDF, que significa Term Frequency-Inverse Document Frequency, é uma medida numérica que determina a importância de uma palavra em um documento em relação ao conjunto de documentos. Ele atribui pesos ou relevância às palavras com base em sua frequência no documento e sua raridade no corpus(conjunto de todos documentos).

```{r}
editais_tf_idf <- editais_exemplo2 %>%
  bind_tf_idf(palavra, edital, n)


editais_tf_idf |> 
  kable() |> 
  kable_styling(bootstrap_options = estilo)
```

Por fim, faremos o pré-processamento do conjunto de treinamento real antes de prosseguir com a modelagem. Neste processo, utilizaremos o framework tidymodel para realizar todas as etapas de pré-processamento necessárias.

```{r}

editais_rec <- recipe(cad~texto, data = editais_treino) |> 
  step_tokenize(texto,options = list(strip_numeric = TRUE)) |> 
  step_stopwords(texto,language = 'pt') |> 
  step_tokenfilter(texto, min_times = 5, max_tokens = 3000) |> 
  step_tfidf(texto) |> 
  step_normalize(all_double())

```

```{r}

editais_treino$texto <- limpar_texto(editais_treino$texto)

editais_prep <-   prep(editais_rec)
editais_bake <-  bake(editais_prep, new_data = NULL)
  
```

### 5 Construção do modelo de classificação

Para construir o modelo de classificação, será utilizada a técnica de regressão multinomial regularizada.

A regressão multinomial é uma poderosa ferramenta de modelagem estatística utilizada para prever a probabilidade de uma variável dependente categórica com mais de duas categorias, com base em um conjunto de variáveis independentes. Essa abordagem é uma extensão da regressão logística binomial, que é aplicada somente a variáveis dependentes com duas categorias.

Ao empregar a regressão multinomial regularizada, estamos adicionando uma componente de regularização ao modelo. A regularização ajuda a controlar o ajuste excessivo (overfitting) e melhora a generalização ao introduzir penalidades para os coeficientes das variáveis independentes. Dessa forma, as variáveis menos relevantes ou redundantes têm sua influência reduzida, resultando em um modelo mais robusto e capaz de generalizar melhor para novos dados.

A utilização da regressão multinomial regularizada nos permitirá construir um modelo de classificação com base nas variáveis independentes disponíveis. Esse modelo será capaz de atribuir probabilidades às diferentes categorias da variável dependente, fornecendo informações valiosas para tomada de decisões e análise de dados em um contexto multiclasse.

### 5.1 Ajuste do modelo

O ajuste do modelo é a forma de seleção dos parâmetros de otimização do modelo. Isso envolve a escolha cuidadosa de configurações como a regularização (por exemplo, usando a técnica LASSO), a taxa de aprendizado, a penalidade das variáveis e outras opções de ajuste disponíveis.

A seleção correta desses parâmetros é fundamental para obter um modelo com bom desempenho e capacidade de generalização. Para isso, podemos utilizar técnicas como validação cruzada e medições de desempenho, como a acurácia ou a matriz de confusão, para avaliar diferentes combinações de parâmetros e identificar a configuração ideal.

Neste projeto o treinamendo do modelo será diretamente com o pacote glmnet. O glmnet é um pacote que ajusta modelos lineares generalizados e similares por meio da máxima verossimilhança penalizada. Ele calcula o caminho de regularização para o LASSO ou penalidade de rede elástica em uma grade de valores, representando o parâmetro de regularização lambda (em escala logarítmica).

```{r message=FALSE, warning=FALSE}

train.matrix <-as.matrix(editais_bake |> select(-cad))

train.matrix <- Matrix(train.matrix, sparse= TRUE)


cv <- cv.glmnet(train.matrix,
             editais_bake$cad,
             nfolds = 3,
             family = 'multinomial',
             type.measure = 'class')


```

No ajuste desse modelo foi aplicado a técnica de validação cruzada (cross validation) que é empregada para otimizar a generalização dos dados. Ela envolve a divisão dos dados de treinamento em subconjuntos e o treinamento do modelo em cada um desses subconjuntos, enquanto suas previsões são avaliadas com os dados restantes. A finalidade de utilizá-la nesta pesquisa foi aumentar a capacidade do modelo de generalizar a dados desconhecidos, ou seja, determinar sua habilidade de prever dados novos com precisão, além de minimizar o risco de sobreajuste dos dados.

O gráfico baixo mostra a curva de aprendizado do modelo. A linha pontilhada vermelha junto com as curvas de desvio padrão superior e inferior ao longo do λ . Dois valores especiais ao longo do λ seqüência são indicadas pelas linhas pontilhadas verticais. `lambda.min`é o valor de λ que dá o erro de validação cruzada média mínima, enquanto `lambda.1se`é o valor de λ que fornece o modelo mais regularizado, de modo que o erro de validação cruzada esteja dentro de um erro padrão do mínimo.

```{r}

plot(cv)
```

predição com um modelo com o valor de regularização otimizado. prob

```{r}



editais_glmnet_prob <- cv |> 
  predict(newx = train.matrix,
          type = "response",
          s = "lambda.min") |>
  data.frame() |> 
  bind_cols(editais_bake) |> 
  tibble()



```

predição com valores classe

```{r}
editais_glmnet_class <- cv |> 
  predict(newx = train.matrix,
          type = "class",
          s = "lambda.min") |>
  tibble(pred = _ ) |> 
  bind_cols(editais_bake) 

editais_glmnet_class$pred <- factor(editais_glmnet_class$pred)
```

## 5.2 Avalização do modelo

A etapa de avaliação de modelos é um processo fundamental para determinar a eficácia e o desempenho dos modelos desenvolvidos. Essa etapa visa testar e comparar diferentes modelos para identificar qual deles melhor se ajusta aos dados e é mais adequado para a tarefa em questão.

A avaliação de modelos é geralmente realizada em um conjunto de dados separado, chamado conjunto de teste, que não foi utilizado durante o treinamento do modelo. Isso é feito para verificar se o modelo é capaz de generalizar bem e produzir resultados precisos em dados não vistos anteriormente.

Durante a etapa de avaliação, são utilizadas métricas de desempenho para quantificar o quão bem o modelo está realizando a tarefa em questão. Essas métricas variam dependendo do tipo de problema e do objetivo do modelo. Alguns exemplos comuns de métricas de avaliação incluem acurácia, precisão, recall, F1-score, área sob a curva ROC, erro médio quadrático, entre outros.

Precisão e ROC AUC são métricas de desempenho básicas usadas para modelos de classificação. Para ambos, valores mais próximos de 1 são melhores.

Precisão é a proporção dos dados que são previstos corretamente. Contudo, a precisão pode ser enganosa em algumas situações, como para conjuntos de dados desequilibrados.

```{r}



accuracy(editais_glmnet_class,cad ,pred)
```

O ROC AUC mede o desempenho de um classificador em diferentes limiares. A curva ROC plota a taxa de verdadeiros positivos em relação à taxa de falsos positivos; AUC mais próxima de 1 indica um modelo de melhor desempenho, enquanto AUC mais próxima de 0,5 indica um modelo que não é melhor do que adivinhação aleatória.

```{r}

roc_curve(editais_glmnet_prob,cad ,1:12) %>%
  autoplot()


```

A matriz de confusão é uma ferramenta comumente utilizada para avaliar o desempenho de um modelo de classificação. Ela permite visualizar a relação entre as classificações reais e as classificações preditas pelo modelo.

```{r}
conf_mat(editais_glmnet_class,cad, pred)  |> 
  autoplot(type = "heatmap")+
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

A diagonal é razoavelmente bem preenchida, o que é um bom sinal. Isso significa que o modelo geralmente previu a classe certa. Os números fora da diagonal são todas as falhas e para onde devemos direcionar nosso foco. É um pouco difícil ver bem esses casos, já que a classe majoritária afeta a escala. Um truque para lidar com esse problema é remover todas as observações previstas corretamente.

```{r}

editais_glmnet_class |> 
  filter(pred != cad) |> 
  conf_mat(cad, pred)  |> 
  autoplot(type = "heatmap")+
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
  
```
