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

## Árvores 2-3
