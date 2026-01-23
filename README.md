# 📊 Previsão de Estoque Inteligente na AWS com [SageMaker Canvas](https://aws.amazon.com/pt/sagemaker/canvas/)

Bem-vindo à minha resposta ao desafio de projeto "Previsão de Estoque Inteligente na AWS com SageMaker Canvas".

## 📋 Seleção e Avaliação do Dataset

Selecionei o arquivo [dataset-1000-com-preco-promocional-e-renovacao-estoque.csv](https://github.com/dirceumuller/lab-aws-sagemaker-canvas-estoque/blob/main/datasets/dataset-1000-com-preco-promocional-e-renovacao-estoque.csv) para fazer o desafio. Abri o arquivo para olhar os dados brutos, e ter uma ideia geral. Nesse exemplo não havia uma coluna indicando demanda, mas sim o estoque. Se plotar o estoque em um gráfico, terá o formato de uma serra, iniciando em 100, e diminuindo conforme a demanda até o estoque ser reposto novamente em 100 unidades, e isso se repete a cada vez que o estoque acabou.

A primeira coisa a ser feita portanto era redefinir os dados. O estoque representa a demanda de forma indireta, e na aula isso foi interpretado de forma equivocada. Acrescentei uma coluna para indicar a demanda do dia, calculada como o (estoqueDoDia - estoqueDoDiaSeguinte). Não corrigi a distorção dos dados que ocorre sempre que o estoque de um produto foi reposto, ou seja, voltou a 100 unidades. O estoque sempre foi reposto apenas ao final dos produtos, mesmo que houvesse apenas 1 unidade remanescente do dia anterior, e entendo isso como uma distorção dos dados, pois a demanda pode ter sido maior do que a quantidade disponível na loja. No último dia, em que não se pode calcular a demanda, deixei os dados omissos, confiando que a inteligência artificial vai interpretar corretamente a ausência deles.

![gráficos](https://github.com/dirceumuller/lab-aws-sagemaker-canvas-estoque/blob/main/datasets/Capturar.JPG))

## 🎯 Primeiros percalços e passos

- O Excel salva .csv usando ";" (ponto e vírgula) como separador em vez de "," (vírgula). Acredito que seja por estar configurado para português brasileiro, onde a vírgula é interpretada como marcador de fracionamento. Na primeira tentativa de upload (arquivo [estoque x demanda.csv](https://github.com/dirceumuller/lab-aws-sagemaker-canvas-estoque/blob/main/datasets/estoque%20x%20demanda.csv)), o Canvas não conseguiu reconhecer as colunas por causa disso. Abri o arquivo no Notepad, substituí todos os ";" por "," e renomeei como [estoque x demanda 2.csv](https://github.com/dirceumuller/lab-aws-sagemaker-canvas-estoque/blob/main/datasets/estoque%20x%20demanda%202.csv). Fiz o upload, e a ID_PRODUTO foi interpretada como valor numérico. O modelo não aceitou o tipo de dado. Defini a coluna como sendo texto, e problema resolvido.

- Apenas por curiosidade, deixei selecionado feriados, embora eu saiba que os dados foram gerados aleatoriamente. Assim, qualquer correlação identificada iria se tratar apenas de acaso ou alucinação.

- Previsão de tempo de construção: 2 horas. Bom, fazer o quê, né? Deixei o Canvas rodando, e fui fazer outras coisas.

## 🚀 Resultados encontrados

### 1. Mantendo ESTOQUE e DEMANDA

| Avg. wQL | MAPE  | WAPE  | RMSE  | MASE  |
| -------- | ----- | ----- | ----- | ----- |
| 0.424    | 0.022 | 0.662 | 1.292 | 0.730 |

Deu uma correlação bem pobre, e o Canvas alertou que PREÇO e FLAG_PROMOCAO poderiam estar diminuindo a qualidade do resultado.

### 2. Removendo ESTOQUE

| Avg. wQL | MAPE  | WAPE  | RMSE   | MASE  |
| -------- | ----- | ----- | ------ | ----- |
| 1.000    | 1.000 | 1.000 | 11.044 | 8.293 |

Sem a coluna ESTOQUE, desapareceu qualquer correlação. Como interpretar isso? Bom, ponto a favor do prompt que criou os valores aleatórios para a tabela inicial, pois se os valores são realmente aleatórios, não há correlação para ser encontrada. Revendo o gráfico da distribuição dos dados acima, em ESTOQUE não há realmente nenhuma linha de tendência.

**Curiosidade**:

*Correlação com feriados: 25,06%*  
*Correlação com FLAG_PROMOÇÃO: 0%*

Com dados aleatórios, essas correlações são apenas coincidências. 

### 3. Analisar

Em primeiro lugar, não achar correlação não é um "resultado errado". Nesse caso, representa a realidade, os dados **são** aleatórios. Mas, como resolver o problema então? Na aula, até porque ao vivo o professor está se dividindo em falar, gravar, ler dúvidas, e ao mesmo tempo demonstrar na tela, houve uma interpretação equivocada. Avaliando os dados de forma dedicada, com a atenção não dividida, se percebe que não há um padrão consistente.

### 4. Prever

Se não há um padrão consistente, pode-se utilizar outras estratégias. Por exemplo, se o tempo de entrega do fornecedor é de três dias, pode-se planejar o gatilho da solicitação de compra do produto para repor o estoque quando o estoque atingir um valor entre três e quatro vezes a maior demanda histórica, de forma a evitar que clientes cheguem na loja e o estoque ainda não tenha sido reposto. Para produtos em que o tempo de entrega é imediato, é possível lidar com estoques menores, em um sistema "just-in-time".

## 🤔 E agora?

Achei o desafio interessante. Como na minha área primária de atuação a estatística, os padrões e os outliners são a regra do jogo, foi possível perceber que a ferramenta é poderosa, mas a mão que a empunha também precisa ter a habilidade e o discernimento de como utilizá-la. Alguns cientistas de dados já declararam que Machine Learning e Inteligência Artificial não vão substituir humanos, mas sim fazer com que aqueles dispostos a aprender como usar se tornem mais eficientes e produtivos.
