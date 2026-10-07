# **Vetores e Matrizes**

Em matemática e engenharia, trabalhamos o tempo todo com **vetores** (sinais, coordenadas, vetores de estado) e **matrizes** (sistemas lineares, transformações, imagens). Em C, usamos arrays; em Python, vamos representá-los com **listas**.

> **Importante:** Nesta aula usaremos apenas recursos nativos do Python (listas e laços), para entender **como** as operações funcionam por dentro. Na **Aula 03**, veremos a biblioteca **NumPy**, que torna tudo isso mais simples e muito mais rápido.

**Objetivos:**

- Representar vetores com listas e realizar operações vetoriais.
- Representar matrizes como listas de listas.
- Criar, acessar e percorrer matrizes corretamente.
- Implementar operações matriciais: soma, multiplicação por escalar, transposta e produto.
- Reconhecer as limitações das listas e a motivação para o NumPy.

---

## **1. Vetores**

Um **vetor** (ou *array* unidimensional) é uma sequência de valores, normalmente numéricos. Em Python, é representado por uma **lista**.

```python
v = [3, 4, 5]
w = [1, 2, 3]
```

### **1.1 Criando vetores**

**Definindo o vetor previamente**

```python
zeros = [0] * 5
uns = [1.0] * 4
sequencia = list(range(1, 6))
quadrados = [i ** 2 for i in range(5)]
```

**Definindo o vetor pela entrada**

```python
# Lendo do usuário
n = int(input("Tamanho do vetor: "))
vetor = []
for i in range(n):
    vetor.append(float(input(f"Elemento {i}: ")))
```

Gerando uma grade de pontos igualmente espaçados (como o `linspace` do NumPy):

```python
a, b, n = 0, 1, 5
passo = (b - a) / (n - 1)
x = [a + i * passo for i in range(n)]
print(x)
```

### **1.2 Acesso e fatiamento**

```python
v = [10, 20, 30, 40, 50]

print(v[0])
print(v[-1])
print(v[1:4])
print(len(v))
```

### **1.3 Cuidado: operadores aritméticos NÃO são elemento a elemento**

Diferentemente de linguagens como MATLAB, os operadores `+` e `*` aplicados a listas fazem **concatenação** e **repetição**:

```python
a = [1, 2, 3]
b = [4, 5, 6]

print(a + b)
print(a * 2)
print(a * b)
```

Para operações matemáticas, precisamos de laços ou compreensões de listas.

---

## **2. Operações com Vetores**

Considere:

```python
u = [1, 2, 3]
v = [4, 5, 6]
```

### **2.1 Soma e subtração**

```python
soma = [u[i] + v[i] for i in range(len(u))]
print(soma)

# Forma mais elegante, com zip
soma = [a + b for a, b in zip(u, v)]
dif = [a - b for a, b in zip(u, v)]
print(dif)
```

### **2.2 Multiplicação por escalar**

```python
k = 3
escalado = [k * a for a in u]
print(escalado)
```

### **2.3 Produto escalar (produto interno)**

`u · v = u₁v₁ + u₂v₂ + ... + uₙvₙ`

```python
produto = 0
for a, b in zip(u, v):
    produto += a * b
print(produto)

# Em uma linha
produto_em_1linha = sum(a * b for a, b in zip(u, v))
print(produto_em_1linha)
```

### **2.4 Norma (módulo) de um vetor**

`‖u‖ = √(u₁² + u₂² + ... + uₙ²)`

```python
import math

norma = math.sqrt(sum(a ** 2 for a in u))
print(f"{norma:.4f}")
```

### **2.5 Vetor unitário (normalização)**

```python
unitario = [a / norma for a in u]
print(unitario)
```

### **2.6 Produto vetorial (3D)**

`u × v = (u₂v₃ - u₃v₂, u₃v₁ - u₁v₃, u₁v₂ - u₂v₁)`

```python
produto_vetorial = [
    u[1] * v[2] - u[2] * v[1],
    u[2] * v[0] - u[0] * v[2],
    u[0] * v[1] - u[1] * v[0],
]
print(produto_vetorial)
```

### **2.7 Estatísticas de um vetor**

```python
dados = [12.5, 14.0, 11.5, 15.0, 13.0]

n = len(dados)
media = sum(dados) / n
variancia = sum((x - media) ** 2 for x in dados) / (n - 1)
desvio = math.sqrt(variancia)

print(f"Média: {media:.2f} | Desvio padrão: {desvio:.2f}")
```

### **2.8 Operações úteis do dia a dia**

```python
v = [5, 3, 8, 1, 9]

print(max(v), min(v), sum(v))
print(sorted(v))
print(v.index(max(v)))

# Aplicando uma função a todos os elementos
raizes = [math.sqrt(x) for x in v]

# Filtrando
maiores_que_4 = [x for x in v if x > 4]
```

---

## **3. Matrizes**

Uma **matriz** (*array* bidimensional) é uma tabela de valores organizada em **linhas** e **colunas**. Em Python, representamos como uma **lista de listas**: cada lista interna é uma linha.

```python
A = [
    [1, 2, 3],
    [4, 5, 6],
]
```

Esta matriz possui **2 linhas** e **3 colunas** (dimensão 2×3).

```python
linhas = len(A)
colunas = len(A[0])
print(f"Dimensão: {linhas}x{colunas}")
```

### **3.1 Acessando elementos**

Usa-se `matriz[linha][coluna]`, com índices a partir de **0**.

```python
print(A[0][0])
print(A[1][2])
print(A[0])
print(A[-1][-1])

A[0][1] = 20
```

Para obter uma **coluna** inteira:

```python
coluna_1 = [linha[1] for linha in A]
print(coluna_1)
```

### **3.2 Criando matrizes**

**Forma correta** — compreensão de listas:

```python
linhas, colunas = 3, 4
zeros = [[0 for _ in range(colunas)] for _ in range(linhas)]
print(zeros)
```

**Armadilha clássica — NÃO faça isso:**

```python
errada = [[0] * 4] * 3
errada[0][0] = 9
print(errada)
```

Isso ocorre porque `[[0]*4] * 3` repete a **referência** para uma única lista. Sempre use a compreensão de listas para criar matrizes.

**Matriz identidade:**

```python
n = 3
I = [[1 if i == j else 0 for j in range(n)] for i in range(n)]
print(I)
```

**Lendo uma matriz do usuário:**

```python
m = int(input("Linhas: "))
n = int(input("Colunas: "))

M = []
for i in range(m):
    linha = []
    for j in range(n):
        linha.append(float(input(f"M[{i}][{j}]: ")))
    M.append(linha)
```

### **3.3 Percorrendo uma matriz**

São necessários **dois laços aninhados**: um para as linhas e outro para as colunas.

```python
A = [[1, 2, 3],
     [4, 5, 6],
     [7, 8, 9]]

# Por índices
for i in range(len(A)):
    for j in range(len(A[0])):
        print(A[i][j], end=" ")
    print()

# Por elementos
for linha in A:
    for elemento in linha:
        print(elemento, end=" ")
    print()
```

### **3.4 Exibindo uma matriz formatada**

```python
for linha in A:
    print(" ".join(f"{x:6.2f}" for x in linha))
```

```
  1.00   2.00   3.00
  4.00   5.00   6.00
  7.00   8.00   9.00
```

---

## **4. Operações com Matrizes**

Considere:

```python
A = [[1, 2],
     [3, 4]]

B = [[5, 6],
     [7, 8]]
```

### **4.1 Soma de matrizes**

As matrizes devem ter a **mesma dimensão**.

```python
m, n = len(A), len(A[0])

C = [[A[i][j] + B[i][j] for j in range(n)] for i in range(m)]
print(C)
```

### **4.2 Multiplicação por escalar**

```python
k = 2
D = [[k * A[i][j] for j in range(n)] for i in range(m)]
print(D)
```

### **4.3 Transposta**

`Aᵀ[j][i] = A[i][j]` — troca linhas por colunas.

```python
M = [[1, 2, 3],
     [4, 5, 6]]

linhas, colunas = len(M), len(M[0])
T = [[M[i][j] for i in range(linhas)] for j in range(colunas)]
print(T)

# Atalho com zip e desempacotamento
T = [list(coluna) for coluna in zip(*M)]
```

### **4.4 Diagonal principal e traço**

```python
A = [[1, 2, 3],
     [4, 5, 6],
     [7, 8, 9]]

diagonal = [A[i][i] for i in range(len(A))]
traco = sum(diagonal)
print(diagonal, traco)
```

### **4.5 Soma por linhas e por colunas**

```python
soma_linhas = [sum(linha) for linha in A]
soma_colunas = [sum(A[i][j] for i in range(len(A))) for j in range(len(A[0]))]

print(soma_linhas)
print(soma_colunas)
```

### **4.6 Produto de matrizes**

Se `A` é de dimensão `m×p` e `B` é `p×n`, o produto `C = A·B` é `m×n`, com:

`C[i][j] = Σₖ A[i][k] · B[k][j]`

> O número de **colunas de A** deve ser igual ao número de **linhas de B**.

```python
A = [[1, 2],
     [3, 4]]
B = [[5, 6],
     [7, 8]]

m = len(A)
p = len(B)
n = len(B[0])

C = [[0 for _ in range(n)] for _ in range(m)]

for i in range(m):
    for j in range(n):
        for k in range(p):
            C[i][j] += A[i][k] * B[k][j]

print(C)
```

> **Lembrete:** o produto de matrizes **não é comutativo**: em geral, `A·B ≠ B·A`.

### **4.7 Produto matriz × vetor**

```python
A = [[1, 2],
     [3, 4]]
x = [5, 6]

y = [sum(A[i][j] * x[j] for j in range(len(x))) for i in range(len(A))]
print(y)
```

### **4.8 Determinante (2×2 e 3×3)**

```python
# 2x2
A = [[1, 2],
     [3, 4]]
det = A[0][0] * A[1][1] - A[0][1] * A[1][0]
print(det)

# 3x3 (regra de Sarrus)
B = [[2, 0, 1],
     [1, 3, 2],
     [1, 1, 1]]
det3 = (B[0][0]*B[1][1]*B[2][2] + B[0][1]*B[1][2]*B[2][0] + B[0][2]*B[1][0]*B[2][1]
      - B[0][2]*B[1][1]*B[2][0] - B[0][0]*B[1][2]*B[2][1] - B[0][1]*B[1][0]*B[2][2])
print(det3)
```

### **4.9 Cópia de matrizes**

Como as linhas são listas, `.copy()` faz apenas uma cópia **rasa** (as linhas continuam compartilhadas). Para uma cópia independente:

```python
import copy

A = [[1, 2], [3, 4]]

errada = A.copy()
errada[0][0] = 99
print(A)

A = [[1, 2], [3, 4]]
correta = copy.deepcopy(A)
correta[0][0] = 99
print(A)
```

---

## **5. Aplicações**

### **5.1 Resolvendo um sistema linear 2×2 (regra de Cramer)**

Sistema:

```
2x + 3y = 8
 x -  y = -1
```

```python
a11, a12, b1 = 2, 3, 8
a21, a22, b2 = 1, -1, -1

det = a11 * a22 - a12 * a21

if det == 0:
    print("Sistema sem solução única.")
else:
    x = (b1 * a22 - a12 * b2) / det
    y = (a11 * b2 - b1 * a21) / det
    print(f"x = {x}, y = {y}")
```

### **5.2 Matriz de multiplicação (tabuada)**

```python
tabuada = [[i * j for j in range(1, 6)] for i in range(1, 6)]

for linha in tabuada:
    print(" ".join(f"{x:3d}" for x in linha))
```

### **5.3 Verificando se uma matriz é simétrica**

```python
def eh_simetrica(M):
    n = len(M)
    for i in range(n):
        for j in range(n):
            if M[i][j] != M[j][i]:
                return False
    return True

S = [[1, 2, 3],
     [2, 5, 6],
     [3, 6, 9]]
print(eh_simetrica(S))
```

*(As funções serão estudadas em detalhe na Aula 03.)*

### **5.4 Imagem como matriz**

Uma imagem em tons de cinza é uma matriz em que cada elemento representa a intensidade (0 = preto, 255 = branco):

```python
imagem = [
    [  0,  50, 100],
    [150, 200, 255],
    [ 30,  60,  90],
]

# Negativo da imagem
negativo = [[255 - pixel for pixel in linha] for linha in imagem]
print(negativo)
```

---

## **6. Limitações das Listas e Motivação para o NumPy**

As listas são excelentes para aprender, mas apresentam limitações para a computação científica:

| Aspecto                  | Listas                                          | NumPy                                 |
| ------------------------ | ----------------------------------------------- | ------------------------------------- |
| Soma de vetores          | Exige laço ou `zip`                             | `a + b`                               |
| Multiplicação de matrizes| Três laços aninhados                            | `A @ B`                               |
| Velocidade               | Lenta para grandes volumes de dados             | Muito mais rápida (implementada em C) |
| Memória                  | Cada elemento é um objeto Python                | Blocos contíguos de memória           |
| Funções matemáticas      | Uma a uma, via laços                            | Aplicadas ao vetor inteiro de uma vez |

Um aperitivo do que veremos na **Aula 03**:

```python
import numpy as np

A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

print(A + B)
print(A @ B)
print(A.T)
print(np.linalg.det(A))
```

---

## **Resumo**

- Vetores são representados por **listas**; matrizes, por **listas de listas** (`A[linha][coluna]`).
- Os operadores `+` e `*` em listas **concatenam e repetem** — não fazem operações vetoriais.
- Para somar, escalar ou multiplicar, utilizamos laços, `zip` e compreensões de listas.
- Crie matrizes com `[[0 for _ in range(n)] for _ in range(m)]`; **nunca** com `[[0]*n]*m`.
- Para copiar matrizes, use `copy.deepcopy`.
- O **NumPy** (Aula 03) simplifica e acelera todas essas operações.