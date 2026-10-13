# **Algoritmos de Ordenação**

Ordenar dados é uma das tarefas mais comuns da computação: colocar notas em ordem decrescente, listar nomes em ordem alfabética, organizar medições por data, preparar dados para uma busca binária. Neste tópico, vamos implementar **na mão** os principais algoritmos de ordenação, entender como cada um funciona e comparar sua eficiência. No final, veremos como o próprio Python resolve o problema com `sorted()` e `sort()`.

> **Pré-requisitos:** listas, laços `for` e `while`, condicionais e funções (tópico anterior desta aula).

**Objetivos:**

- Entender o problema da ordenação e seus conceitos-chave (comparações, trocas, estabilidade, ordenação *in-place*).
- Implementar *Bubble Sort*, *Selection Sort*, *Insertion Sort*, *Merge Sort* e *Quick Sort*.
- Conhecer *Heap Sort* e *Counting Sort*.
- Interpretar a complexidade de tempo em notação *Big-O*.
- Medir o tempo de execução dos algoritmos.
- Usar `sorted()` e `list.sort()` com os parâmetros `key` e `reverse`.

---

## **1. O Problema da Ordenação**

**Entrada:** uma lista `[a₁, a₂, ..., aₙ]`.
**Saída:** uma permutação da lista tal que `a₁ ≤ a₂ ≤ ... ≤ aₙ`.

```
Entrada:  [5, 2, 9, 1, 7]
Saída:    [1, 2, 5, 7, 9]
```

### **1.1 Conceitos importantes**

| Conceito             | Significado                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------------- |
| **Comparação**       | Verificar se `a < b`. É a operação básica da maioria dos algoritmos.                                    |
| **Troca**            | Permutar a posição de dois elementos.                                                                   |
| **In-place**         | Ordena usando pouca memória extra (além de algumas variáveis), modificando a própria lista.             |
| **Estável**          | Elementos **iguais** mantêm a ordem relativa que tinham na entrada.                                     |
| **Adaptativo**       | Fica mais rápido quando a lista já está parcialmente ordenada.                                          |
| **Complexidade**     | Como o tempo (ou a memória) cresce em função do tamanho `n` da lista.                                   |

**Exemplo de estabilidade:** ordenar alunos por nota.

```python
alunos = [("Ana", 8), ("Bruno", 7), ("Carla", 8), ("Davi", 7)]
```

Uma ordenação **estável** por nota (crescente) produz:

```python
[("Bruno", 7), ("Davi", 7), ("Ana", 8), ("Carla", 8)]
```

Bruno continua antes de Davi, e Ana antes de Carla, como na entrada. Uma ordenação **instável** poderia inverter essas posições.

### **1.2 Troca de elementos em Python**

Em C, trocar dois valores exige uma variável auxiliar. Em Python, usamos o desempacotamento de tuplas:

```python
v = [10, 20, 30]
v[0], v[2] = v[2], v[0]
print(v)   # [30, 20, 10]
```

### **1.3 Notação Big-O (noção intuitiva)**

Descreve como o número de operações cresce com `n`:

| Notação      | Nome           | Se `n` dobra, o tempo...   | Exemplo                      |
| ------------ | -------------- | -------------------------- | ---------------------------- |
| `O(1)`       | Constante      | não muda                   | Acessar `v[i]`               |
| `O(log n)`   | Logarítmica    | aumenta uma constante      | Busca binária                |
| `O(n)`       | Linear         | dobra                      | Percorrer a lista            |
| `O(n log n)` | Linearítmica   | pouco mais que dobra       | Merge Sort, Quick Sort       |
| `O(n²)`      | Quadrática     | **quadruplica**            | Bubble, Selection, Insertion |

Para `n = 1.000.000`, um algoritmo `O(n²)` faz da ordem de 10¹² operações, enquanto um `O(n log n)` faz cerca de 2·10⁷. A diferença é enorme!

---

## **2. Bubble Sort (Ordenação por Bolhas)**

**Ideia:** percorra a lista comparando **pares adjacentes** e troque-os se estiverem fora de ordem. A cada passada, o maior elemento "sobe como uma bolha" até a sua posição final, no fim da lista.

**Execução com `[5, 3, 8, 1]`:**

```
Passada 1: [5,3,8,1] → [3,5,8,1] → [3,5,8,1] → [3,5,1,8]
Passada 2: [3,5,1,8] → [3,5,1,8] → [3,1,5,8]
Passada 3: [3,1,5,8] → [1,3,5,8]
Resultado: [1, 3, 5, 8]
```

**Implementação:**

```python
def bubble_sort(lista):
    """Ordena em ordem crescente com Bubble Sort e retorna uma nova lista."""
    v = lista.copy()          # não altera a lista original
    n = len(v)

    for i in range(n - 1):
        trocou = False
        for j in range(n - 1 - i):
            if v[j] > v[j + 1]:
                v[j], v[j + 1] = v[j + 1], v[j]
                trocou = True
        if not trocou:        # nenhuma troca: a lista já está ordenada
            break

    return v

print(bubble_sort([5, 3, 8, 1]))   # [1, 3, 5, 8]
```

**Observações:**

- O `-i` no laço interno evita comparar os elementos que já estão nas posições finais.
- A variável `trocou` é uma otimização: se uma passada completa não fez trocas, a lista já está ordenada.
- **Complexidade:** `O(n²)` no pior caso e no caso médio; `O(n)` se a lista já estiver ordenada (com a otimização).
- **Estável:** sim. **In-place:** sim.
- Útil para **ensino**; raramente usado na prática.

---

## **3. Selection Sort (Ordenação por Seleção)**

**Ideia:** a cada passo, **selecione o menor elemento** da parte ainda não ordenada e coloque-o na próxima posição.

**Execução com `[64, 25, 12, 22, 11]`:**

```
i=0: menor = 11  → [11, 25, 12, 22, 64]
i=1: menor = 12  → [11, 12, 25, 22, 64]
i=2: menor = 22  → [11, 12, 22, 25, 64]
i=3: menor = 25  → [11, 12, 22, 25, 64]
```

**Implementação:**

```python
def selection_sort(lista):
    """Ordena em ordem crescente com Selection Sort."""
    v = lista.copy()
    n = len(v)

    for i in range(n - 1):
        indice_menor = i
        for j in range(i + 1, n):
            if v[j] < v[indice_menor]:
                indice_menor = j
        v[i], v[indice_menor] = v[indice_menor], v[i]

    return v

print(selection_sort([64, 25, 12, 22, 11]))   # [11, 12, 22, 25, 64]
```

**Observações:**

- Faz **poucas trocas** (no máximo `n − 1`), o que é vantajoso quando trocar é caro.
- **Complexidade:** `O(n²)` em **todos** os casos (mesmo com a lista já ordenada, ele compara tudo).
- **Estável:** não (a troca pode "furar a fila" de elementos iguais). **In-place:** sim.

---

## **4. Insertion Sort (Ordenação por Inserção)**

**Ideia:** é como **ordenar cartas na mão**. Mantemos uma parte esquerda já ordenada e inserimos cada novo elemento na posição correta dentro dela.

**Execução com `[5, 2, 4, 6, 1, 3]`:**

```
Início:       [5 | 2, 4, 6, 1, 3]
Insere 2:     [2, 5 | 4, 6, 1, 3]
Insere 4:     [2, 4, 5 | 6, 1, 3]
Insere 6:     [2, 4, 5, 6 | 1, 3]
Insere 1:     [1, 2, 4, 5, 6 | 3]
Insere 3:     [1, 2, 3, 4, 5, 6]
```

**Implementação:**

```python
def insertion_sort(lista):
    """Ordena em ordem crescente com Insertion Sort."""
    v = lista.copy()

    for i in range(1, len(v)):
        chave = v[i]
        j = i - 1

        # desloca para a direita os elementos maiores que a chave
        while j >= 0 and v[j] > chave:
            v[j + 1] = v[j]
            j -= 1

        v[j + 1] = chave

    return v

print(insertion_sort([5, 2, 4, 6, 1, 3]))   # [1, 2, 3, 4, 5, 6]
```

**Observações:**

- **Complexidade:** `O(n²)` no pior caso e no caso médio; `O(n)` quando a lista já está (quase) ordenada.
- **Estável:** sim. **In-place:** sim. **Adaptativo:** sim.
- Excelente para **listas pequenas** ou **quase ordenadas**. Por isso, algoritmos sofisticados (inclusive o do Python) o utilizam internamente para pequenos trechos.

---

## **5. Merge Sort (Ordenação por Intercalação)**

**Ideia:** *dividir para conquistar*.

1. **Divida** a lista ao meio.
2. **Ordene recursivamente** cada metade.
3. **Intercale** (*merge*) as duas metades ordenadas em uma lista ordenada.

**Esquema para `[38, 27, 43, 3]`:**

```
            [38, 27, 43, 3]
             /            \
       [38, 27]          [43, 3]
        /    \            /    \
     [38]   [27]       [43]    [3]
        \    /            \    /
       [27, 38]          [3, 43]
             \            /
          [3, 27, 38, 43]
```

**Implementação:**

```python
def intercalar(esquerda, direita):
    """Junta duas listas já ordenadas em uma única lista ordenada."""
    resultado = []
    i = j = 0

    while i < len(esquerda) and j < len(direita):
        if esquerda[i] <= direita[j]:      # "<=" garante a estabilidade
            resultado.append(esquerda[i])
            i += 1
        else:
            resultado.append(direita[j])
            j += 1

    resultado.extend(esquerda[i:])         # sobras (apenas uma das duas não é vazia)
    resultado.extend(direita[j:])
    return resultado


def merge_sort(lista):
    """Ordena em ordem crescente com Merge Sort."""
    if len(lista) <= 1:                    # caso base
        return lista.copy()

    meio = len(lista) // 2
    esquerda = merge_sort(lista[:meio])
    direita = merge_sort(lista[meio:])
    return intercalar(esquerda, direita)

print(merge_sort([38, 27, 43, 3, 9, 82, 10]))   # [3, 9, 10, 27, 38, 43, 82]
```

**Observações:**

- **Complexidade:** `O(n log n)` em **todos** os casos: previsível e eficiente.
- **Memória extra:** `O(n)` (cria listas auxiliares).
- **Estável:** sim. **In-place:** não.
- Muito usado para grandes volumes de dados e para ordenação em disco (dados que não cabem na memória).

---

## **6. Quick Sort**

**Ideia:** também é *dividir para conquistar*.

1. Escolha um elemento como **pivô**.
2. **Particione** a lista: elementos **menores** que o pivô de um lado, **maiores** do outro.
3. **Ordene recursivamente** cada lado.

### **6.1 Versão simples (didática)**

```python
def quick_sort(lista):
    """Quick Sort simples (usa listas auxiliares)."""
    if len(lista) <= 1:
        return lista.copy()

    pivo = lista[len(lista) // 2]
    menores = [x for x in lista if x < pivo]
    iguais  = [x for x in lista if x == pivo]
    maiores = [x for x in lista if x > pivo]

    return quick_sort(menores) + iguais + quick_sort(maiores)

print(quick_sort([10, 7, 8, 9, 1, 5]))   # [1, 5, 7, 8, 9, 10]
```

Esta versão é curta e fácil de entender, mas usa memória extra.

### **6.2 Versão *in-place* (esquema de Lomuto)**

```python
def particionar(v, inicio, fim):
    """Usa v[fim] como pivô e retorna a posição final dele."""
    pivo = v[fim]
    i = inicio - 1

    for j in range(inicio, fim):
        if v[j] <= pivo:
            i += 1
            v[i], v[j] = v[j], v[i]

    v[i + 1], v[fim] = v[fim], v[i + 1]
    return i + 1


def quick_sort_inplace(v, inicio=0, fim=None):
    """Ordena a própria lista v (modifica a lista original)."""
    if fim is None:
        fim = len(v) - 1

    if inicio < fim:
        p = particionar(v, inicio, fim)
        quick_sort_inplace(v, inicio, p - 1)
        quick_sort_inplace(v, p + 1, fim)

dados = [10, 7, 8, 9, 1, 5]
quick_sort_inplace(dados)
print(dados)   # [1, 5, 7, 8, 9, 10]
```

**Observações:**

- **Complexidade:** `O(n log n)` no caso médio; `O(n²)` no pior caso (por exemplo, quando o pivô é sempre o menor ou o maior elemento, como na versão *in-place* acima aplicada a uma lista já ordenada).
- A escolha do pivô importa: pivô central ou **aleatório** (`random.choice`) reduz a chance do pior caso.
- **Estável:** não. **In-place:** sim (na versão 6.2).
- Na prática, é um dos algoritmos mais rápidos para listas na memória.
- Cuidado com listas grandes já ordenadas na versão 6.2: a recursão pode estourar o limite (`RecursionError`).

---

## **7. Outros Algoritmos (Visão Geral)**

### **7.1 Heap Sort**

Usa uma estrutura chamada **heap** (árvore binária em que o pai é sempre menor ou igual aos filhos, no caso do *min-heap*). O Python fornece o módulo `heapq`:

```python
import heapq

def heap_sort(lista):
    """Heap Sort usando o módulo heapq (min-heap)."""
    heap = lista.copy()
    heapq.heapify(heap)                       # transforma em heap em O(n)
    return [heapq.heappop(heap) for _ in range(len(heap))]

print(heap_sort([4, 10, 3, 5, 1]))   # [1, 3, 4, 5, 10]
```

- **Complexidade:** `O(n log n)` em todos os casos.
- Boa escolha quando precisamos apenas dos **k menores/maiores** elementos: `heapq.nsmallest(3, lista)` e `heapq.nlargest(3, lista)`.

### **7.2 Counting Sort (Ordenação por Contagem)**

**Não usa comparações!** Conta quantas vezes cada valor aparece. Funciona para **inteiros não negativos** em um intervalo pequeno.

```python
def counting_sort(lista):
    """Ordena inteiros não negativos por contagem."""
    if not lista:
        return []

    maior = max(lista)
    contagem = [0] * (maior + 1)

    for x in lista:
        contagem[x] += 1

    resultado = []
    for valor, qtd in enumerate(contagem):
        resultado.extend([valor] * qtd)

    return resultado

print(counting_sort([4, 2, 2, 8, 3, 3, 1]))   # [1, 2, 2, 3, 3, 4, 8]
```

- **Complexidade:** `O(n + k)`, em que `k` é o maior valor. Muito rápido quando `k` é pequeno (por exemplo, notas de 0 a 10), mas inviável se `k` for gigantesco.

---

## **8. Comparação dos Algoritmos**

| Algoritmo      | Melhor caso  | Caso médio   | Pior caso    | Memória extra | Estável | In-place |
| -------------- | ------------ | ------------ | ------------ | ------------- | ------- | -------- |
| Bubble Sort    | `O(n)`*      | `O(n²)`      | `O(n²)`      | `O(1)`        | Sim     | Sim      |
| Selection Sort | `O(n²)`      | `O(n²)`      | `O(n²)`      | `O(1)`        | Não     | Sim      |
| Insertion Sort | `O(n)`       | `O(n²)`      | `O(n²)`      | `O(1)`        | Sim     | Sim      |
| Merge Sort     | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(n)`        | Sim     | Não      |
| Quick Sort     | `O(n log n)` | `O(n log n)` | `O(n²)`      | `O(log n)`    | Não     | Sim      |
| Heap Sort      | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(1)`**      | Não     | Sim**    |
| Counting Sort  | `O(n + k)`   | `O(n + k)`   | `O(n + k)`   | `O(k)`        | —       | Não      |
| Timsort (Python)| `O(n)`      | `O(n log n)` | `O(n log n)` | `O(n)`        | Sim     | Não      |

\* Com a otimização da variável `trocou`.
\*\* No Heap Sort clássico (com o heap montado dentro da própria lista). A versão com `heapq` acima copia a lista e usa `O(n)` de memória extra.

**Como escolher (regra prática):**

- **Listas pequenas ou quase ordenadas:** Insertion Sort.
- **Garantia de desempenho e estabilidade:** Merge Sort.
- **Desempenho médio excelente em memória:** Quick Sort.
- **Valores inteiros em faixa pequena:** Counting Sort.
- **No dia a dia:** use o `sorted()` / `sort()` do Python (seção 10).

---

## **9. Medindo o Desempenho**

Podemos verificar na prática a diferença entre `O(n²)` e `O(n log n)`:

```python
import random
import time

def medir(algoritmo, dados):
    inicio = time.perf_counter()
    algoritmo(dados)
    return time.perf_counter() - inicio


algoritmos = {
    "Bubble":    bubble_sort,
    "Selection": selection_sort,
    "Insertion": insertion_sort,
    "Merge":     merge_sort,
    "Quick":     quick_sort,
    "sorted()":  sorted,
}

for n in (500, 1000, 2000, 4000):
    dados = [random.randint(0, 100000) for _ in range(n)]
    print(f"\nn = {n}")
    for nome, funcao in algoritmos.items():
        print(f"  {nome:10s}: {medir(funcao, dados):.5f} s")
```

**O que observar:**

- Quando `n` **dobra**, os algoritmos `O(n²)` levam cerca de **4 vezes** mais tempo.
- Merge e Quick crescem muito mais devagar.
- O `sorted()` do Python é o mais rápido, pois é implementado em C.

> Os tempos exatos variam conforme o computador; o que importa é a **tendência** de crescimento.

### **9.1 Contando comparações**

Outra forma de analisar um algoritmo é contar as operações:

```python
def bubble_sort_contando(lista):
    v = lista.copy()
    comparacoes = 0
    n = len(v)

    for i in range(n - 1):
        trocou = False
        for j in range(n - 1 - i):
            comparacoes += 1
            if v[j] > v[j + 1]:
                v[j], v[j + 1] = v[j + 1], v[j]
                trocou = True
        if not trocou:
            break

    return v, comparacoes

_, c1 = bubble_sort_contando(list(range(100)))          # já ordenada
_, c2 = bubble_sort_contando(list(range(100, 0, -1)))   # ordem inversa
print(c1, c2)    # 99 4950
```

Para `n = 100`: a lista ordenada precisa de apenas 99 comparações, enquanto a invertida precisa de `n(n−1)/2 = 4950`.

### **9.2 Verificando se está ordenada**

```python
def esta_ordenada(v):
    return all(v[i] <= v[i + 1] for i in range(len(v) - 1))

import random
teste = [random.randint(0, 50) for _ in range(20)]
assert esta_ordenada(merge_sort(teste))
assert merge_sort(teste) == sorted(teste)
print("Ordenação correta!")
```

> **Boa prática:** compare sempre o seu algoritmo com o `sorted()` em várias listas aleatórias, inclusive vazias, com um elemento e com repetidos.

---

## **10. Ordenação no Python: `sorted()` e `sort()`**

O Python possui ordenação embutida, usando o algoritmo **Timsort** (híbrido de Merge Sort e Insertion Sort), que é **estável**, `O(n log n)` no pior caso e `O(n)` em listas quase ordenadas.

| Forma             | Comportamento                                             |
| ----------------- | --------------------------------------------------------- |
| `sorted(seq)`     | Retorna uma **nova lista** ordenada; funciona com qualquer sequência |
| `lista.sort()`    | Ordena a **própria lista** (*in-place*) e retorna `None`  |

```python
numeros = [5, 2, 9, 1, 7]

print(sorted(numeros))                  # [1, 2, 5, 7, 9]
print(sorted(numeros, reverse=True))    # [9, 7, 5, 2, 1]
print(numeros)                          # [5, 2, 9, 1, 7]  (original intacto)

numeros.sort()
print(numeros)                          # [1, 2, 5, 7, 9]

print(sorted("python"))                 # ['h', 'n', 'o', 'p', 't', 'y']
print(sorted((3, 1, 2)))                # [1, 2, 3]
print(sorted({"b": 2, "a": 1}))         # ['a', 'b']  (ordena as chaves)
```

### **10.1 O parâmetro `key`**

Define uma função aplicada a cada elemento **antes da comparação**:

```python
palavras = ["banana", "kiwi", "maçã", "abacaxi"]

print(sorted(palavras))                  # ordem alfabética
print(sorted(palavras, key=len))         # por tamanho: ['kiwi', 'maçã', 'banana', 'abacaxi']
print(sorted(palavras, key=str.lower))   # ignora maiúsculas/minúsculas

numeros = [-5, 3, -1, 8, -9]
print(sorted(numeros, key=abs))          # [-1, 3, -5, 8, -9]
```

### **10.2 Ordenando registros**

```python
alunos = [
    {"nome": "Ana",   "nota": 8.5, "idade": 20},
    {"nome": "Bruno", "nota": 7.0, "idade": 22},
    {"nome": "Carla", "nota": 8.5, "idade": 21},
]

# Por nota, decrescente
por_nota = sorted(alunos, key=lambda a: a["nota"], reverse=True)

# Por nota decrescente e, em caso de empate, por nome crescente
ranking = sorted(alunos, key=lambda a: (-a["nota"], a["nome"]))

for a in ranking:
    print(a["nome"], a["nota"])
# Ana 8.5
# Carla 8.5
# Bruno 7.0
```

Com tuplas:

```python
pontos = [(2, 3), (1, 5), (2, 1), (1, 2)]
print(sorted(pontos))                          # [(1, 2), (1, 5), (2, 1), (2, 3)]
print(sorted(pontos, key=lambda p: p[1]))      # pela 2ª coordenada
```

### **10.3 Ordenação estável em múltiplos critérios**

Como o `sort` é estável, podemos ordenar em etapas, do critério **menos** importante para o **mais** importante:

```python
alunos.sort(key=lambda a: a["nome"])                    # 1º: nome
alunos.sort(key=lambda a: a["nota"], reverse=True)      # 2º: nota (mantém nome nos empates)
```

### **10.4 Outras funções úteis**

```python
import heapq

v = [8, 3, 10, 1, 7]

print(min(v), max(v))                  # 1 10
print(heapq.nsmallest(2, v))           # [1, 3]
print(heapq.nlargest(2, v))            # [10, 8]
print(list(reversed(sorted(v))))       # [10, 8, 7, 3, 1]
```

> **Nota:** Listas com tipos misturados e incomparáveis geram erro: `sorted([3, "a", 1])` → `TypeError`.

---

## **11. Ordenação e Busca Binária**

Uma das grandes vantagens de ter uma lista **ordenada** é poder realizar a **busca binária**, que encontra um elemento em `O(log n)` em vez de `O(n)`:

```python
def busca_binaria(v, alvo):
    """Retorna a posição de alvo na lista ordenada v, ou -1 se não existir."""
    inicio, fim = 0, len(v) - 1

    while inicio <= fim:
        meio = (inicio + fim) // 2
        if v[meio] == alvo:
            return meio
        elif v[meio] < alvo:
            inicio = meio + 1
        else:
            fim = meio - 1

    return -1

dados = sorted([42, 7, 19, 3, 88, 56, 23])
print(dados)                       # [3, 7, 19, 23, 42, 56, 88]
print(busca_binaria(dados, 42))    # 4
print(busca_binaria(dados, 50))    # -1
```

Para uma lista de 1 milhão de elementos, a busca binária faz no máximo cerca de 20 comparações! O módulo `bisect` da biblioteca padrão já implementa isso (`bisect_left`, `insort`).

---

## **12. Erros Comuns**

| Erro                                                      | Explicação / Correção                                                          |
| --------------------------------------------------------- | ------------------------------------------------------------------------------ |
| `x = lista.sort()`                                        | `sort()` devolve `None`. Use `x = sorted(lista)` ou chame `lista.sort()` sozinho |
| Modificar a lista original sem querer                     | Faça `v = lista.copy()` no início da função                                    |
| Laço interno com limites errados (`range(n)` em vez de `range(n-1)`) | `IndexError` ao acessar `v[j + 1]`                                    |
| Esquecer o caso base na recursão (Merge/Quick)            | `RecursionError`                                                               |
| Usar `<` em vez de `<=` na intercalação                   | Perde a estabilidade do Merge Sort                                             |
| Contar com `sorted()` para tipos incomparáveis            | `TypeError`                                                                    |
| Ordenar strings numéricas: `sorted(["10", "9", "2"])`     | Resultado `['10', '2', '9']`; converta com `key=int`                           |

---

## **Resumo**

- **Ordenar** é reorganizar os elementos segundo um critério; os conceitos-chave são comparação, troca, estabilidade e *in-place*.
- **Bubble, Selection e Insertion Sort** são simples, mas `O(n²)`; o Insertion Sort é ótimo para listas pequenas ou quase ordenadas.
- **Merge Sort** e **Quick Sort** usam *dividir para conquistar* e têm `O(n log n)` (o Quick Sort pode degradar para `O(n²)` no pior caso).
- **Counting Sort** não compara elementos e é muito eficiente para inteiros em faixas pequenas.
- Em Python, prefira `sorted()` e `list.sort()` (**Timsort**, estável e rápido), com `key` e `reverse` para critérios personalizados.
- Uma lista ordenada permite **busca binária** em `O(log n)`.
- Implementar os algoritmos manualmente ajuda a entender **como** funcionam e **por que** uns são mais rápidos que outros.

---

## **Referências**

- [Documentação: Como Ordenar (Sorting HOWTO)](https://docs.python.org/pt-br/3/howto/sorting.html)
- [Documentação: módulo `heapq`](https://docs.python.org/pt-br/3/library/heapq.html)
- [Documentação: módulo `bisect`](https://docs.python.org/pt-br/3/library/bisect.html)
- CORMEN, T. H. et al. *Algoritmos: Teoria e Prática*. Elsevier.