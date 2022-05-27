# Algoritmo de Rabin-Karp

## Buscas em textos

A busca de pedaços de palavra, palavras completas ou trechos de textos em conteúdos maiores é um problema que vem habitando o dia a dia de praticamente todos que utilizam computadores, desde suas primeiras versões. O primeiro comando que deve vir a sua mente nesse tema, o popular _Ctrl+F_, apesar de pouco sofisticado, exige um raciocínio computacional bem mais verborrágico do que parece.

Na era atual, ainda mais, é possível pensar em diversos contextos que exigem lidar com dados variados, em volumes crescentes e com velocidade cada vez maior. A importância de ferramentas de busca cresce fortemente com o crescimento do uso de conjuntos de dados maiores e mais complexos (ou Big Data, num termo bem popular atualmente).

De fato, existem diversos algoritmos (com estratégias diferentes) que servem à finalidade proposta. Você consegue pensar em uma estratégia para o desafio de, digamos, buscar o termo `md paga` na palavra `md papagaio`?

## Como procurar palavras específicas em textos?

Taticamente falando, muitos dos métodos dependem de percorrer todo o texto, buscando o trecho que quer ser encontrado. No exemplo citado acima, temos um trecho de 4 letras a ser buscado em uma palavra de 8 letras. A princípio, é razoável assumir que temos que conferir todas as _janelas_ de 4 caracteres seguidos dentro do conjunto total, que tem 8 caracteres.

:windows

Nesse caso, a nossa verificação encontrou 1 ocorrência do termo nas _janelas_ contidas na palavra buscada. O modo em que a checagem dentro das _janelas_ é feita pode variar para os diferentes algoritmos existentes. Como primeira abstração, vamos partir de um algoritmo "força bruta".

## Algoritmo da força bruta

Talvez a estratégia que você pensou anteriormente foi de comparar, letra a letra, contando cada ocorrência em que todas as letras sejam iguais e interrompendo a análise de uma dada _janela_ quando um dos caracteres não for igual. Por exemplo, se quiséssemos contar as ocorrências de `md paga` em `md papagaio`, seria possível fazer:

:brutus

### Pseudo-código da versão Força Bruta

```powershell
 enquanto não chega ao final do texto:
    enquanto não chega ao final da janela:
        compara o caractere da janela com o caractere do padrão.
            se forem iguais:
                avança o índice da janela em um.
            se não forem:
                sai do looping.
        se todos os caracteres analisados foram iguais:
            adiciona o índice do início da janela em um array de matchings.

        avança a janela em uma posição.
```

??? Checkpoint

Analisando o comportamento acima, o que você poderia deduzir a respeito da complexidade dessa estratégia de busca, no **pior dos casos**?

:::Gabarito

No pior dos casos, para cada _janela_ do texto comparada com o padrão, compararíamos todas as letras até chegar à última, ou seja, percorreríamos dois loopings de comparação, o que implicaria em um algoritmo com complexidade $O(n^2)$.

:::

???

Essa estratégia pode até parecer razoável para o exemplo anterior, mas imagine que fôssemos buscar as ocorrências da palavra `md batata` no livro `md Quincas Borba`, de Machado de Assis ("Ao vencedor, as batatas!"). Quantas comparações letra a letra o algoritmo teria que fazer até que analisasse o livro por completo, procurando pelo padrão? Com certeza seria um número grande, mas boa parte delas seriam desnecessárias ou poderiam ter sido descartadas, caso adotássemos uma estratégia melhor.

Por ter uma complexidade quadrática, esse algoritmo está longe de ser uma boa alternativa para a busca de padrões textuais na vida real. Por enquanto, estamos desperdiçando nossos recursos fazendo comparações intermediárias que serão descartadas em alguns passos adiante (quando o fim da palavra não corresponde ao padrão). Será que teria uma maneira conveniente para evitar isso? O que poderíamos fazer para melhorar nosso algoritmo e diminuir sua complexidade?

É aqui que começa a aparecer a genialidade do algoritmo de Rabin Karp!

## O algoritmo ingênuo

Imagine que cada palavra ou conjunto de caracteres tivesse uma _impressão digital numérica_, isto é, algum código numérico que a identificasse e que pudéssemos usá-lo para compará-la com outras palavras do texto. Isso reduziria nosso trabalho a simplesmente buscar as digitais de cada janela e comparar com a digital do nosso padrão.

Resta-nos, porém, determinar uma regra para compor essa impressão digital numérica. Uma maneira simples de se fazer isso seria propor uma equivalência numérica para cada caractere, somar o valor de cada um e compor a identidade numérica da palavra. Para isso, podemos fazer uso da tabela ASCII simplificada, em que cada caractere corresponde a um número `md (A=65, B=66, ... ,Z=90)`.

| Caractere | Decimal | Octal | Hexadecimal |
| :-------: | :-----: | :---: | :---------: |
|    ...    |   ...   |  ...  |     ...     |
|   **@**   |   64    | 0100  |    0x40     |
|   **A**   |   65    | 0101  |    0x41     |
|   **B**   |   66    | 0102  |    0x42     |
|   **C**   |   67    | 0103  |    0x43     |
|    ...    |   ...   |  ...  |     ...     |
|   **Y**   |   89    | 0131  |    0x59     |
|   **Z**   |   90    | 0132  |    0x5a     |
|   **[**   |   91    | 0133  |    0x5b     |
|  **\\**   |   92    | 0134  |    0x5c     |
|    ...    |   ...   |  ...  |     ...     |

Você pode ver a versão completa da Tabela ASCII [aqui](https://web.fe.up.pt/~ee96100/projecto/Tabela%20ascii.htm).

No exemplo que está sendo trabalhado, o valor da _impressão digital numérica_ associado ao termo `md paga` é o nosso target, ou seja, o que queremos encontrar dentro da palavra `md papagaio`. Ao analisar a tabela ASCII, verificamos que o valor da _impressão digital numérica_ target é 281. 

![Hash Paga](/hashpaga.png)

Resta, então, percorrer todas as janelas de 4 caracteres da palavra (assim como no algoritmo força bruta) e encontrar todas aquelas que tem valores de _impressão digital numérica_ iguais ao valor target, 281. Quando uma janela não tiver o valor de _impressão digital numérica_ desejado, não é preciso analisar letra a letra para encontrar os matchs - ela já não deu match na busca do código numérico!

:ingenuo

Essa estratégia de gerar uma saída padronizada (_impressão digital numérica_) a partir de uma entrada qualquer (uma palavra qualquer: um conjunto de caracteres) é bastante utilizada no mundo da computação, em diversas formas diferentes. Ela é conhecida como **Função Hash**. Uma função hash é aquela que recebe um valor qualquer de entrada e devolve uma resposta em um formato padronizado. Essa saída é conhecida como **_hash value_** (que, convenhamos, é bem menos verborrágico que _impressão digital numérica_).

```powershell

i = 0
hash = 0
enquanto i for menor que o tamanho da string
    hash += funcao_hash(string[i])

```

??? Checkpoint

No caso da animação anterior, em que aplicamos o _hashing_, considerando o **pior dos casos**, qual seria a complexidade desse método?

**Dica**: Pense em como você calcularia o _hash value_ de cada trecho analisado.

::: Gabarito

Para calcular o _hash_ de cada trecho analisado, precisamos varrê-lo e somar o valor númerico correspondente a cada letra. Dessa forma, ainda precisamos recorrer ao looping interno à janela. Assim, nosso algoritmo ainda continua tendo complexidade $O(n^2)$.

:::

???

Até agora, essa ideia não parece ter surtido efeito na melhoria do algoritmo, pois continuamos percorrendo um looping na janela de cada uma das strings para obter o seu _hash_. Mas será que é necessário recalcular o _hash_ cada vez que deslocamos a janela de análise em uma posição?

??? Checkpoint

Você consegue propor uma estratégia para não ter que percorrer todos os caracteres contidos dentro da janela da string para obter o seu _hash_?

**Dica:** Reveja a última animação. O que acontece quando a janela _avança_ um caractere?

::: Gabarito

A cada avanço da janela sobre o texto, os termos do cálculo do _hash_ que mudam são os termos inicial e final (os demais não se alteram). Portanto, em vez de percorremos toda a string a cada iteração, podemos apenas **subtrair** o _hash_ do caractere que saiu e **somar** o _hash_ do caractere entrante.

Por exemplo, ao sair de `md PAPA` ($hash=290$) para `md APAG`, o cálculo seria:

$$hash(APAG) = hash(PAPA) - hash(P) + hash(G)$$
$$hash(APAG) = 290 - 80 + 71 = 281$$

Veja a animação abaixo:

:rolling

:::

???

A ideia apresentada acima é conhecida como **_Rolling Hash_**. Ela nos permite calcular mais rapidamente o _hash value_ de um trecho a partir do _hash_ anterior.

??? Checkpoint
Antes de prosseguirmos, há mais um ponto a ser levantado. Você notou, na animação que exemplifica o _hash_, que houve um caso em que o _hash value_ da janela era igual ao do padrão? As strings eram iguais?.

::: Gabarito

Houve uma colisão de _hash_ em que as strings analisadas eram diferentes!

:::

???

??? Checkpoint

No caso descrito acima, qual foi o "critério de desempate" para strings com _hashs_ iguais?

Quais possíveis implicações isso pode ter em termos de desempenho do algoritmo?

::: Gabarito

Quando os _hashs_ resultam no mesmo valor, é necessário fazer uma segunda verificação, caractere por caractere, para de fato validar ou não a igualdade entre strings.

Já fica evidente, portanto, que, no pior dos casos, quando toda janela possui o mesmo _hash_ do padrão, o algoritmo tem complexidade $O(n^2)$.

:::
???

!!! Importante
Esse é um ponto crucial no algoritmo! Não é possível impedir que colisões de _hash_ com strings distintas aconteçam. E, quanto mais frequente forem, mais teremos que forçar o algoritmo a checar a correspondência caractere por caractere.
Porém, a boa notícia é que temos total controle sobre como as _digitais numéricas_ de cada palavra são formadas e, portanto, podemos tornar a colisão de _hashes_ mais rara.

!!!

No código acima, fica nítido que podem ocorrer colisões não desejadas ao longo da busca em um texto. Mas, no último checkpoint, também vimos que temos o controle sobre a **Função Hash**, que pode tornar as _digitais numéricas_ mais ou menos singulares, dificultando, assim, colisões indesejadas. Assim, a construção de um algoritmo em cima da ideia de _Hashing_ deve considerar **reduzir a probabilidade dessas colisões**.

Se conseguíssemos tonar os _hashing values_ suficientemente singulares, poderíamos simplesmente confiar no _matching_ entre os valores para julgar a igualdade ou não entre as strings. De fato, essa é uma versão aplicada do algoritmo de _Rabin-Karp_ e é conhecida como **_versão Monte Carlo_**. Essa é uma versão que prioriza velocidade. Como ela não checa caractere por caractere, o algoritmo consegue passar por todo o texto com maior facilidade. O nível de precisão nas respostas dependerá da complexidade da funçao hash utilizada. Logo, apesar dela poder fornecer uma resposta errada, é possível reduzir essa possibilidade.

### Pseudo-código da versão Monte Carlo

```powershell
 hash_padrao = calcula o hash do padrão procurado
 hash_value = calcula hash da janela inicial

 enquanto não chega ao final do texto:
    compara hash_value com hash_padrao.
        se forem iguais:
            adiciona o índice do início da janela em um array de matchings.
        se não forem:
            não faz nada.

    avança a janela em uma posição.
    hash_value = recalcula hash com rolling_hash
```

??? Checkpoint

Qual é a complexidade do Algoritmo de Rabin-Karp na versão Monte Carlo?

::: Gabarito

Como só percorremos um único looping, verificando o _hash value_, a versão Monte Carlo tem complexidade $O(n)$.

:::

???

Em contrapartida, podemos optar por uma versão mais segura, a **_versão Las Vegas_**. Além de checar o _hashing value_, ela confere o _matching_ entre os caracteres. No pior dos casos, ou seja, naquele em que os _hashing values_ dão match constantemente, ela tem complexidade $O(n \cdot m)$. De forma semelhante à Monte Carlo, ela pode tornar esses casos mais raros, utilizando uma função hash mais complexa.

### Pseudo-código da versão Las Vegas

```powershell
 hash_padrao = calcula o hash do padrão procurado
 hash_value = calcula hash da janela inicial

 enquanto não chega ao final do texto:
    compara hash_value com hash_padrao.
        se os hash_values são iguais:
            enquanto não chega ao final da janela:
                compara o caractere da janela com o caractere do padrão.
                    se forem iguais:
                        avança o índice da janela em um.
                    se não forem:
                        sai do looping.
                se todos os caracteres analisados foram iguais:
                    adiciona o índice do início da janela em um array de matchings.
        se os hash_values são diferentes:
            não faz nada.

    avança a janela em uma posição.
    hash_value = recalcula hash com rolling_hash
```

??? Checkpoint

Qual é a complexidade do Algoritmo de Rabin-Karp na versão Las Vegas?

::: Gabarito

Nesse caso, podemos identificar dois loopings: um para percorrer o texto e outro para conferir caractere por caractere.
No pior dos casos, em que acionaríamos o segundo looping a todo momento, o algoritmo teria complexidade $O(n \cdot m)$ - em que $n$ é o tamanho do texto e $m$ é o tamanho do padrão.
Mas, como estamos utilizando uma _função hash_ relativamente complexa, podemos reduzir bastante a probabilidade dessas colisões ocorrerem. Nesse contexto, podemos considerar apenas o looping mais externo, ou seja, na prática, esse algoritmo tem complexidade $O(n)$.

:::

???
