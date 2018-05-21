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
