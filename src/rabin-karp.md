# Algoritmo de Rabin-Karp

## Algoritmo da força bruta

Talvez uma das maneiras mais intuitivas para buscar um determinado padrão dentro de um texto seja percorrê-lo por completo, analisando um conjunto de caracteres do mesmo tamanho do nosso padrão e checar letra por letra, se corresponde a ordem desejada. Caso uma das letras não corresponda, podemos avançar o intervalo da nossa janela de análise e, assim, prosseguir por todo o texto. Por exemplo, se quiséssemos contar as ocorrências de `md paga` em `md papagaio`, poderíamos fazer:

:brutus

??? Checkpoint

Analisando o comportamento da animação acima, o que você poderia deduzir a respeito da complexidade dessa estratégia de busca, no **pior dos casos**, ou seja, quando?

:::Gabarito

No pior dos casos, para cada _janela_ do texto comparada com o padrão, compararíamos todas as letras até chegar à última, ou seja, percorreríamos dois loopings de comparação, o que implicaria em um algoritmo com complexidade $O(n^2)$.

:::

???

Essa estratégia pode até parecer razoável para o exemplo anterior, mas imagine que fôssemos buscar as ocorrências da palavra `md batata` no livro `md Quincas Borba`, de Machado de Assis, quantas comparações letra a letra o algoritmo teria que fazer até que analisasse o livro por completo, procurando pelo padrão? Com certeza seria um número grande, mas boa parte delas seriam desnecessárias ou poderiam ter sido descartadas, caso adotássemos uma estratégia melhor.

Por ter uma complexidade quadrática, esse algoritmo está longe de ser uma boa alternativa para a busca de padrões textuais na vida real. Por enquanto, estamos desperdiçando nossos recursos fazendo comparações intermediárias que serão descartadas em alguns passos seguintes. Será que teria maneira conveniente para evitar isso? O que poderíamos fazer para melhorar nosso algoritmo e diminuir sua complexidade?

É aqui que começa a aparecer a genialidade do algoritmo de Rabin Karp.

## A ideia do algoritmo ingênuo

Imagine que cada palavra ou conjunto de caracteres tivesse uma _impressão digital numérica_, isto é, algum código numérico que a identificasse e que pudéssemos usá-la para compará-la com outras palavras do texto. Isso reduziria nosso trabalho a simplesmente buscar as digitais de cada janela e comparar com a digital do nosso padrão.

Resta-nos, agora, determinar uma regra para compor essa impressão digital numérica. Uma maneira simples de se fazer isso seria propor uma equivalência numérica para cada caractere, somar o valor de cada um e compor a identidade numérica da palavra. Para isso, podemos fazer uso da tabela ASCII simplificada, em que cada caractere corresponde a um número `md (A=65, B=66, ... ,Z=90)`.

// TODO: adicionar a tabela ASCII aqui

:ingenuo

Essa estratégia de gerar uma saída padronizada (_impressão digital numérica_) a partir de uma entrada qualquer (uma palavra qualquer: um conjunto de caracteres) é bastante utilizada no mundo da computação, em diversas formas diferentes. Ela é conhecida como **Função Hash**. Uma função hash é aquela que recebe um valor qualquer de entrada e devolve uma resposta em um formato padronizado. Essa saída é conhecida como **_hash value_**.

??? Checkpoint

No caso da animação anterior, em que aplicamos o _hashing_, considerando o **pior dos casos**, qual seria a complexidade desse método?

**Dica**: Pense em como você calcularia o _hash value_ de cada trecho analisado.

::: Gabarito

Para calcular o _hash_ de cada trecho analisado, precisamos sempre varrê-lo e incrementar o valor correspondente de cada letra. Assim, ainda que alguns casos a verificação entre o padrão e o trecho seja imediata (quando os _hashs_ são iguais), continuam sendo necessárias várias iterações letra a letra. Por tanto, ainda teríamos uma complexidade $O(n^2)$, tal qual no caso da força bruta.

:::

???

Então, essa ideia ainda parece insuficiente para otimizar o nosso algoritmo, pois continuamos percorrendo cada uma das strings para obter o seu _hash_.

??? Checkpoint

Que estratégia poderíamos utilizar para não ter que percorrer uma string ao longo de um texto todas as vezes para obter o seu _hash_?

**Dica:** Observe atentamente a animação anterior. O que acontece quando a janela _avança_ um caractere?

::: Gabarito

A cada avanço da janela sobre o texto, os termos do cálculo do _hash_ que mudam são os termos inicial e final (os demais não se alteram). Por tanto, em vez de percorremos toda a string a cada iteração, podemos apenas **subtrair** o _hash_ do caractere que saiu e **somar** o _hash_ do caractere entrante.

Por exemplo, ao sair de `md PAPA` ($hash=290$) para `md APAG`, o cálculo seria:

$$hash(APAG) = hash(PAPA) - hash(P) + hash(G)$$
$$hash(APAG) = 290 - 80 + 71 = 281$$

Veja a animação abaixo:

:rolling

:::

???

A ideia apresentada acima chama-se **_Rolling Hash_**, que permite calcular mais rapidamente o _hash value_ de um trecho a partir do _hash_ do valor anterior.

Antes de irmos de fato para o aguardado Algoritmo de Rabin-Karp, há mais um ponto a ser levantado. Você deve ter notado, na animação que exemplifica o _hash_, que houve um caso em que o _hash value_ da janela era igual ao do padrão, mas as duas strings eram diferentes uma da outra.

??? Checkpoint

No caso descrito acima, qual foi o "critério de desempate" para strings com _hashs_ iguais?

- Quais possíveis implicações isso pode ter em termos de desempenho do algoritmo?

::: Gabarito

Quando os _hashs_ resultam o mesmo, fez-se uma verificação caractere por caractere, para de fato validar ou não a igualdade.

A ocorrência de **_colisões_** (quando os _hashs_ são iguais sem necessariamente as strings serem) diminui a performance e aumenta (ou não reduz) a complexidade do nosso algoritmo, já que uma maior quantidade de conferências de strings será necessária ao longo de um texto.

:::

???

O último checkpoint reforça que podem ocorrer colisões ao longo de um texto. Assim, a construção de um algoritmo em cima da ideia de _Hashing_ deve considerar **reduzir a probabilidade dessas colisões**, o que implica em diminuir a quantidade de "varreduras completas" em cada string e acelerar o processo de busca como um todo.
