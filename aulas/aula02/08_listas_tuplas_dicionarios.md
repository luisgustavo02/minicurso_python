# **Listas, Tuplas e Dicionários**

Referência: [python.org: typesseq-list](https://docs.python.org/3/builtins/stdtypes.html#typesseq-list), [python.org: more-on-lists](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists), [python.org: tuples-and-sequences](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences), [python.org - dictionaries](https://docs.python.org/3/tutorial/datastructures.html#dictionaries), [python.org: sets](https://docs.python.org/3/tutorial/datastructures.html#sets)

Até aqui, cada variável guardava **um único valor**. Muitas vezes precisamos armazenar **coleções** de dados: as notas de uma turma, as coordenadas de um ponto, os dados de um aluno. O Python oferece estruturas de dados embutidas para isso. Nesta seção veremos as três mais importantes.

| Estrutura   | Sintaxe        | Ordenada | Mutável | Permite repetidos       |
| ----------- | -------------- | -------- | ------- | ----------------------- |
| **Lista**   | `[1, 2, 3]`    | Sim      | Sim     | Sim                     |
| **Tupla**   | `(1, 2, 3)`    | Sim      | **Não** | Sim                     |
| **Dicionário** | `{"a": 1}`  | Sim (ordem de inserção) | Sim | Chaves únicas |

**Objetivos:**

- Criar e manipular listas, tuplas e dicionários.
- Acessar elementos por índice, fatiamento e chave.
- Usar os principais métodos de cada estrutura.
- Entender mutabilidade e cópia de listas.
- Escolher a estrutura adequada para cada problema.

---

## **1. Listas (`list`)**

Uma **lista** é uma coleção **ordenada** e **mutável** que pode conter elementos de tipos diferentes.

```python
notas = [7.5, 8.0, 6.5, 9.0]
nomes = ["Ana", "Bruno", "Carla"]
misto = [1, "texto", 3.14, True, [1, 2]]
vazia = []
outra_vazia = list()
```

### **1.1 Acessando elementos (índices)**

Os índices começam em **0**. Índices negativos contam a partir do final.

```python
frutas =    ["maçã", "banana", "uva", "manga"]
#  índice:      0        1       2       3
#  negativo:   -4       -3      -2      -1

print(frutas[0])
print(frutas[2])
print(frutas[-1])
print(frutas[-2])
print(frutas[10])
```

### **1.2 Fatiamento (*slicing*)**

Sintaxe: `lista[início:fim:passo]` — o `início` é **incluído** e o `fim` é **excluído**.

```python
n = [10, 20, 30, 40, 50, 60]

print(n[1:4])
print(n[:3])
print(n[3:])
print(n[::2])
print(n[::-1])
print(n[-3:])
```

### **1.3 Modificando elementos**

Listas são **mutáveis**: podemos alterar seu conteúdo depois de criadas.

```python
notas = [7.0, 8.0, 6.0]
notas[1] = 9.5
print(notas)

notas[0:2] = [5.0, 5.5]
print(notas)
```

### **1.4 Principais métodos e funções**

| Operação                | Descrição                                          |
| ----------------------- | -------------------------------------------------- |
| `len(lista)`            | Quantidade de elementos                            |
| `lista.append(x)`       | Adiciona `x` ao final                              |
| `lista.insert(i, x)`    | Insere `x` na posição `i`                          |
| `lista.extend(outra)`   | Adiciona todos os elementos de `outra`             |
| `lista.remove(x)`       | Remove a primeira ocorrência de `x`                |
| `lista.pop()`           | Remove e retorna o último elemento                 |
| `lista.pop(i)`          | Remove e retorna o elemento da posição `i`         |
| `del lista[i]`          | Remove o elemento da posição `i`                   |
| `lista.index(x)`        | Posição da primeira ocorrência de `x`              |
| `lista.count(x)`        | Quantas vezes `x` aparece                          |
| `lista.sort()`          | Ordena a própria lista (crescente)                 |
| `sorted(lista)`         | Retorna uma **nova** lista ordenada                |
| `lista.reverse()`       | Inverte a própria lista                            |
| `lista.copy()`          | Cria uma cópia (rasa) da lista                     |
| `lista.clear()`         | Remove todos os elementos                          |
| `min(l)`, `max(l)`, `sum(l)` | Menor, maior e soma dos elementos             |

```python
numeros = [4, 2, 9]

numeros.append(7)
print(numeros)

numeros.insert(1, 100)
print(numeros)

numeros.extend([5, 5])
print(numeros)

numeros.remove(100)
print(numeros)

ultimo = numeros.pop()
print(numeros, ultimo)

print(numeros.count(5))
print(numeros.index(9))

numeros.sort()
numeros.sort(reverse=True)

print(len(numeros), min(numeros), max(numeros), sum(numeros))
print(sum(numeros) / len(numeros))
```

> **Atenção:** `sort()` e `reverse()` **modificam** a lista e retornam `None`. Escrever `x = lista.sort()` faz `x` valer `None`!

### **1.5 Operações com listas**

```python
a = [1, 2, 3]
b = [4, 5]

print(a + b)
print(a * 2)
print(2 in a)
print(len(a + b))
```

### **1.6 Cuidado: atribuição não copia a lista!**

```python
a = [1, 2, 3]
b = a            # b aponta para a MESMA lista
b.append(4)
print(a)

c = a.copy()     # Cópia independente
c.append(99)
print(a)
print(c)
```

### **1.7 Percorrendo uma lista**

Veremos os laços em detalhes no tópico *Estruturas de Repetição*; por ora, a forma mais comum é:

```python
for fruta in ["maçã", "banana", "uva"]:
    print(fruta)
```

### **1.8 Compreensão de listas (*list comprehension*)**

Uma forma compacta de criar listas a partir de outras:

```python
quadrados = [x ** 2 for x in range(1, 6)]
print(quadrados)

pares = [x for x in range(10) if x % 2 == 0]
print(pares)
```

### **1.9 Listas e textos**

```python
frase = "python é divertido"
palavras = frase.split()
print(" ".join(palavras))
print("-".join(["a", "b", "c"]))
print(list("abc"))
```

**Exemplo — Lendo vários números em uma linha:**

```python
valores = list(map(float, input("Digite os números: ").split()))
print("Média:", sum(valores) / len(valores))
```

---

## **2. Tuplas (`tuple`)**

Uma **tupla** é como uma lista, mas **imutável**: depois de criada, não pode ser alterada. É ideal para dados que não devem mudar, como coordenadas, datas e constantes.

```python
ponto = (3, 4)
cores = ("vermelho", "verde", "azul")
vazia = ()
unico = (5,)
sem_parenteses = 1, 2, 3
```

### **2.1 Acesso e fatiamento**

Funcionam exatamente como nas listas:

```python
t = (10, 20, 30, 40)
print(t[0])
print(t[-1])
print(t[1:3])
print(len(t))
```

### **2.2 Imutabilidade**

```python
t = (1, 2, 3)
t[0] = 99
# TypeError: 'tuple' object does not support item assignment
```

Métodos disponíveis: apenas `count()` e `index()`.

### **2.3 Desempacotamento (*unpacking*)**

```python
ponto = (3, 4)
x, y = ponto
print(x, y)

# Troca de valores sem variável auxiliar
a, b = 10, 20
a, b = b, a
print(a, b)

# Captura do restante com *
primeiro, *resto = (1, 2, 3, 4)
print(primeiro, resto)
```

### **2.4 Funções que retornam várias coisas**

```python
quociente, resto = divmod(17, 5)
print(quociente, resto)
```

### **2.5 Conversões**

```python
lista = [1, 2, 3]
tupla = tuple(lista)
de_volta = list(tupla)
```

### **2.6 Por que usar tuplas?**

- Protegem dados contra alteração acidental.
- São ligeiramente mais rápidas e ocupam menos memória.
- Podem ser usadas como **chaves de dicionários** (listas não podem).

---

## **3. Dicionários (`dict`)**

Um **dicionário** armazena pares **chave → valor**. Em vez de acessar por posição, acessamos por uma **chave** significativa. É a estrutura ideal para representar "registros".

```python
aluno = {
    "nome": "Ana",
    "idade": 20,
    "curso": "Engenharia Eletrônica",
    "notas": [8.5, 9.0, 7.5]
}

vazio = {}
outro_vazio = dict()
```

As chaves devem ser **únicas** e de tipo **imutável** (texto, número, tupla).

### **3.1 Acessando valores**

```python
print(aluno["nome"])
print(aluno["notas"][0])
print(aluno["telefone"])

print(aluno.get("telefone"))
print(aluno.get("telefone", "N/D"))
```

### **3.2 Adicionando, alterando e removendo**

```python
aluno["idade"] = 21
aluno["email"] = "ana@ufpe.br"

aluno.update({"idade": 22, "periodo": 5})

del aluno["periodo"]
email = aluno.pop("email")
```

### **3.3 Principais métodos**

| Operação              | Descrição                                       |
| --------------------- | ----------------------------------------------- |
| `d.keys()`            | Todas as chaves                                 |
| `d.values()`          | Todos os valores                                |
| `d.items()`           | Pares `(chave, valor)`                          |
| `d.get(k, padrão)`    | Valor de `k` ou `padrão` se não existir         |
| `d.pop(k)`            | Remove `k` e retorna o valor                    |
| `d.update(outro)`     | Atualiza com os pares de `outro`                |
| `d.setdefault(k, v)`  | Retorna `d[k]`; se não existir, cria com `v`    |
| `d.clear()`           | Esvazia o dicionário                            |
| `k in d`              | Verifica se a **chave** existe                  |
| `len(d)`              | Quantidade de pares                             |

```python
estoque = {"maçã": 10, "banana": 25, "uva": 8}

print(list(estoque.keys()))
print(list(estoque.values()))
print(list(estoque.items()))

print("uva" in estoque)
print(8 in estoque)
print(8 in estoque.values())
```

### **3.4 Percorrendo um dicionário**

```python
for fruta, qtd in estoque.items():
    print(f"{fruta}: {qtd} unidades")
```

### **3.5 Dicionários aninhados**

```python
turma = {
    "Ana":   {"idade": 20, "media": 8.7},
    "Bruno": {"idade": 22, "media": 7.1},
}

print(turma["Ana"]["media"])
turma["Carla"] = {"idade": 21, "media": 9.2}
```

---

## **4. Outros tipos:**

### **4.1. `set`**

Coleção **não ordenada** de elementos **únicos**, útil para remover duplicados e fazer operações de conjuntos.

```python
numeros = {1, 2, 2, 3, 3, 3}
print(numeros)

a = {1, 2, 3, 4}
b = {3, 4, 5}
print(a | b)    # união
print(a & b)    # interseção
print(a - b)    # diferença

print(list(set([5, 1, 5, 2, 1])))
```

> Para criar um conjunto vazio, use `set()` — `{}` cria um **dicionário** vazio.

## **5. Qual Estrutura Usar?**

| Situação                                                   | Estrutura     |
| ---------------------------------------------------------- | ------------- |
| Sequência de itens que pode crescer ou mudar               | **Lista**     |
| Dados fixos, como coordenadas `(x, y)` ou datas            | **Tupla**     |
| Dados identificados por nome (registro de aluno, config.)  | **Dicionário**|
| Itens únicos e testes rápidos de pertinência               | **Conjunto**  |

---

## **Resumo**

- **Listas** `[ ]`: ordenadas e mutáveis; métodos como `append`, `insert`, `remove`, `pop`, `sort`.
- **Tuplas** `( )`: ordenadas e imutáveis; ótimas para dados fixos e desempacotamento.
- **Dicionários** `{chave: valor}`: acesso por chave; use `get()` para evitar `KeyError`.
- **Conjuntos** `{ }`: elementos únicos e operações de conjuntos.
- O índice começa em 0 e o fatiamento `[ini:fim]` exclui o `fim`.
- Atribuir uma lista a outra variável **não copia**: use `.copy()`.