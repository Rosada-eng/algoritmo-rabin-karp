# Algoritmo de Rabin-Karp

## Buscas em textos

Seja no estudo, trabalho ou lazer, os mecanismos de busca estão presentes nas nossas vidas desde gerações passadas. Imagine seus avós, num mundo ainda sem internet, tendo que buscar o que significa a palavra "Verborrágico" no volume "V" da Enciclopédia Barsa, após ouvir o termo em uma radionovela.

Num processo árduo, possivelmente passariam pelas palavras Vassoura, Ventilador, Verba, Verbo, até descobrir que Verborrágico é o mesmo que prolixo, pleonástico ou tagarela.

Anos após o episódio hipotético fastidioso ao qual seus avós foram submetidos, você ainda enfrenta o mesmo desafio que eles supostamente enfrentaram.

## Como procurar palavras específicas em textos?

Ao pesquisar uma simples palavra (como "verborrágico”, por exemplo) no Google, você espera receber vários textos da internet em que essa palavra foi citada. Mas como é feita a busca de uma única palavra dentre TODOS OS DADOS DA INTERNET?

Imaginando que a internet é uma grande Enciclopédia Barsa, o processo lógico que seus avós utilizariam seria de ir aos poucos se aproximando da palavra desejada utilizando a ordem alfabética.

    #TODO: 🛌🏿 Animação: V → Vassoura → VEntilador → VERBa → VERBO → VERBORRÁGICO

??? Checkpoint
Mas e você? O que você acha? O algoritmo desenvolvido inconscientemente por seus avós é o mais eficiente para atacar esse tipo de problema?

::: Gabarito

Aparentemente, não. Num universo de zilhões de dados, a estratégia parece um tanto quanto pueril.

:::
???

Bom, você deve imaginar que a comparação **letra a letra** da palavra "Verborrágico" com as diversas outras palavras presentes no universo deve demorar uma eternidade. Convenhamos, fomos um pouco ingênuos em achar que funcionaria.

Vamos tentar, de fato, quantificar o porquê dessa estratégia não ser eficiente. Como "verborrágico” é uma palavra um tanto quanto verborrágica, vamos imaginar um exemplo mais simples em que a estratégia de comparar letra a letra é utilizada para buscar o texto "amar" na palavra “reclamar”. Considerando o mecanismo ingênuo, a checagem ocorreria da seguinte forma:

    #TODO: Animação: busca do amar dentro do reclamar letra a letra

??? Checkpoint

Utilizando o mecanismo de busca letra a letra, quantas checagens teriam que ser feitas para buscar uma palavra que possui n caracteres num texto que possui m caracteres?

::: Gabarito
Isso mesmo, $n \cdot m$. Cada caractere do padrão buscado é checado com caracteres do texto que está sendo varrido.

:::

???

Agora você já sabe o porque nem mesmo os supercomputadores (aqueles mesmos que fazem 200 quatrilhões de cálculos por **_segundo_**) seriam capazes de entregar uma pesquisa dessa forma em instantes, como faz o Google. Imagine buscar "verborrágico”, uma palavra com 12 caracteres dentro de zilhões de caracteres de toda a internet!

Felizmente, muito se evoluiu desde a era _bársica_ e os mecanismos de busca de pedaços de textos em volumes maiores de palavras evoluíram também. No contexto atual, em que dados com maior variedade que chegam em volumes crescentes e com velocidade cada vez maior, algoritmos mais sofisticados foram criados para lidar com esse problema de forma muito mais rápida.

## A ideia do algoritmo ingênuo

Já que não é nada interessante buscarmos um padrão dentro de um texto comparando letra a letra todas as vezes, que estratégia poderíamos adotar para que pudéssemos, por exemplo, descartar rapidamente um padrão se ele não for igual ao trecho do texto analisado?

??? Checkpoint

Pense em uma estratégia que, quando compararmos o padrão com o trecho do texto (por exemplo, procurar `BCB` dentro do texto `ACBCBB`), possamos descartar imediatamente os casos que são “bem” diferentes.

**Dica**: Imagine duas pessoas com 4 moedas cada uma, em que as moedas podem ter ou não o mesmo valor. Qual o procedimento que você naturalmente faria para dizer quem tem a mesma quantia de dinheiro?

::: Gabarito
Você olharia os valores de cada moeda e somaria para obter um valor total para cada uma das pessoas. De posse desse valor, você poderia determinar se elas têm ou não a mesma quantia.

O procedimento para buscarmos um padrão no texto é análogo. Podemos pensar em “valores” que cada letra possui que, quando somados, nos dão o “quanto vale” aquele padrão, e então calcularíamos o “quanto vale” o trecho do texto para comparar o nosso padrão.
:::

???

Como usaríamos essa ideia na prática?

Poderíamos começar pensando no nosso alfabeto `A, B, C, ..., Z`, em que, para cada letra, atribuiríamos um número, por exemplo, `A=1, B=2, C=3, ..., Z=26`.

??? Checkpoint

Lembrando de como fizemos no início, usando uma “janela” avançando caractere por caractere dentro do texto, use essa estratégia com o padrão e o texto do checkpoint anterior para calcular o “valor total” do padrão e das 2 primeiras “janelas” do texto. O que você concluiria comparando esses valores?

::: Gabarito
Para o padrão, temos que:

$BCB=B+C+B=2+3+2=7$

A primeira janela do nosso texto é `ACB` e a segunda, `CBC`:

$ACB=A+C+B=1+3+2=6$

$CBC=C+B+C=3+2+3=8$

Observamos que o valor total de cada uma das 2 primeiras janelas é diferente do padrão buscado. Então, poderíamos afirmar, sem ter que comparar letra a letra, que os dois trechos analisados não correspondem ao nosso padrão de busca.
:::

???

Ok, os casos que comparamos resultaram em valores diferentes do nosso padrão, sendo fácil, portanto, descartá-los. E se esses valores resultassem iguais, poderíamos afirmar que correspondem ao padrão de busca?

??? Checkpoint

Continue o procedimento para as “janelas” restantes do texto.

::: Gabarito
$BCB=B+C+B=2+3+2=7$

$CBB=C+B+B=3+2+2=7$
:::

???

Opa! Encontramos dois casos cujos valores calculados são iguais ao do padrão, mas um deles não é o que buscamos.

??? Checkpoint

O que podemos fazer para distinguir cada um dos dois casos acima?

::: Gabarito
Podemos fazer com a ideia mais simples que já vimos, que é comparar letra por letra. Mas, para sermos mais práticos, fazemos a comparação letra por letra. Se uma delas não coincidir, já podemos descartar esse trecho e partir para o próximo. O trecho só será considerado igual ao padrão se eles forem iguais **letra por letra**.

Ao comparar o padrão `BCB`com `BCB`, só concluímos que são equivalentes ao comparar letra a letra até a última. No caso de comparar `BCB` com `CBB`, na verificação da primeira letra já constatamos que não são iguais, então podemos descartar esse trecho e prosseguir com o restante do texto.
:::

???

Ou seja, uma forma um pouco mais rápida para buscarmos um padrão em um texto é calcular um valor a esse padrão e comparar com o valor de cada trecho do texto, e somente fazer a comparação entre caracteres quando esses valores forem iguais.

A essa técnica de “atribuir valor” a uma entrada damos o nome de **_hashing_**. Essa técnica é feita com _funções de hashing_, que recebe uma entrada (chave) e a associa a um _hash value_. Ou seja, nessa versão mais simplificada que trabalhamos, as letras correspondem às entradas e seus “valores” aos *hash value*s.

Legal, conseguimos ser um pouco mais rápidos com essa ideia de _hashing_ para buscar padrões em texto. No entanto, se pararmos para analisarmos melhor esse procedimento, ao utilizarmos com padrões e textos reais, com _hash values_ mais complexos, nosso algoritmo terá que aplicar a _função de hashing_ para cada “janela”, implicando em muitos cálculos a se fazerem e, portanto, exigindo mais processamento por parte do algoritmo. Como poderíamos melhorar a nossa ideia nesse quesito?

??? Checkpoint

Retomando os nossos exemplos com o padrão `BCB` e o texto `ACBCBB`, calculamos os _hash values_ das 2 primeiras janelas da seguinte maneira:

$ACB=A+C+B=1+3+2=6$

$CBC=C+B+C=3+2+3=8$

Como poderíamos obter o valor do trecho seguinte mais rapidamente, sem ter que recalculá-lo do zero?

**Dica**: Pense no deslocamento da “janela” ao longo do texto.

::: Gabarito
Lembre-se que a “janela” desloca-se sempre de 1 em 1 caractere ao longo do texto. O trecho `CBC` é obtido a partir do trecho `ACB`, removendo o caractere inicial `A` e adicionando ao final o caractere seguinte do texto `C`.

Em termos de cálculo, isso equivaleria a:

$CBC=ACB-A+C=6-1+3=8$

Assim, podemos reaproveitar o valor de cada trecho para obtermos o trecho seguinte.
:::

???

A ideia apresentada no checkpoint acima é conhecida como Rolling Hash, que é uma função de hashing em que o trecho de entrada é deslocado ao longo do dado e seu hash value é calculado a partir do anterior (exceto a primeira “janela”, que é calculada “do zero”). Essa técnica permite que se obtenha o hash value mais rapidamente, reduzindo ainda mais os custos operacionais para o nosso algoritmo.

## A função de hashing na prática

Você talvez tenha achado bem simples a convenção para os valores de cada letra (`A=1, B=2, ..., Z=26`) e que bastava somá-los, e de fato essa ideia está extremamente simplificada, pois o propósito inicial era ter uma noção geral de como o _hashing_ funciona. Agora, vamos entender como se dá o cálculo do _hash_.

Considere que o padrão de interesse tem `M` caracteres, o texto a ser analisado tem `N` caracteres e o alfabeto tem `R` caracteres.

!!! Atenção
Aqui, quando falamos alfabeto, estamos falando da quantidade de números para representar cada caractere. Por exemplo, o alfabeto de dígitos decimais tem 10 elementos (0 ... 9), enquanto que o alfabeto ASCII estendido tem 256 elementos (0 ... 255).
!!!

O cálculo do hash é feito como se a string fosse convertida para um número em base R.

??? Checkpoint

Ainda assumindo que `A=1, B=2, ..., Z=26`, a primeira etapa para o cálculo do _hash_ é converter o padrão usando R como base. Para o padrão `ACDE`, que valor obtém-se ao aplicar a condição dada em base R?

**Dica**: O binário 1101 de 2 equivale a $1\cdot2^3+1\cdot2^2+0\cdot2^1+1\cdot2^0$

::: Gabarito
Sendo `A=1`, `C=3`, `D=4` e `E=5`, convertemos da seguinte maneira:

$1\cdot R^3+3\cdot R^2+4\cdot R^1+5\cdot R^0$
:::

???

A etapa seguinte consiste em fazer a operação mod usando módulo Q, isto é, calculando o resto da divisão do valor calculado no passo anterior por Q.

??? Checkpoint

Assuma, ainda para o caso anterior, que `Q=547` e `R=10`, determine o hash do padrão ACDE.

::: Gabarito
O valor calculado no checkpoint anterior é:

$10^3+3\cdot10^2+4\cdot10+5=1345$

O _hash_ então é determinado da seguinte maneira:

$1345 \ \% \ 547=251$

Esse é o _hash_ correspondente ao padrão `ACDE` para esse caso em particular.
:::

???

Da mesma maneira que o cálculo do havia sido apresentado de maneira simplificada no início, o _rolling hashing_ também foi mostrado de forma mais “ingênua”. A ideia geral continua a mesma, ou seja, removemos do cálculo o primeiro caractere do padrão adicionamos na conta o caractere inserido, mas é necessários alguns ajustes nesse cálculo já que estamos trabalhando com base `R`.

??? Checkpoint

Calcule o hash para as duas primeiras janelas do texto ACBCBB, com 3 caracteres cada uma, com Q=547 e R=10.

::: Gabarito
$ACB=(1\cdot10^2+3\cdot10^1+2\cdot10^0)\ \% \ 547=132$

$CBC=(3\cdot10^2+2\cdot10^1+3\cdot10^0)\ \% \ 547=323$

:::

???

??? Checkpoint
Agora, pense em uma maneira de obter o hash do segundo trecho calculado a partir do primeiro.

**Dica**: Observe o que acontece com a potência de R em cada termo ao deslocar a janela em um caractere.

::: Gabarito
Na versão mais simples desse procedimento, apenas subtraímos o caractere retirado e somamos o entrante ao hash anterior. Nesse caso, iniciamos também subtraindo o valor correspondente ao primeiro caractere, mas devemos nos atentar ao fator $R^k$ que multiplica esse caractere.

O caractere entrante é multiplicado pelo fator $R^0$, então basta somar seu valor ao hash anterior.

O pulo do gato está nos caracteres que ficam. Ao deslocar todos para a esquerda, cada termo é multiplicado por $R$. Veja passo a passo:

$ACB=A\cdot R^2+C\cdot R^1+B$

$\_CB=C\cdot R+B=ACB-A\cdot R^2$

$CB\_=(C\cdot R+B)\cdot R=(ACB-A\cdot R^2)\cdot R$

$CBC=(ACB-A\cdot R^2)\cdot R+C$
:::

???

Então, suponha que $x_{i}$ seja o hash que se deseja obter a partir do hash de $x_{i-1}$, e que $x_i$ é representado por $c_ic_{i+1}...c_{i+m-1}$, em que $c_k$ representa cada caractere. Podemos obter $x_i$ da seguinte maneira:

$x_i=(x_{i-1}-c_{i-1})\cdot R+c_{i+m-1}$

### Algoritmo de Horner

Uma forma de acelerar o cálculo do hash é por meio do **Algoritmo de Horner**. Imagine que queremos calcular $1\cdot R^3+3\cdot R^2+4\cdot R^1+5\cdot R^0$. Se `R=256`ou maior, por exemplo, os números podem se tornar grandes o suficiente para reduzir a eficiência do algoritmo em termos de tempo de execução. Então, em vez de calcularmos diretamente dessa maneira, podemos fazê-lo em passos:

$R\cdot(4+R\cdot(3+R))+5$

Essa maneira acelera o processo do cálculo do _hash_, diminuindo o tempo necessário de execução e otimizando o algoritmo de busca.

Esse algoritmo é particularmente recomendado devido a sua capacidade de identificar padrões em textos longos relativamente rápido. Mas, da forma como está, você o recomendaria para uma empresa que precisa encontrar padrões de sequenciamento de nucleotídeos em amostras de DNA, sendo que cada uma delas contém milhões de nucleotídeos ? Provavelmente não. Ainda não temos uma performance tão boa quanto gostaríamos. Será que ainda podemos aprimorar o desempenho do algoritmo e torná-lo mais rápido?

??? Checkpoint
Você consegue identificar qual etapa do nosso algoritmo simplificado poderíamos aprimorar?

::: Gabarito
Se você ficou com a sensação de que esse algoritmo está muito “enroscado”, você está no caminho certo. Utilizar uma fórmula simples para calcular o hash de cada palavra seria desperdiçar todo o seu potencial e genialidade. Podemos deixar o nosso algoritmo mais fluido se conseguirmos tornar o matching entre hashes cada vez mais raro. Dessa forma, nosso algoritmo não precisaria percorrer o segundo looping desnecessariamente várias vezes.

:::
???

## Aprimorando nosso algoritmo

Bom, se não queremos que nosso algoritmo “trave” muito, ou seja, dê match várias vezes, precisamos que nossa fórmula gere uma grande variedade de hashes. Para isso, temos dois fatores que podemos variar: a base e o divisor. A base geralmente é definida de acordo com o número de caracteres do alfabeto utilizado (para o alfabeto de dígitos decimais {0, 1, 2, ... , 9}, utiliza-se R = 10; para o[ ASCII estendido](http://www.mhavila.com.br/topicos/mm/char_maps.html) {a,A,Å, ..., ÿ }, R = 256) e, portanto, não temos muito controle sobre ela. Resta analisar o divisor.

Estamos interessados, na verdade, no resto deixado pela operação de divisão e queremos maximizar a variedade de restos possíveis. Para isso, queremos que, num conjunto de números, tenhamos a menor quantidade de múltiplos possíveis (caso em que o resto é zero) e que esses os restos obtidos repitam poucas vezes (se possível, nenhuma vez).

Talvez a resposta para a primeira pergunta seja fácil. Você se lembra do nome de conjunto de números especiais que tem quase nenhum divisor? Além do 1 ele mesmo?

Sim, os **números primos**! São em situações assim que eles começam a ser úteis! Resta agora, descobrir como podemos maximizar o número de valores diferentes para o resto. Para isso, podemos analisar um conjunto de 1 a 10 e os restos obtidos pelos primos 3, 5 e 7.

| mód (n)  | Conjunto                   | nº dígitos distintos                |
| -------- | -------------------------- | ----------------------------------- |
| Conjunto | {1,2,3,4,5,6,7,8,9,10}     | -                                   |
| n %3     | {1,2,0,1,2,0,1,2,0,1}      | 3 dígitos distintos {0,1,2}         |
| n %5     | {1,2,3,4,0,1,2,3,4,0}      | 4 dígitos distintos {0,1,2,3,4}     |
| n %7     | {1,2,3,4,5,6,0,1,2,3}      | 6 dígitos distintos {0,1,2,3,4,5,6} |
| ...      | ...                        | ...                                 |
| n %k     | {0,1,2,3,4, ..., k-2, k-1} | k-1 dígitos distintos               |

??? Checkpoint
Você consegue tirar alguma conclusão com a tabela acima?

::: Gabarito
O número de valores distintos para o resto pode ser dado por: #N = k-1, em que k é um número primo qualquer. Assim, quanto maior o valor do primo, maior a quantidade de restos possíveis.

:::
???

Agora sabemos o que precisamos: a base referente ao nosso alfabeto utilizado e um número primo grande, para gerar vários restos distintos. Mas será que podemos, ainda, utilizar quaisquer números?

??? Checkpoint

#TODO: exercício com valores de base e divisor que estouram a memória
::: Gabarito
#TODO: solução do ex.
:::
???
!!! Aviso
Como estamos lidando com a potência de um número, precisamos tomar o devido cuidado para que ele não se torne muito grande a ponto de não ser armazenado no espaço de memória da variável.
!!!

Para evitar esse problema, podemos utilizar do algoritmo de Horner aliado às propriedades de mod. Assim, após cada operação aritmética (multiplicar por R e somar o próximo termo), calcularemos o resto da divisão por D, mantendo, assim, a dimensão dos nossos números sob controle sem alterar o resultado final.

::: Demonstração

    #TODO: Propriedades de MOD

Agora, podemos calcular o valor de Hash de uma maneira eficaz. Além disso, podemos fazer uso do rolling hash para tornar as contas mais fáceis:

    #TODO: Equação final em markdown:

$$x_i \% Q = ((x_{i-1} \% Q + c_{i-1} \cdot (Q-R^{m-1} \% Q))\cdot R + c_{i+m-1}) \% Q$$
