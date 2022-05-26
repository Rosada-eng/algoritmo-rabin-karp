# Algoritmo de Rabin-Karp

## Algoritmo da força bruta

Talvez uma das maneiras mais intuitivas para buscar um determinado padrão dentro de um texto seja percorrê-lo por completo, analisando um conjunto de caracteres do mesmo tamanho do nosso padrão e checar letra por letra, se corresponde a ordem desejada. Caso uma das letras não corresponda, podemos avançar o intervalo da nossa janela de análise e, assim, prosseguir por todo o texto. Por exemplo, se quiséssemos contar as ocorrências de `md paga` em `md papagaio`, poderíamos fazer:

:brutus

//TODO: colocar um pseudo-código em alto nível

??? Checkpoint

Analisando o comportamento da animação acima, o que você poderia deduzir a respeito da complexidade dessa estratégia de busca, no **pior dos casos**?

:::Gabarito

No pior dos casos, para cada _janela_ do texto comparada com o padrão, compararíamos todas as letras até chegar à última, ou seja, percorreríamos dois loopings de comparação, o que implicaria em um algoritmo com complexidade $O(n^2)$.

:::

???

Essa estratégia pode até parecer razoável para o exemplo anterior, mas imagine que fôssemos buscar as ocorrências da palavra `md batata` no livro `md Quincas Borba`, de Machado de Assis ("Ao vencedor, as batatas!"), quantas comparações letra a letra o algoritmo teria que fazer até que analisasse o livro por completo, procurando pelo padrão? Com certeza seria um número grande, mas boa parte delas seriam desnecessárias ou poderiam ter sido descartadas, caso adotássemos uma estratégia melhor.

Por ter uma complexidade quadrática, esse algoritmo está longe de ser uma boa alternativa para a busca de padrões textuais na vida real. Por enquanto, estamos desperdiçando nossos recursos fazendo comparações intermediárias que serão descartadas em alguns passos adiante (quando o fim da palavra não corresponde ao padrão). Será que teria uma maneira conveniente para evitar isso? O que poderíamos fazer para melhorar nosso algoritmo e diminuir sua complexidade?

É aqui que começa a aparecer a genialidade do algoritmo de Rabin Karp!

## O algoritmo ingênuo

Imagine que cada palavra ou conjunto de caracteres tivesse uma _impressão digital numérica_, isto é, algum código numérico que a identificasse e que pudéssemos usá-la para compará-la com outras palavras do texto. Isso reduziria nosso trabalho a simplesmente buscar as digitais de cada janela e comparar com a digital do nosso padrão.

Resta-nos, porém, determinar uma regra para compor essa impressão digital numérica. Uma maneira simples de se fazer isso seria propor uma equivalência numérica para cada caractere, somar o valor de cada um e compor a identidade numérica da palavra. Para isso, podemos fazer uso da tabela ASCII simplificada, em que cada caractere corresponde a um número `md (A=65, B=66, ... ,Z=90)`.

// TODO: adicionar a tabela ASCII aqui

:ingenuo

Essa estratégia de gerar uma saída padronizada (_impressão digital numérica_) a partir de uma entrada qualquer (uma palavra qualquer: um conjunto de caracteres) é bastante utilizada no mundo da computação, em diversas formas diferentes. Ela é conhecida como **Função Hash**. Uma função hash é aquela que recebe um valor qualquer de entrada e devolve uma resposta em um formato padronizado. Essa saída é conhecida como **_hash value_**.

//TODO: Antes de partir para a pergunta, colocar um pseudo-código em alto nível com o hash value

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
// TODO: Pseudo-código com o Algoritmo usando Rolling Hash

No código acima, fica claro que podem ocorrer colisões não desejadas ao longo da busca em um texto. Mas, no último checkpoint, também vimos que temos o controle sobre a **Função Hash**, que pode tornar as _digitais numéricas_ mais ou menos singulares, dificultando, assim, colisões indesejadas. Assim, a construção de um algoritmo em cima da ideia de _Hashing_ deve considerar **reduzir a probabilidade dessas colisões**.

Se conseguíssemos tonar os _hashing values_ suficientemente singulares, poderíamos simplesmente confiar no _matching_ entre os valores para julgar a igualdade ou não entre as strings. De fato, essa é uma versão aplicada do algoritmo de _Rabin-Karp_ e é conhecida como **_versão Monte Carlo_**. Essa é uma versão que prioriza velocidade. Como ela não checa caractere por caractere, o algoritmo consegue passar por todo o texto com maior facilidade. O nível de precisão nas respostas dependerá da complexidade da funçao hash utilizada. Logo, apesar dela poder fornecer uma resposta errada, é possível reduzir essa possibilidade.

// TODO: Pseudo-código da Versão Monte Carlo

Fica evidente, portanto, que a versão Monte Carlo tem complexidade $O(n)$.

Em contrapartida, podemos optar por uma versão mais segura, a **_versão Las Vegas_**. Além de checar o _hashing value_, ela confere o _matching_ entre os caracteres. No pior dos casos, ou seja, naquele em que os _hashing values_ dão match constantemente, ela tem complexidade $O(n \cdot m)$. De forma semelhante à Monte Carlo, ela pode tornar esses casos mais raros, utilizando uma função hash mais complexa.
