# Titanic --- Duelo de Modelos

Projeto desenvolvido a partir da competição **Titanic: Machine Learning
from Disaster**, do Kaggle, como prática de modelagem e otimização de
algoritmos de Machine Learning.

O projeto foi construído com foco em **autonomia na escolha, preparação,
comparação e otimização de modelos**, utilizando SVM e XGBoost e
avaliando diferentes configurações antes da submissão ao Kaggle.

## Objetivo do projeto

Desenvolver um modelo capaz de prever a sobrevivência dos passageiros do
Titanic a partir das características disponíveis no conjunto de dados.

Objetivos principais:

-   praticar a preparação de dados para Machine Learning;
-   realizar análise exploratória;
-   estabelecer um baseline;
-   aplicar pré-processamento e balanceamento;
-   realizar um duelo entre modelos;
-   utilizar otimização de hiperparâmetros e Cross Validation;
-   selecionar uma configuração final;
-   gerar uma submissão válida para o Kaggle;
-   analisar a diferença entre os resultados internos e o score público.

O foco não foi apenas obter uma pontuação elevada, mas compreender o
efeito de cada etapa sobre o desempenho dos modelos.

## Dataset

Foram utilizados os arquivos disponibilizados pela competição:

-   `train.csv` --- dados de treinamento, contendo as variáveis
    preditoras e `Survived`;
-   `test.csv` --- dados utilizados para gerar as previsões da
    competição;
-   `gender_submission.csv` --- arquivo de referência para o formato da
    submissão.

O conjunto de treinamento possui **891 registros e 12 colunas** e o
conjunto de teste possui **418 registros e 11 colunas**.

A variável-alvo é:

-   `Survived = 0` --- não sobreviveu;
-   `Survived = 1` --- sobreviveu.

No treinamento:

-   549 passageiros não sobreviveram;
-   342 passageiros sobreviveram.

## Estrutura do projeto

``` text
Projeto Titanic/
├── data/
│   ├── train.csv
│   ├── test.csv
│   └── gender_submission.csv
├── notebooks/
│   ├── 01_analise_exploratoria.ipynb
│   └── 02_duelo_modelos.ipynb
├── outputs/
│   ├── figures/
│   └── submission/
│       └── submission.csv
└── README.md
```

A organização separa a análise exploratória da etapa principal de
modelagem e mantém os arquivos originais do Kaggle preservados em
`data/`.

## Análise exploratória

Foram analisados:

-   estrutura e dimensões das bases;
-   tipos das variáveis;
-   valores ausentes;
-   duplicidades;
-   distribuição da variável-alvo;
-   relação entre características dos passageiros e sobrevivência.

As principais variáveis observadas foram `Sex`, `Pclass`, `Age`, `Fare`
e `Embarked`.

A análise indicou diferenças claras na distribuição de sobrevivência de
acordo com `Sex` e `Pclass`. A combinação dessas variáveis também
apresentou padrões relevantes.

`Fare` apresentou diferenças entre sobreviventes e não sobreviventes,
enquanto `Age` mostrou maior sobreposição entre os grupos.

Também foram identificados valores ausentes em `Age`, `Cabin`,
`Embarked` e, no conjunto de teste, `Fare`.

## Preparação dos dados

Para a modelagem, foram removidas:

-   `PassengerId`;
-   `Name`;
-   `Ticket`;
-   `Cabin`.

`PassengerId` é um identificador, enquanto `Name` e `Ticket` exigiriam
transformações adicionais. `Cabin` apresentava muitos valores ausentes.

Tratamento dos valores ausentes:

-   `Age` → mediana;
-   `Embarked` → moda;
-   `Fare` no conjunto de teste → mediana.

Transformação das variáveis categóricas:

-   `Sex`: `male = 0`, `female = 1`;
-   `Embarked`: `S = 0`, `C = 1`, `Q = 2`.

A divisão dos dados foi de **80% para treinamento e 20% para validação
interna**, utilizando `stratify=y` e `random_state=42`.

## Modelos utilizados

Foram comparados dois algoritmos:

### SVM

Utilizado por sua capacidade de encontrar fronteiras de decisão entre
classes e por sua sensibilidade à escala das variáveis.

### XGBoost

Utilizado como modelo baseado em árvores de decisão combinadas por
boosting, permitindo comparar uma abordagem diferente da utilizada pelo
SVM.

O objetivo do duelo foi observar o comportamento dos modelos diante das
diferentes etapas de preparação e otimização.

## 6. Baseline

  Modelo      Acurácia
  --------- ----------
  SVM           62,01%
  XGBoost       80,45%

O XGBoost apresentou desempenho inicial superior ao SVM.

## 7. Pré-processamento / Balanceamento

Resultados obtidos com o SVM:

  Configuração                     Acurácia
  ------------------------------ ----------
  SVM + StandardScaler               81,56%
  SVM + StandardScaler + SMOTE       79,89%

A padronização produziu uma melhora expressiva no SVM.

O SMOTE, aplicado somente aos dados de treinamento, reduziu a acurácia
nessa configuração. O resultado reforçou que uma técnica de
balanceamento não deve ser considerada automaticamente como melhoria.

## 8. Duelo

  Modelo/configuração                Acurácia
  ------------------------------ ------------
  SVM --- Baseline                     62,01%
  XGBoost --- Baseline                 80,45%
  SVM + StandardScaler             **81,56%**
  SVM + StandardScaler + SMOTE         79,89%

Até esse ponto, a melhor configuração avaliada no conjunto de validação
foi o **SVM com StandardScaler**.

## 9. Otimização + Cross Validation

Foi utilizado `GridSearchCV` com **5 folds de Cross Validation**.

### SVM otimizado

Melhores parâmetros:

``` text
C = 1
kernel = rbf
gamma = auto
```

Resultados:

-   Cross Validation: **82,87%**
-   Teste interno: **81,56%**

### XGBoost otimizado

Melhores parâmetros:

``` text
learning_rate = 0.1
max_depth = 3
n_estimators = 200
```

Resultados:

-   Cross Validation: **82,73%**
-   Teste interno: **79,89%**

Os dois modelos apresentaram resultados próximos no Cross Validation. No
teste interno, o SVM otimizado apresentou 81,56%, enquanto o XGBoost
otimizado apresentou 79,89%.

A otimização dos hiperparâmetros, portanto, não garantiu aumento de
desempenho no conjunto de teste em relação às melhores configurações
anteriores.

## 10. Modelo final

O modelo final escolhido foi:

**SVM otimizado + StandardScaler**

Parâmetros principais:

``` text
C = 1
kernel = rbf
gamma = auto
```

Essa configuração foi utilizada para gerar as previsões destinadas ao
Kaggle.

## 11. Submission

As previsões foram organizadas no formato exigido pela competição:

``` text
PassengerId
Survived
```

O arquivo foi salvo em:

``` text
outputs/submission/submission.csv
```

## 12. Kaggle e análise do Score

Score obtido no Kaggle:

**0,78229 (78,23%)**

Comparação:

  Avaliação                               Resultado
  ------------------------------------ ------------
  SVM otimizado --- Cross Validation         82,87%
  SVM otimizado --- teste interno            81,56%
  Kaggle                                 **78,23%**

O score do Kaggle ficou abaixo dos resultados observados durante o
desenvolvimento, mostrando que o desempenho da validação interna não foi
reproduzido integralmente no conjunto avaliado pela competição.

## Conclusão

O projeto percorreu o processo completo de construção de um modelo de
Machine Learning, desde a análise exploratória até a avaliação em uma
competição real.

O duelo mostrou que o desempenho depende não apenas do algoritmo, mas
também da preparação dos dados e da configuração dos hiperparâmetros.

O SVM apresentou uma melhora expressiva após o `StandardScaler`,
passando de 62,01% no baseline para 81,56%. O SMOTE não produziu
melhoria nessa configuração.

Na otimização, o SVM apresentou 82,87% de acurácia média no Cross
Validation e 81,56% no teste interno. O XGBoost apresentou 82,73% no
Cross Validation e 79,89% no teste interno.

O modelo final foi utilizado para gerar a submissão ao Kaggle,
alcançando **0,78229 (78,23%)**.

Mais do que comparar uma pontuação final, o projeto teve como objetivo
desenvolver autonomia na análise do comportamento dos modelos e
compreender como diferentes decisões de preparação, balanceamento e
otimização influenciam os resultados.

## Tecnologias utilizadas

-   Python
-   Pandas
-   NumPy
-   Matplotlib
-   Seaborn
-   Scikit-learn
-   Imbalanced-learn
-   XGBoost
-   Jupyter Notebook
-   Kaggle

## Principais conceitos praticados

-   Análise exploratória de dados
-   Tratamento de valores ausentes
-   Transformação de variáveis categóricas
-   Train/Test Split
-   Baseline
-   SVM
-   XGBoost
-   StandardScaler
-   SMOTE
-   GridSearchCV
-   Cross Validation
-   Avaliação e comparação de modelos
-   Geração de submissão para Kaggle
-   Análise de desempenho em dados externos à validação
