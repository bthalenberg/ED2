-----------------------------------------------------------------------------------------
# MAC0323 - Algoritmos e Estruturas de Dados II

## Prova 2: Conteúdo:

* BST
* ST 2-3
* ST Rubro-negra
* Hashing
* Separate chaining
* Linear probing
* R-way tries
* TST
* Compressão de dados
* Algoritmo de huffman

-----------------------------------------------------------------------------------------

## Árvores binárias de busca (BSTs)

* Referências: Árvores binárias de busca (PF); Binary Search Trees (S&W); slides (S&w).

### Definição

Árvores binárias são um subtipo de tabelas de símbolos, ou seja, um conjunto de objetos,
cada um dotado de uma chave e de um valor. São dotadas de duas operações: put e get.
BSTs são pensadas para que as operações sejam razoavelmente eficientes (há que se con-
siderar o trade-off: inserções muito rápidas tornam buscas muito lentas e vice-versa).

Cada nó de uma árvore binária tem quatro campos:

```
Key key;
Value val;
Node left, right;
```

Dizemos que x é **pai** de y se ```x.left == y || x.right == y```. Nesse caso, y é **filho**
de x. Uma **folha** é um nó sem filhos (```x.left == null && x.right == null```).
Analogamente, dizemos que p é **descendente** de r se pode ser alcançado pela iteração
dos comandos ```p = p.left``` e ```p = p.right```, em qualquer ordem. Um nó r e todos os
seus descendentes compõem uma árvore e r é dito **raiz** da árvore.

A **profundidade** de um nó de uma BT é o número de links no caminho da raiz até o nó.
A **altura** de uma BT é a profundidade do nó mais profundo. A altura de uma árvore
com n nós é um número entre lg(n) e n.

Uma árvore binária é dita **de busca** se para cada nó x:

* k.key é maior ou igual à chave de qualquer nó na subárvore esquerda de x;
* x.key é menor ou igual à chave de qualquer nó na subárvore direita de x.

Toda operação de busca ou inserção visita 1 + p nós, sendo p a profundidade do último
nó visitado.

Buscas com sucesso numa BST aleatória com n chaves requer cerca de 2 lg n comparações,
em média. O número esperado de nós visitados durante uma busca em uma BST aleatória
não passa de 3 lg n.

### Operações

#### get(key)

```
private Node get(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp < 0)
        return get(x.left, key);
    if (cmp > 0)
        return get(x.right, key);
    else return x;
}
```

#### put(key, val)

```
private Node put(Node x, Key key, Value val) {
    if (x == null)
        return new Node(key, val);
    int cmp = key.compareTo(x.key);
    if (cmp < 0)
        x.left = put(x.left, key, val);
    else if (cmp > 0)
        x.right = put(x.right, key, val);
    else x.val = val;
    return x;
}
```

### delete
```
// Remove o nó que tem a menor chave na subárvore cuja raiz é x e retorna a raiz
// da subárvore resultante
private Node deleteMin(Node x) {
    if (x.left == null) return x.right;
    x.left = deleteMin(x.left);
    return x;
}

private Node delete(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp < 0)
        x.left = delete(x.left, key);
    else if (cmp > 0)
        x.right = delete(x.right, key);
    else {
        if (x.right == null) return x.left;
        if (x.left == null) return x.right;
        Node t = x;
        x = min(t.right);
        x.right = deleteMin(t.right);
        x.left = t.left;
    }
    return x;
}
```

### Maneiras de varrer uma árvore

* _inorder traversal_: esquerda-raiz-direita. Visitamos a subárvore esquerda da raiz
em _inorder_, depois a raiz, depois a subárvore direita em _inorder_.
* _preorder traversal_: raiz-esquerda-direita. Visitamos a raiz, depois a subárvore
esquerda da raiz em _preorder_, depois a subárvore direita em _preorder_.
* _postorder traversal_: esquerda-direita-raiz. Visitamos a subárvore esquerda da raiz
em _postorder_, depois a subárvore direita em _postorder_, depois a raiz.

### Árvores balanceadas

Uma BT é **balanceada** se, em cada um de seus nós, as subárvores esquerda e direita
tiverem aproximadamente a mesma altura. Nesse caso, terá altura próxima de lg(n).
É interessante ter uma árvore balanceada pois o consumo de tempo dos algoritmos que
manipulam árvores binárias dependem frequentemente da altura da árvore.

O **comprimento interno** (= internal path length) de uma BT é a soma das profundidades
dos seus nós.

### Algoritmo A*

O algoritmo A busca encontrar uma sequência de passos (=caminho) que leva de um dado
nó inicial s a um destino t em um grafo. O grafo é tipicamente muito grande e muitas
vezes não pode ser dado explicitamente, e é fornecido através de um iterador que,
a partir de um dado nó, mostra seus vizinhos.

O A* é iterativo. No início de cada iteração temos uma partição dos nós em dois conjuntos:
**vistos** e **não vistos**. O primeiro conjunto subdivide-se em **vistos não examinados**
e **vistos examinados**. O primeiro desses subconjuntos é a chave do algoritmo. Esse
conjunto é mantido em uma fila priorizada.

#### Pseudocódigo

```
while (true) {
    // remova o primeiro elemento da fila
    u = vistos_nao_examinados.delMin();
    // examine u
    // chegamos no destino
    if (u == t) break;
    // visite todos os vizinhos de u
    for (Node v: u.viz()) {
        if (!vistos.contains(v)) {
            vistos.insert(v);
            vistos_nao_examinados.insert(v);
        }
    }
    // acabamos de examinar u
    vistos_examinados.insert(u)
}
```

## Árvores 2-3

* Referências: Árvores 2-3 (PF); Balanced Search Trees (S&W); slides (S&W).

### Definição

Uma árvore 2-3 busca resolver a questão de como manter uma BST aproximadamente
balanceada. Desejamos que a BST tenha altura próxima de lg n, sendo n o número
de nós, qualquer que seja a sequência de buscas e inserções. Árvores 2-3 são
a abstração de árvores rubro-negras, que veremos mais adiante.

Uma árvore 2-3 é:
* uma árvore vazia;
* ou um nó simples com 2 links: um link left e um right, cada um para uma árvore 2-3;
* ou um nó duplo com 3 links: um link left, um mid e um right, cada um para uma
árvore 2-3.

Dizemos que uma árvore 2-3 está perfeitamente balanceada se todos os links null estão
no mesmo nível. Toda árvore 2-3 de altura h tem no mínimo 2^{h+1} - 1 nós e no
máximo 3^{h+1} - 1 nós.

Uma árvore de busca 2-3 é uma árvore 2-3 que segue as propriedades normais de uma
árvore de busca. Especificamente, em nós duplos, o link mid fornece as chaves
que estão entre as duas chaves de um nó.

### Operações

Numa árvore 2-3 com n nós, busca e inserção nunca visitam mais que lg(n+1) nós.
Cada visita faz no máximo 2 comparações de chaves.

O processo de inserção consiste em procurar o nó (a partir do maior) em que o valor
se encaixaria. Transformar esse nó em um 2, 3 ou 4-nó. Nesse último caso,
espatifamos o nó (a chave do meio sobe de nível), e assim sucessivamente, até que
encontremos um nó que não necessite ser espatifado. Caso tenhamos subido até a
raiz e ainda assim não tenhamos encontrado tal nó, espatifamos a raíz, aumentando
a altura da árvore. Transformações locais preservam ordem e balanceamento.

## Árvores rubro-negras (red & black trees)

* Referências: BSTs rubro-negras (PF); Balanced Search Trees (S&W); slides (S&W).

### Definição

Uma BST rubro-negra é uma BST que simula uma árvore 2-3. Cada 3-nó da árvore 2-3
é representado por dois 2-nós ligados por um link rubro, ou seja, se os links
rubros forem desenhados horizontamente e depois contraídos, teremos uma árvore 2-3.
Nossas BSTs são **esquerdistas** pois os links rubros são sempre inclinados para a esquerda.

Uma BST rubro-negra é uma BST cujos links são negros e rubros e que satisfaz as
seguintes propriedades:
* links rubros se inclinam para a esquerda;
* nenhum nó incide em dois links rubros;
* balanceamento negro perfeito: todo caminho da raiz até um link null tem o mesmo
número de links negros.

O **balanceamento negro perfeito** vem do fato de que os links negros correspondem
aos links da árvore 2-3.

A **profundidade negra** de um nó x é o número de links negros no caminho da raiz
até x. A **altura negra** da árvore é o máximo da profundidade negra de todos os nós.

------------------------------------------------------------------------------------
_Uma observação de implementação_: No CLRS as árvores rubro-negras tem **nós**, e não
links, rubros e negros. Nós rubros são os referenciados por links rubros, e nós
negros são os referenciados por links negros. Isso tem a ver com a implementação de
fato da BST, pois é inconveniente armazenar a cor de um link na ED, e é mais simples
armazenar essa informação nos nós (isso também é feito em S&W, chamar o link de
rubro ou de negro é meramente didático).

A cor de um nó é a cor do único link que entra nele. A raíz é considerada negra.
------------------------------------------------------------------------------------

```
private class Node {
    Key key;
    Value val;
    Node left, right;
    int n; // número de nós na subárvore
    boolean color; // cor do link para este nó
}
```

### Operações

A operação de busca (= get()) para BSTs rubro-negras é exatamente igual ao das BSTs
comuns.

O código de inserção depende de operações de **rotação**. Durante uma operação de inserção, podemos ter, temporariamente, um link rubro inclinado para a direita ou dois links rubros incidindo no mesmo nó. Para corrigir isso, usamos rotações e inversões de cores, respectivamente.

#### Operações de correção

* **Rotação esquerda (ou horária)** em torno do nó h: o filho direito de h sobe e adota h como seu filho esquerdo.
* **Rotação direita (ou anti-horária)** em torno do nó h: o filho esquerdo de h sobe e adota h como seu filho direito.
* **Inversão de cores (flipping colors)**: corresponde a espatifar um 4-nó e subir a chave do meio para o nó pai.

```
private Node rotateLeft(Node h) {
    Node x = h.right;
    h.right = x.left;
    x.left = h;
    x.color = h.color;
    h.color = RED;
    x.n = h.n;
    h.n = 1 + size(h.left) + size(h.right);
    return x;
}

private Node rotateRight(Node h) {
    Node x = h.left;
    h.left = x.right;
    x.right = h;
    x.color = h.color;
    h.color = RED;
    x.n = h.n;
    h.n = 1 + size(h.left) + size(h.right);
    return x;
}

private void flipColors(Node h) {
    h.color = !h.color;
    h.left.color = !h.left.color;
    h.right.color = !h.right.color;
}

// Verifica invariante rubro-negro quando estamos voltando da recursão de inserção
private Node balance(Node h) {
    // verifica se o link rubro está para a esquerda (e arruma se for o caso)
    if (isRed(h.right))
        h = rotateLeft(h);
    // verifica se a ordem dos elementos no nó está correta (e arruma se for o caso)
    if (isRed(h.left) && isRed(h.left.left))
        h = rotateRight(h);
    // verifica se é 4=nó e espatifa
    if (isRed(h.left) && isRed(h.right))
        flipColors(h);
    h.n = size(h.left) + size(h.right) + 1;
    return h;
}
```

#### Inserção

```
private Node put(Node h, Key key, Value val) {
    if (h == null) return new Node(key, val, 1, RED);
    int cmp = key.compareTo(h.key);
    if (cmp < 0)
        h.left = put(h.left, key, val);
    else if (cmp > 0)
        h.right = put(h.right, key, val);
    else h.val = val;
    h = balance(h);
    return h;
}
```

#### Remoção

No caminho até a chave a ser removida, o algoritmo mantém a relação invariante
```o nó sendo examinado é um 3-nó ou um 4-nó (temporário)```.

No meio do caminho, sabemos que o nó corrente h é um 3-nó ou um 4-nó. Antes de
movermos para o nó mais à esquerda precisamos nos certificar que esse nó é um
3-nó ou um 4-nó. Desta forma, ao atingir a folha mais à esquerda, teremos chegado
a um 3-nó ou um 4-nó. Removendo o item mais à esquerda (delMin()), teremos um
2-nó ou 3-nó, e devemos voltar espatifando os 4-nós que restaram pelo caminho.

```
public void delMin() {
    if (r == null) return;
    if (!isRed(r.left) && !isRed(r.right))
        r.color = RED;
    r = delMin(r);
    if (!isEmpty()) r.color = BLACK;
}

private Node delMin(Node h) {
    if (h.left == null) return null;
    if (!isRed(h.left) && !isRed(h.left.left))
        h.moveRedLeft(h);
    h.left = delMin(h.left);
    return balance(h);
}

private Node moveRedLeft(Node h) {
    flipcolors(h);
    if (isRed(h.right.left)) {
        h.right = rotateRight(h.right);
        h = rotateLeft(h);
        flipColors(h);
    }
    return h;
}

private Node moveRedRight(Node h) {
    flipcolors(h);
    if (isRed(h.left.left)) {
        h = rotateRight(h);
        flipColors(h);
    }
    return h;
}

private Node delete(Node h, Key key) {
    if (key.compareTo(h.key) < 0) {
        if (!isRed(h.left) && !isRed(h.left.left))
            h = moveRedLeft(h);
        h.left = delete(h.left, key);
    }
    else {
        if (isRed(h.left))
            h = rotateRight(h);
        if (key.equals(h.key) && (h.right == null))
            return null;
        if (!isRed(h.right) && !isRed(h.right.left))
            h = moveRedRight(h);
        if (key.equals(h.key)) {
            Node x = min(h.right);
            h.key = x.key;
            h.val = x.val;
            h.right = delMin(h.right);
        }
        else h.right = delete(h.right, key);
    }
    return balance(h);
}
```
```

## Hashing

* Referências: Hashing (PF); Hash tables (S&W); slides (S&W); Hashing functions
(S&W); CLRS ch. 12; TAOP vol.3 ch. 6.4;

### Endereçamento direto

No endereçamento direto temos tabela indexada pelas chaves, uma posição para cada
possível índice. Cada posição armazena o valor correspondente a uma dada chave.
É uma técnica que funciona bem quando o universo de chaves é razoavelmente pequeno.
Em uma tabela de símbolos com endereçamento direto o consumo de tempo de get(),
put() e delete() é O(1).

Seus defeitos são:
* Em geral, chaves não são inteiros não-negativos pequenos.
* Desperdício de espaço: é possível que a maior parte da tabela fique vazia.

### Definição

Uma tabela de dispersão (=hash table) é uma maneira de organizar uma tabela de
símbolos. Consumo de tempo amortizado O(1).

* Universo de chaves: conjunto de todas as possíveis chaves.
* Chaves realmente usadas são, em geral, uma parte pequena do universo.
* A tabela terá forma st[0, ..., m-1], onde m é o tamanho da tabela.

Uma função de dispersão (= hash function) é uma maneira de mapear o universo de
chaves no conjunto de índices da tabela. A função de dispersão recebe uma chave
key e retorna um inteiro h(key) no intervalo 0, ..., m-1. O número h(key) é
o código de dispersão (= hash code) da chave.

As _desiderata_ de uma função de hashing são:

* Eficácia (calcula h(key) em O(1));
* Uniformidade (espalha bem as chaves pelo intervalo).

Uma função só é eficiente se espalha as chaves pelo intervalo de índices de maneira
razoavelmente uniforme.

* **Hipótese do hashing uniforme:** vamos supor que nossas funções de hashing
 distribuem as chaves pelo intervalo de inteiros 0, ..., m de maneira uniforme e
 independente.

Como funções injetoras são difíceis de encontrar, ainda que o tamanho da tabela
seja razoavelmente grande, teremos que lidar com colisões.

### Função de hashing modular

* **Método da divisão ou hash modular**: supondo que as chaves são inteiros positivos
podemos usar a função modular (resto da divisão por m):
* **Vantagens:** rápida, faz apenas uma divisão.
* **Desvantagem**: m deve ser bem escolhido. Um primo não muito perto de uma potência
de 2 parece ser uma boa escolha para m.

```
private int hash(int key) {
    return key % m;
}

// No caso de strings, podemos iterar sobre seus caracteres
private int hash(String key) {
    int h = 0;
    for (int i = 0; i < key.length(); i++)
        h = (31 * h + key.charAt(i)) % m;
    return h;
}
```

### Função de hashing multiplicativa

* escolha uma constante A, 0 < A < 1;
* multiplique key por A;
* extraia a parte fracional de key x A;
* multiplique a parte fracionária por m;
* o valor de hash é o chão dessa multiplicação

* **Desvantagem**: mais lenta que o hash modular;
* **Vantagem**: o valor de m não é crucial.

### Colisões

Dizemos que há uma colisão quando duas chaves diferentes são levadas no mesmo índice.
Há algumas maneiras de tratar colisões, dentre elas _separate chaining_, _linear
probing_ e _double hashing_.

#### Separate chaining (colisões por listas encadeadas)

Para cada índice h da tabela há uma lista encadeada que armazena todos os objetos
que a função de dispersão leva em h. Essa solução é muito boa se cada uma das
listas de colisão resultar curta.

Se o numero total de chaves usadas por n, o comprimento de cada lista deveria,
idealmente, estar próximo de _a_ = n/m. O valor _a_ é chamado de fator de carga da tabela.

Em uma tabela de hash encadeada com m listas e n chaves, se vale a hipótese do
hashing uniforme, a probabilidade de que o número de chaves em cada lista não passa
de _a_ = n/m multiplicado por uma pequena constante é muito próxima de 1. Uma solução elegante para manter o fator de carga pequeno é realizar
rehashing, ou seja, aumentar o tamanho da tabela se _a_ for maior do que determinado
valor.

```
public void put(Key key, Value val) {
    if (n >= 10*m) resize(2*m);
    int i = hash(key);
    if (!st[i].contains(key)) n++;
    st[i].put(key, val);
}

private void resize(int chains){
    HashST<Key, Value> t = new HashST<Key, Value>(chains);
    for (int i = 0; i < m; ++i) {
        for (Key key: st[i].keys())
            t.put(key, st[i].get(key));
    }
    this.m = t.m;
    this.n = t.n;
    this.st = t.st;
}
```

##### Consumo de tempo

Supondo que h(key) é computada em O(1), o tempo gasto pela inserção depende do
comprimento da lista st[h(key)]. O pior caso ocorre quando todas as n chaves vão para a mesma lista. Logo, o consumo de  tempo médio depende de quão bem a função de
 hashing distribui as chaves.

Em uma busca malsucedida, percorremos a lista st[h(key)] até o final. O comprimento
esperado dessa lista é _a_. Logo, o consumo de tempo médio de uma busca malsucedida
é O(1 + _a_).

Em uma busca bem sucedida, o número de chaves examinado é 1 mais o número de
 elementos na lista encadeada antes de key (elementos que foram inseridos depois
de key, já que sempre inserimos na cabeça da lista). Depois de muitas contas
(SPOILERS) concluimos que o tempo esperado de busca é O(1 + _a_).

Logo, o consumo das operações get, put e delete é O(n/m) = O(_a_). Se n <= cm para alguma constante c, ou seja, n = O(m), então _a_ é O(1).

### Open addressing e linear probing

Open addressing busca evitar o espaço extra usado por listas ligadas colocando todas
as chaves na tabela. O fator de carga _a_ é menor do que 1 (idealmente, _a_ <= 1/2). Estendemos a função de hash para ter o número da sondagem como segundo parâmetro, e
 a sequência de todas as sondagens h(key, 0), ..., h(key, m-1) deve ser uma permutação de 0, ..., m-1.

O método de open addressing mais simples é conhecido por sondagem linear (= linear
probing). Todos os itens são armazenados em um vetor e, quando ocorre uma colisão,
procuramos a próxima posição vaga do vetor. Quanto maior o fator de carga, mais tempo
as funções de busca e inserção vão consumir. Durante a busca, há três possibilidades:

* encontramos a chave e paramos a busca;
* posição não ocupada, paramos a busca;
* posição está ocupada e não é a chave, vamos para a pŕoxima posição.

```
public Value get(Key key) {
    for (int i = hash(key); keys[i] != null; i = (i + 1) % m)
        if (keys[i].equals(key)) return vals[i];
    return null;
}
```

#### Consumo de tempo

O consumo de tempo de uma busca com linear probing depende, no pior caso, do tamanho
do maior cluster. De acordo com a hipótese do hashing uniforme, o número médio de
sondagens em buscas bem sucedidas é aproximadamente (1 + 1/(1-a)).1/2 e, em buscas
mal sucedidas, é (1 + 1/(1-a)^2).1/2

### Consumo de memória para as diferentes STs:

| **método** | **espaço usado** para n itens |
| separate chaining | ~48n + 64m |
| linear probing | ~32n a ~128n |
| BSTs | ~56n |

-------------------------------------------------------------------------------------
**Uma observação importante**: note a diferença de interpretação do fator de carga.
Em:
* **separate chaining** o fator de carga é o número médio de itens por lista; ele
pode ser maior que 1.
* **open addressing** o fator de carga é a fração da tabela que está ocupada; ele
deve ser menor que 1.
-------------------------------------------------------------------------------------
