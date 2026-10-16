# **NumPy**

O **NumPy** (*Numerical Python*) é a biblioteca fundamental da computação científica em Python. Quase todo o ecossistema de ciência de dados, aprendizado de máquina, processamento de sinais e cálculo numérico se apoia nele: SciPy, Matplotlib, Pandas, scikit-learn, TensorFlow, PyTorch...

Na Aula 02, implementamos vetores e matrizes com **listas** e laços. Funciona, mas é verboso e lento. O NumPy resolve isso com o **`ndarray`**, um *array* multidimensional eficiente, e com centenas de funções que operam sobre arrays inteiros de uma só vez.

**Objetivos:**

- Instalar e importar o NumPy.
- Criar e inspecionar arrays (`ndarray`).
- Indexar, fatiar e filtrar arrays (inclusive com máscaras booleanas).
- Realizar operações **vetorizadas** e entender o *broadcasting*.
- Calcular estatísticas e agregações por eixo.
- Manipular formas (`reshape`, `transpose`, `concatenate`...).
- Resolver problemas de **álgebra linear** com `np.linalg`.
- Gerar números aleatórios e ler/gravar dados numéricos.
- Aplicar o NumPy em problemas de **Cálculo Numérico**.

---

## **1. Por que usar o NumPy?**

| Aspecto               | Listas do Python                  | Arrays do NumPy                               |
| --------------------- | --------------------------------- | --------------------------------------------- |
| Soma de vetores       | Laço ou `zip`                     | `a + b`                                       |
| Produto de matrizes   | Três laços aninhados              | `A @ B`                                       |
| Elementos             | Qualquer tipo, misturados         | **Um único tipo** (`dtype`)                   |
| Memória               | Cada elemento é um objeto Python  | Bloco contíguo de memória                     |
| Velocidade            | Lenta em grandes volumes          | Dezenas a centenas de vezes mais rápido       |
| Funções matemáticas   | Uma a uma, via laço               | Aplicadas ao array inteiro (*vetorização*)    |

O ganho vem da **vetorização**: os laços acontecem em código compilado (C), e não no interpretador Python.

---

## **2. Instalação e Importação**

No terminal:

```bash
pip install numpy
```

No Jupyter/Colab: `%pip install numpy` (no Colab, o NumPy já vem instalado).

Por convenção universal, importamos com o apelido `np`:

```python
import numpy as np

print(np.__version__)
```

---

## **3. Criando Arrays**

### **3.1 A partir de listas: `np.array`**

```python
import numpy as np

v = np.array([1, 2, 3, 4])                 # vetor (1D)
M = np.array([[1, 2, 3],
              [4, 5, 6]])                  # matriz (2D)

print(v)           # [1 2 3 4]
print(M)
# [[1 2 3]
#  [4 5 6]]
print(type(v))     # <class 'numpy.ndarray'>
```

> **Diferença visual:** ao imprimir, o array **não** tem vírgulas entre os elementos, ao contrário de uma lista.

### **3.2 Arrays prontos**

| Função                 | Resultado                                        |
| ---------------------- | ------------------------------------------------ |
| `np.zeros((2, 3))`     | Matriz 2×3 de zeros                              |
| `np.ones((2, 3))`      | Matriz 2×3 de uns                                |
| `np.full((2, 2), 7)`   | Matriz 2×2 preenchida com 7                      |
| `np.eye(3)`            | Matriz identidade 3×3                            |
| `np.diag([1, 2, 3])`   | Matriz diagonal com os valores dados             |
| `np.empty((2, 2))`     | Matriz sem inicialização (valores arbitrários)   |
| `np.zeros_like(a)`     | Zeros com o mesmo formato e tipo de `a`          |

```python
print(np.zeros((2, 3)))
# [[0. 0. 0.]
#  [0. 0. 0.]]

print(np.eye(3))
# [[1. 0. 0.]
#  [0. 1. 0.]
#  [0. 0. 1.]]
```

### **3.3 Sequências: `arange` e `linspace`**

```python
print(np.arange(5))            # [0 1 2 3 4]
print(np.arange(1, 10, 2))     # [1 3 5 7 9]
print(np.arange(0, 1, 0.25))   # [0.   0.25 0.5  0.75]

print(np.linspace(0, 1, 5))    # [0.   0.25 0.5  0.75 1.  ]
print(np.linspace(0, 2 * np.pi, 100))   # 100 pontos de 0 a 2π
```

| Função                        | Característica                                                |
| ----------------------------- | ------------------------------------------------------------- |
| `np.arange(ini, fim, passo)`  | Você define o **passo**; o `fim` é **excluído**               |
| `np.linspace(ini, fim, n)`    | Você define o **número de pontos**; o `fim` é **incluído**    |
| `np.logspace(a, b, n)`        | `n` pontos espaçados logaritmicamente entre `10^a` e `10^b`   |

> **Dica:** com passos decimais, prefira `linspace`. O `arange` pode gerar um ponto a mais ou a menos por causa de erros de arredondamento.

### **3.4 Pontos para funções de duas variáveis: `meshgrid`**

```python
x = np.linspace(-1, 1, 3)
y = np.linspace(0, 2, 3)

X, Y = np.meshgrid(x, y)
print(X)
# [[-1.  0.  1.]
#  [-1.  0.  1.]
#  [-1.  0.  1.]]
print(Y)
# [[0. 0. 0.]
#  [1. 1. 1.]
#  [2. 2. 2.]]

Z = X ** 2 + Y ** 2       # f(x, y) avaliada em toda a grade
```

Será essencial para gráficos de superfícies e curvas de nível no Matplotlib.

---

## **4. Atributos de um Array**

```python
A = np.array([[1, 2, 3],
              [4, 5, 6]])

print(A.shape)       # (2, 3)  -> 2 linhas, 3 colunas
print(A.ndim)        # 2       -> número de dimensões (eixos)
print(A.size)        # 6       -> total de elementos
print(A.dtype)       # int64   -> tipo dos elementos
print(A.itemsize)    # 8       -> bytes por elemento
print(A.nbytes)      # 48      -> bytes no total
print(len(A))        # 2       -> tamanho do 1º eixo
```

> `shape` é uma **tupla**. Para um vetor de 5 elementos, é `(5,)`.

### **4.1 Tipos de dados (`dtype`)**

Todos os elementos de um array têm o **mesmo tipo**.

| `dtype`                           | Descrição                 |
| --------------------------------- | ------------------------- |
| `int8`, `int16`, `int32`, `int64` | Inteiros com sinal        |
| `uint8`, ...                      | Inteiros sem sinal        |
| `float32`, `float64`              | Reais (padrão: `float64`) |
| `complex128`                      | Números complexos         |
| `bool`                            | Booleanos                 |

```python
a = np.array([1, 2, 3])
b = np.array([1.0, 2, 3])
c = np.array([1, 2.5, 3])
d = np.array([1, 2, 3], dtype=float)
e = np.array([1 + 2j, 3 - 1j])

print(a.dtype, b.dtype, c.dtype, d.dtype, e.dtype)
# int64 float64 float64 float64 complex128

f = a.astype(np.float32)     # converte o tipo (gera uma cópia)
print(f.dtype)               # float32
```

> **Cuidado — estouro de inteiros:** tipos inteiros têm limite e "dão a volta" silenciosamente.

```python
x = np.array([200], dtype=np.uint8)
print(x + 100)    # [44]   (300 não cabe em uint8: 300 - 256 = 44)
```

---

## **5. Indexação e Fatiamento**

### **5.1 Vetores (1D)**

Igual às listas:

```python
v = np.arange(10, 60, 10)    # [10 20 30 40 50]

print(v[0])        # 10
print(v[-1])       # 50
print(v[1:4])      # [20 30 40]
print(v[::-1])     # [50 40 30 20 10]

v[0] = 99          # altera o elemento
```

### **5.2 Matrizes (2D): `A[linha, coluna]`**

Em arrays NumPy, usamos **uma única vírgula** dentro dos colchetes (e não `A[i][j]`, embora funcione):

```python
A = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

print(A[0, 1])       # 2        (linha 0, coluna 1)
print(A[2, -1])      # 9
print(A[1])          # [4 5 6]  (linha 1)
print(A[:, 1])       # [2 5 8]  (coluna 1)
print(A[0:2, 1:])    # [[2 3]
                     #  [5 6]]
print(A[::2, ::2])   # [[1 3]
                     #  [7 9]]
```

Compare com a Aula 02: para extrair uma coluna de uma lista de listas, era necessária uma compreensão de listas. Aqui basta `A[:, 1]`.

### **5.3 Fatias são *views* (não cópias!)**

Ao contrário das listas, **uma fatia de array NumPy compartilha a memória** com o original:

```python
v = np.arange(10)
w = v[2:5]          # view
w[0] = 99
print(v)            # [ 0  1 99  3  4  5  6  7  8  9]   <- v mudou!

c = v[2:5].copy()   # cópia independente
c[0] = -1
print(v)            # v não muda
```

### **5.4 Indexação booleana (máscaras)**

Permite **filtrar** elementos por uma condição:

```python
v = np.array([5, 12, 7, 20, 3])

mascara = v > 6
print(mascara)          # [False  True  True  True False]
print(v[mascara])       # [12  7 20]
print(v[v > 6])         # [12  7 20]  (forma direta)

# Combinando condições: use & (E), | (OU), ~ (NÃO), com parênteses!
print(v[(v > 6) & (v < 15)])    # [12  7]
print(v[(v < 5) | (v > 15)])    # [20  3]
print(v[~(v > 6)])              # [5 3]

# Modificando por condição
v[v > 10] = 10
print(v)                        # [ 5 10  7 10  3]
```

> **Atenção:** `and`, `or` e `not` **não funcionam** com arrays. Use `&`, `|` e `~`, sempre com **parênteses** em cada condição.

### **5.5 `np.where`**

```python
v = np.array([5, 12, 7, 20, 3])

print(np.where(v > 6, 1, 0))        # [0 1 1 1 0]   (escolhe entre dois valores)
print(np.where(v > 6))              # (array([1, 2, 3]),)   (posições onde é verdadeiro)
print(np.where(v % 2 == 0, "par", "ímpar"))
```

### **5.6 Indexação por lista de posições (*fancy indexing*)**

```python
v = np.array([10, 20, 30, 40, 50])
print(v[[0, 2, 4]])       # [10 30 50]
print(v[[4, 4, 0]])       # [50 50 10]
```

---

## **6. Operações com Arrays**

### **6.1 Operações elemento a elemento**

Os operadores aritméticos atuam **posição por posição**, sem laços:

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

print(a + b)      # [5 7 9]
print(a - b)      # [-3 -3 -3]
print(a * b)      # [ 4 10 18]
print(a / b)      # [0.25 0.4  0.5 ]
print(a ** 2)     # [1 4 9]
print(2 * a)      # [2 4 6]      (escalar × vetor)
print(a + 10)     # [11 12 13]
print(a > 2)      # [False False  True]
```

Compare com as **listas**, em que `a + b` concatenava e `2 * a` repetia!

> **Atenção:** `*` em matrizes é o produto **elemento a elemento** (produto de Hadamard), **não** o produto matricial. Para este, use `@` (ou `np.dot`).

```python
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

print(A * B)     # [[ 5 12]
                 #  [21 32]]   elemento a elemento

print(A @ B)     # [[19 22]
                 #  [43 50]]   produto matricial (o mesmo resultado da Aula 02!)
```

### **6.2 Funções universais (*ufuncs*)**

Funções matemáticas aplicadas a **todos** os elementos:

```python
x = np.array([0, np.pi / 2, np.pi])

print(np.sin(x))      # [0.0000000e+00 1.0000000e+00 1.2246468e-16]
print(np.cos(x))      # [ 1.0000000e+00  6.1232340e-17 -1.0000000e+00]
print(np.sqrt([1, 4, 9]))      # [1. 2. 3.]
print(np.exp([0, 1, 2]))       # [1.         2.71828183 7.3890561 ]
print(np.log([1, np.e, 10]))   # [0.         1.         2.30258509]
print(np.abs([-3, 2, -1]))     # [3 2 1]
```

| Categoria         | Funções                                                           |
| ----------------- | ----------------------------------------------------------------- |
| Trigonométricas   | `sin`, `cos`, `tan`                                               |
| Funções arco      | `arcsin`, `arccos`, `arctan`, `arctan2`                           |
| Hiperbólicas      | `sinh`, `cosh`, `tanh`                                            |
| Exponenciais      | `exp`, `log`, `log10`, `log2`, `sqrt`, `power`                    |
| Arredondamento    | `round`, `floor`, `ceil`, `trunc`                                 |
| Outras            | `abs`, `sign`, `maximum`, `minimum`, `clip`, `deg2rad`, `rad2deg` |

```python
print(np.clip([-5, 3, 12], 0, 10))     # [ 0  3 10]  (limita ao intervalo)
print(np.maximum([1, 5, 3], [4, 2, 6]))  # [4 5 6]
print(np.rad2deg(np.pi))               # 180.0
```

### **6.3 Vetorização × laço: a diferença de desempenho**

```python
import time
import numpy as np

n = 1_000_000
lista = list(range(n))
array = np.arange(n)

# Soma dos quadrados com laço
t0 = time.perf_counter()
total = 0
for x in lista:
    total += x * x
t_laco = time.perf_counter() - t0

# Soma dos quadrados com NumPy
t0 = time.perf_counter()
total_np = np.sum(array ** 2)
t_np = time.perf_counter() - t0

print(f"Laço:  {t_laco:.4f} s")
print(f"NumPy: {t_np:.4f} s")
print(f"Speedup: {t_laco / t_np:.0f}x")
```

> Os tempos variam por máquina, mas o NumPy costuma ser **dezenas de vezes mais rápido**. Regra de ouro: **evite laços `for` sobre elementos de arrays; use operações vetorizadas.**

---

## **7. Broadcasting**

O **broadcasting** é o mecanismo que permite operar arrays de **formatos diferentes**, "esticando" automaticamente o menor.

```python
a = np.array([1, 2, 3])
print(a + 10)        # o escalar 10 é "esticado" para [10 10 10]

M = np.array([[1, 2, 3],
              [4, 5, 6]])
v = np.array([10, 20, 30])

print(M + v)         # v é somado a CADA linha
# [[11 22 33]
#  [14 25 36]]
```

**Regra:** compare os formatos (`shape`) **da direita para a esquerda**. Duas dimensões são compatíveis se forem **iguais** ou se uma delas for **1**.

```
M:      (2, 3)
v:      (   3)   → compatível: (2, 3)
```

**Exemplo — tabuada (linha × coluna):**

```python
linha  = np.arange(1, 4)             # shape (3,)
coluna = np.arange(1, 4)[:, None]    # shape (3, 1)  ("[:, None]" vira coluna)

print(coluna * linha)
# [[1 2 3]
#  [2 4 6]
#  [3 6 9]]
```

**Exemplo — normalizar as colunas de uma matriz (subtrair a média de cada coluna):**

```python
dados = np.array([[1., 10.],
                  [2., 20.],
                  [3., 30.]])

centrados = dados - dados.mean(axis=0)
print(centrados)
# [[-1. -10.]
#  [ 0.   0.]
#  [ 1.  10.]]
```

**Quando não é compatível:**

```python
np.ones((3, 2)) + np.ones(3)
# ValueError: operands could not be broadcast together with shapes (3,2) (3,)
```

---

## **8. Agregações e Estatísticas**

```python
v = np.array([4, 8, 15, 16, 23, 42])

print(v.sum())        # 108
print(v.mean())       # 18.0
print(v.min(), v.max())          # 4 42
print(v.argmin(), v.argmax())    # 0 5   (POSIÇÃO do mínimo e do máximo)
print(v.prod())       # produto de todos os elementos
print(np.median(v))   # 15.5
print(np.cumsum(v))   # [  4  12  27  43  66 108]   (soma acumulada)
print(np.diff(v))     # [ 4  7  1  7 19]            (diferenças consecutivas)
```

### **8.1 Desvio padrão e variância**

```python
dados = np.array([2, 4, 4, 4, 5, 5, 7, 9])

print(np.var(dados))             # 4.0
print(np.std(dados))             # 2.0     (populacional)
print(np.std(dados, ddof=1))     # 2.138...(amostral)
print(np.percentile(dados, 75))  # 5.5
```

> **Atenção:** `np.std` usa por padrão `ddof=0` (desvio **populacional**), enquanto `statistics.stdev` calcula o **amostral**. Use `ddof=1` para obter o mesmo resultado.

### **8.2 O parâmetro `axis`**

Em matrizes, `axis` indica **ao longo de qual eixo** a operação é feita:

- `axis=0` → opera **descendo** as linhas (um resultado por **coluna**).
- `axis=1` → opera **atravessando** as colunas (um resultado por **linha**).

```python
M = np.array([[1, 2, 3],
              [4, 5, 6]])

print(M.sum())            # 21
print(M.sum(axis=0))      # [5 7 9]     (soma de cada coluna)
print(M.sum(axis=1))      # [ 6 15]     (soma de cada linha)
print(M.mean(axis=0))     # [2.5 3.5 4.5]
print(M.max(axis=1))      # [3 6]
print(M.argmax())         # 5   (posição no array "achatado")
print(np.unravel_index(M.argmax(), M.shape))   # (1, 2)  (linha 1, coluna 2)
```

### **8.3 Verificações lógicas**

```python
v = np.array([3, 7, 12])

print(np.any(v > 10))     # True   (algum é > 10?)
print(np.all(v > 0))      # True   (todos são > 0?)
print((v > 5).sum())      # 2      (quantos são > 5? True conta como 1)
print((v > 5).mean())     # 0.666..(fração dos que são > 5)
```

### **8.4 Valores ausentes (`NaN`)**

```python
v = np.array([1.0, 2.0, np.nan, 4.0])

print(v.mean())           # nan     (contamina o resultado)
print(np.nanmean(v))      # 2.333...
print(np.isnan(v))        # [False False  True False]
print(v[~np.isnan(v)])    # [1. 2. 4.]
```

---

## **9. Alterando a Forma dos Arrays**

```python
a = np.arange(12)                 # [0 1 2 ... 11]

M = a.reshape(3, 4)               # 3 linhas, 4 colunas
print(M)
# [[ 0  1  2  3]
#  [ 4  5  6  7]
#  [ 8  9 10 11]]

print(a.reshape(2, -1).shape)     # (2, 6)   (o -1 é calculado automaticamente)
print(M.T)                        # transposta (4×3)
print(M.ravel())                  # volta para 1D (view quando possível)
print(M.flatten())                # volta para 1D (sempre cópia)
```

### **9.1 Juntando e separando arrays**

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

print(np.concatenate([a, b]))     # [1 2 3 4 5 6]
print(np.vstack([a, b]))          # empilha verticalmente (linhas)
# [[1 2 3]
#  [4 5 6]]
print(np.hstack([a, b]))          # [1 2 3 4 5 6]
print(np.column_stack([a, b]))    # a e b como colunas
# [[1 4]
#  [2 5]
#  [3 6]]

print(np.split(np.arange(6), 3))  # [array([0, 1]), array([2, 3]), array([4, 5])]
```

### **9.2 Adicionando e removendo eixos**

```python
v = np.array([1, 2, 3])

print(v.shape)                # (3,)
print(v[:, None].shape)       # (3, 1)   vetor coluna
print(v[None, :].shape)       # (1, 3)   vetor linha
print(np.expand_dims(v, 0).shape)   # (1, 3)
print(np.squeeze(v[:, None]).shape) # (3,)
```

### **9.3 Repetição**

```python
print(np.tile([1, 2], 3))       # [1 2 1 2 1 2]
print(np.repeat([1, 2], 3))     # [1 1 1 2 2 2]
```

---

## **10. Ordenação, Busca e Conjuntos**

```python
v = np.array([30, 10, 20, 10])

print(np.sort(v))               # [10 10 20 30]
print(np.sort(v)[::-1])         # [30 20 10 10]   (decrescente)
print(np.argsort(v))            # [1 3 2 0]   (posições que ordenam o array)
print(np.unique(v))             # [10 20 30]
print(np.unique(v, return_counts=True))
# (array([10, 20, 30]), array([2, 1, 1]))

print(np.isin(v, [10, 30]))     # [ True  True False  True]
print(np.searchsorted([10, 20, 30], 25))   # 2   (posição de inserção; vetor ordenado)
```

`argsort` é muito útil para ordenar um array **pela ordem de outro**:

```python
nomes = np.array(["Ana", "Bruno", "Carla"])
notas = np.array([8.5, 6.0, 9.2])

ordem = np.argsort(notas)[::-1]       # da maior para a menor nota
print(nomes[ordem])                   # ['Carla' 'Ana' 'Bruno']
```

---

## **11. Álgebra Linear: `np.linalg`**

```python
A = np.array([[2., 1.],
              [1., 3.]])
b = np.array([3., 5.])
```

| Operação                  | Código                        | Resultado para `A`            |
| ------------------------- | ----------------------------- | ----------------------------- |
| Produto matricial         | `A @ A` ou `np.dot(A, A)`     | `[[5, 5], [5, 10]]`           |
| Transposta                | `A.T`                         | `A` (simétrica)               |
| Determinante              | `np.linalg.det(A)`            | `5.0` (aprox.)                |
| Inversa                   | `np.linalg.inv(A)`            | `[[0.6, -0.2], [-0.2, 0.4]]`  |
| Traço                     | `np.trace(A)`                 | `5.0`                         |
| Posto (*rank*)            | `np.linalg.matrix_rank(A)`    | `2`                           |
| Norma de vetor/matriz     | `np.linalg.norm(v)`           | -                             |
| Autovalores e autovetores | `np.linalg.eig(A)`            | -                             |

### **11.1 Resolvendo sistemas lineares: `solve`**

Resolver `Ax = b`, ou seja:

```
2x +  y = 3
 x + 3y = 5
```

```python
x = np.linalg.solve(A, b)
print(x)                         # [0.8 1.4]
print(np.allclose(A @ x, b))     # True  (verificação)
```

> **Boa prática:** use `np.linalg.solve(A, b)` em vez de `np.linalg.inv(A) @ b`. É mais **rápido** e **numericamente mais estável**.

### **11.2 Autovalores e autovetores**

```python
autovalores, autovetores = np.linalg.eig(A)
print(autovalores)       # [3.61803399 1.38196601]   (a ordem pode variar)

# Para matrizes simétricas, prefira eigh (mais estável; valores em ordem crescente)
w, V = np.linalg.eigh(A)
print(w)                 # [1.38196601 3.61803399]
```

Cada **coluna** de `autovetores` é um autovetor.

### **11.3 Normas**

```python
v = np.array([3, 4])
print(np.linalg.norm(v))          # 5.0
print(np.linalg.norm(v, 1))       # 7.0   (norma-1: soma dos módulos)
print(np.linalg.norm(v, np.inf))  # 4.0   (norma do máximo)
print(np.linalg.norm(A))          # norma de Frobenius da matriz
```

### **11.4 Produto escalar e vetorial**

```python
u = np.array([1, 2, 3])
w = np.array([4, 5, 6])

print(np.dot(u, w))       # 32
print(u @ w)              # 32
print(np.cross(u, w))     # [-3  6 -3]   (produto vetorial, 3D)
```

### **11.5 Matrizes singulares**

```python
S = np.array([[1., 2.],
              [2., 4.]])      # linhas proporcionais

print(np.linalg.matrix_rank(S))     # 1
np.linalg.inv(S)                    # LinAlgError: Singular matrix
```

### **11.6 Mínimos quadrados: `lstsq`**

```python
# Ajuste de reta y = a·x + b para dados com ruído
x = np.array([0, 1, 2, 3, 4])
y = np.array([1.1, 2.9, 5.2, 7.1, 8.8])

A = np.column_stack([x, np.ones_like(x)])
(a, b), *_ = np.linalg.lstsq(A, y, rcond=None)
print(f"y = {a:.2f}x + {b:.2f}")      # y = 1.96x + 1.10
```

---

## **12. Polinômios, Interpolação e Ajuste**

```python
# p(x) = x² - 3x + 2   -> coeficientes do MAIOR para o MENOR grau
p = [1, -3, 2]

print(np.polyval(p, 3))        # 2        (valor de p(3))
print(np.roots(p))             # [2. 1.]  (raízes)
print(np.polyder(p))           # [ 2 -3]  (derivada: 2x - 3)
print(np.polyint(p))           # [ 0.33333333 -1.5  2.  0. ]   (integral)
```

**Ajuste polinomial (`polyfit`):**

```python
x = np.array([0, 1, 2, 3, 4])
y = np.array([1.1, 2.9, 5.2, 7.1, 8.8])

coef = np.polyfit(x, y, 1)             # grau 1 (reta)
print(coef)                            # ≈ [1.96 1.1 ]

print(np.polyval(coef, 5))             # previsão em x = 5  ->  ≈ 10.9
```

**Interpolação linear (`interp`):**

```python
xp = [0, 1, 2, 3]
yp = [0, 1, 4, 9]
print(np.interp(2.5, xp, yp))          # 6.5
```

> Para trabalhar com polinômios de forma mais completa (e com ordem crescente dos coeficientes), existe `numpy.polynomial.Polynomial`.

---

## **13. Números Aleatórios**

A forma recomendada é criar um **gerador** com `np.random.default_rng`, opcionalmente com uma **semente** para reprodutibilidade:

```python
rng = np.random.default_rng(seed=42)

print(rng.random(3))                   # 3 reais em [0, 1)
print(rng.integers(1, 7, size=10))     # 10 dados de 6 faces (1 a 6; o 7 é excluído)
print(rng.normal(0, 1, size=5))        # distribuição normal (média 0, desvio 1)
print(rng.uniform(-1, 1, size=(2, 3))) # uniforme em [-1, 1)

print(rng.choice([10, 20, 30, 40], size=2, replace=False))   # sorteio sem reposição
print(rng.permutation(5))              # permutação aleatória de 0..4

v = np.arange(5)
rng.shuffle(v)                         # embaralha no próprio array
```

### **13.1 Exemplo: Método de Monte Carlo para estimar π**

Sorteamos pontos no quadrado `[0, 1]²` e contamos quantos caem dentro do quarto de círculo de raio 1. A razão entre as áreas é `π/4`.

```python
rng = np.random.default_rng(42)

n = 1_000_000
pontos = rng.random((n, 2))                       # n pontos (x, y)
dentro = (pontos ** 2).sum(axis=1) <= 1           # x² + y² <= 1 ?

pi_estimado = 4 * dentro.mean()
print(pi_estimado)                                # ≈ 3.14
```

### **13.2 Exemplo: verificando a lei dos grandes números**

```python
rng = np.random.default_rng(0)
lancamentos = rng.integers(1, 7, size=100_000)
print(lancamentos.mean())          # ≈ 3.5  (valor esperado de um dado)
```

---

## **14. Lendo e Salvando Dados**

### **14.1 Arquivos de texto/CSV**

```python
# Salvando
tabela = np.column_stack([np.arange(5), np.arange(5) ** 2])
np.savetxt("quadrados.csv", tabela, delimiter=",", fmt="%d",
           header="n,quadrado", comments="")

# Lendo (skiprows=1 pula o cabeçalho)
dados = np.loadtxt("quadrados.csv", delimiter=",", skiprows=1)
print(dados.shape)                 # (5, 2)

# Separando as colunas em variáveis
n, quadrado = np.loadtxt("quadrados.csv", delimiter=",", skiprows=1, unpack=True)
```

Para arquivos com valores ausentes ou tipos mistos, existe `np.genfromtxt`.

### **14.2 Formato binário do NumPy (`.npy` e `.npz`)**

Rápido, compacto e preserva `shape` e `dtype`:

```python
A = np.random.default_rng(1).random((3, 3))

np.save("matriz.npy", A)
B = np.load("matriz.npy")
print(np.array_equal(A, B))        # True

np.savez("conjunto.npz", A=A, v=np.arange(5))   # vários arrays em um arquivo
dados = np.load("conjunto.npz")
print(dados["v"])                  # [0 1 2 3 4]
```

---

## **15. Aplicações em Cálculo Numérico**

### **15.1 Avaliando funções em muitos pontos**

```python
x = np.linspace(0, 2 * np.pi, 1000)
y = np.sin(x) * np.exp(-x / 5)      # sem nenhum laço!

print(y.max(), x[y.argmax()])        # valor máximo e onde ocorre
```

### **15.2 Integração numérica (regra do trapézio)**

```python
x = np.linspace(0, np.pi, 1001)
y = np.sin(x)

integral = np.trapezoid(y, x)        # NumPy 2.0+  (em versões antigas: np.trapz)
print(integral)                       # ≈ 2.0
```

Versão "manual" vetorizada (comparável à função `trapezio` do tópico de Funções):

```python
h = x[1] - x[0]
integral = h * (y.sum() - (y[0] + y[-1]) / 2)
```

### **15.3 Derivada numérica**

```python
x = np.linspace(0, 2 * np.pi, 200)
y = np.sin(x)

dy = np.gradient(y, x)                  # derivada numérica de y em relação a x
erro = np.max(np.abs(dy - np.cos(x)))   # compara com a derivada exata
print(f"Erro máximo: {erro:.2e}")       # da ordem de 1e-4
```

### **15.4 Método de Euler para EDO**

Resolver `dy/dt = -2y`, com `y(0) = 1` (solução exata: `y = e^(-2t)`):

```python
h = 0.01
t = np.arange(0, 2 + h, h)
y = np.zeros_like(t)
y[0] = 1.0

for i in range(len(t) - 1):          # aqui o laço é inevitável: cada passo depende do anterior
    y[i + 1] = y[i] + h * (-2 * y[i])

exata = np.exp(-2 * t)
print(np.max(np.abs(y - exata)))     # erro máximo da ordem de 1e-3
```

### **15.5 Método de Newton vetorizado**

Aplicar Newton a **vários chutes iniciais ao mesmo tempo** para `f(x) = x² − 2`:

```python
x = np.array([0.5, 1.0, 3.0, 10.0])      # vários chutes iniciais

for _ in range(30):
    x = x - (x ** 2 - 2) / (2 * x)       # atualiza todos de uma vez

print(x)                                  # [1.41421356 1.41421356 1.41421356 1.41421356]
```

### **15.6 Métodos iterativos para sistemas lineares (Jacobi)**

```python
A = np.array([[10., 2., 1.],
              [1., 5., 1.],
              [2., 3., 10.]])
b = np.array([7., -8., 6.])

D = np.diag(A)                  # diagonal de A (vetor)
R = A - np.diagflat(D)          # A sem a diagonal
x = np.zeros_like(b)

for _ in range(50):
    x = (b - R @ x) / D         # x^(k+1) = D⁻¹ (b − R x^(k))

print(x)                                    # solução aproximada
print(np.allclose(A @ x, b))                # True
print(np.linalg.solve(A, b))                # solução "exata", para comparar
```

### **15.7 Substituindo as implementações da Aula 02**

| Tarefa (Aula 02)                | Com listas e laços                                        | Com NumPy                      |
| ------------------------------- | --------------------------------------------------------- | ------------------------------ |
| Soma de vetores                 | `[a + b for a, b in zip(u, v)]`                           | `u + v`                        |
| Produto escalar                 | `sum(a * b for a, b in zip(u, v))`                        | `u @ v`                        |
| Norma                           | `math.sqrt(sum(a ** 2 for a in u))`                       | `np.linalg.norm(u)`            |
| Transposta                      | `[list(c) for c in zip(*M)]`                              | `M.T`                          |
| Produto de matrizes             | Três laços aninhados                                      | `A @ B`                        |
| Determinante                    | Fórmula manual (Sarrus)                                   | `np.linalg.det(A)`             |
| Diagonal e traço                | `[A[i][i] for i in range(n)]`                             | `np.diag(A)`, `np.trace(A)`    |
| Matriz identidade               | `[[1 if i == j else 0 ...] ...]`                          | `np.eye(n)`                    |
| Média e desvio padrão           | Fórmulas manuais                                          | `v.mean()`, `v.std(ddof=1)`    |

---

## **16. Boas Práticas**

- **Evite laços sobre elementos:** vetorize com operações de array, `where` e máscaras.
- Use `np.isclose` / `np.allclose` para comparar reais (nunca `==`).
- Prefira `np.linalg.solve` a calcular a inversa.
- Defina **sementes** (`default_rng(seed)`) para resultados reprodutíveis.
- Esteja atento a **views** × **cópias** (`.copy()` quando precisar de independência).
- Verifique sempre o `shape` e o `dtype` ao depurar.
- Para listas pequenas e operações simples, listas comuns bastam; o NumPy brilha em dados numéricos **grandes**.
- Use nomes padrão: `import numpy as np`.

---

## **17. Erros Comuns**

| Erro / Situação                                                | Explicação / Correção                                                            |
| -------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `ValueError: operands could not be broadcast together`         | Formatos incompatíveis; confira os `shape`                                       |
| `ValueError: The truth value of an array ... is ambiguous`     | Usou `if a == b:` com arrays; use `(a == b).all()`, `np.array_equal` ou `np.any` |
| Usar `and`/`or` com máscaras                                   | Use `&`, `\|`, `~` com parênteses                                                |
| `A * B` esperando produto matricial                            | `*` é elemento a elemento; use `A @ B`                                           |
| Modificar uma fatia e alterar o original                       | Fatias são *views*; use `.copy()`                                                |
| `np.array([1, 2, 3]) / 0`                                      | Gera `inf`/`nan` com aviso, **não** gera exceção                                 |
| `np.array([[1, 2], [3]])`                                      | Listas de tamanhos diferentes: erro (ou array de objetos em versões antigas)     |
| Estouro de inteiros (`uint8`, `int8`)                          | Use um `dtype` maior                                                             |
| Comparar reais com `==`                                        | Use `np.isclose`                                                                 |
| Esperar `arange` com `float` exato                             | Use `linspace`                                                                   |
| `np.trapz` não encontrado/avisando                             | Em NumPy ≥ 2.0 use `np.trapezoid`                                                |
| `A[i][j]` em laços de arrays grandes                           | Funciona, mas é lento; use `A[i, j]` ou vetorize                                 |

---

## **Resumo Rápido (*Cheat Sheet*)**

| Tarefa                        | Código                                                |
| ----------------------------- | ----------------------------------------------------- |
| Importar                      | `import numpy as np`                                  |
| Criar de lista                | `np.array([1, 2, 3])`                                 |
| Zeros / uns / identidade      | `np.zeros((m, n))`, `np.ones((m, n))`, `np.eye(n)`    |
| Sequências                    | `np.arange(a, b, passo)`, `np.linspace(a, b, n)`      |
| Formato e tipo                | `A.shape`, `A.ndim`, `A.size`, `A.dtype`              |
| Elemento / linha / coluna     | `A[i, j]`, `A[i]`, `A[:, j]`                          |
| Filtro                        | `v[v > 0]`, `np.where(cond, a, b)`                    |
| Produto matricial             | `A @ B`                                               |
| Soma por coluna / por linha   | `A.sum(axis=0)`, `A.sum(axis=1)`                      |
| Média, desvio                 | `v.mean()`, `v.std(ddof=1)`                           |
| Mudar formato                 | `A.reshape(m, n)`, `A.T`, `A.ravel()`                 |
| Juntar                        | `np.concatenate`, `np.vstack`, `np.hstack`            |
| Sistema linear                | `np.linalg.solve(A, b)`                               |
| Inversa / determinante        | `np.linalg.inv(A)`, `np.linalg.det(A)`                |
| Autovalores                   | `np.linalg.eig(A)`                                    |
| Ajuste polinomial             | `np.polyfit(x, y, grau)`                              |
| Aleatórios                    | `rng = np.random.default_rng(42)`; `rng.random(n)`    |
| Ler / salvar texto            | `np.loadtxt(...)`, `np.savetxt(...)`                  |
| Ler / salvar binário          | `np.load(...)`, `np.save(...)`                        |
| Comparar reais                | `np.isclose(a, b)`, `np.allclose(A, B)`               |

---

## **Resumo**

- O **NumPy** fornece o `ndarray`: arrays multidimensionais de tipo único, rápidos e compactos.
- Crie arrays com `array`, `zeros`, `ones`, `eye`, `arange`, `linspace` e `meshgrid`; inspecione com `shape`, `dtype`, `ndim` e `size`.
- Indexação em matrizes: `A[i, j]`, `A[:, j]`; fatias são **views**; máscaras booleanas filtram dados.
- Operadores atuam **elemento a elemento**; use `@` para o produto matricial e `ufuncs` (`sin`, `exp`, `sqrt`...) para aplicar funções a arrays inteiros.
- O **broadcasting** permite operar arrays de formatos diferentes seguindo regras simples.
- Agregações (`sum`, `mean`, `std`, `max`...) aceitam `axis` para operar por linhas ou colunas.
- `np.linalg` resolve sistemas (`solve`), calcula `det`, `inv`, `eig`, `norm` e mais; `polyfit`, `interp` e `trapezoid` cobrem ajuste, interpolação e integração.
- `default_rng` gera números aleatórios reprodutíveis; `loadtxt`/`savetxt` e `save`/`load` guardam e leem dados.
- A regra de ouro: **vetorize** e evite laços sobre elementos.
- O NumPy é a base para o **Matplotlib** (gráficos) e o **SymPy** (matemática simbólica), os próximos tópicos.

---

## **Referências**

- [Site oficial do NumPy](https://numpy.org/)
- [NumPy: guia para iniciantes](https://numpy.org/doc/stable/user/absolute_beginners.html)
- [Broadcasting (documentação)](https://numpy.org/doc/stable/user/basics.broadcasting.html)
- [Álgebra linear com `numpy.linalg`](https://numpy.org/doc/stable/reference/routines.linalg.html)