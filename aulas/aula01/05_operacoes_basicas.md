# **Operações Básicas**

Nesta seção, veremos como o Python realiza cálculos e manipula valores por meio de **operadores** e **funções embutidas** (*built-in*). Tudo o que está aqui pode ser testado diretamente no terminal Python ou em um Notebook.

**Objetivos:**

- Utilizar os operadores aritméticos do Python.
- Entender a ordem de precedência das operações.
- Usar operadores de atribuição compostos.
- Converter valores entre tipos (`int`, `float`, `str`).
- Aplicar funções matemáticas embutidas
- Reconhecer erros comuns em operações numéricas.

## **1. Operadores Aritméticos**

As operações aritméticas são:

| Operador | Operação          | Exemplo   | Resultado |
| -------- | ----------------- | --------- | --------- |
| `+`      | Adição            | `7 + 2`   | `9`       |
| `-`      | Subtração         | `7 - 2`   | `5`       |
| `*`      | Multiplicação     | `7 * 2`   | `14`      |
| `/`      | Divisão real      | `7 / 2`   | `3.5`     |
| `//`     | Divisão inteira   | `7 // 2`  | `3`       |
| `%`      | Resto (módulo)    | `7 % 2`   | `1`       |
| `**`     | Potenciação       | `7 ** 2`  | `49`      |

```python
a = 17
b = 5

print("Soma:", a + b)
print("Subtração:", a - b)
print("Multiplicação:", a * b)
print("Divisão:", a / b)
print("Divisão inteira:", a // b)
print("Resto:", a % b)
print("Potência:", a ** b)
```

```output
Soma: 22
Subtração: 12
Multiplicação: 85
Divisão: 3.4
Divisão inteira: 3
Resto: 2
Potência: 1419857
```

**Observações importantes:**

- O operador `/` **sempre** retorna um `float`, mesmo quando a divisão é exata: `10 / 2` resulta em `5.0`.
- O operador `//` retorna a parte inteira da divisão (arredondando para baixo). Com números negativos: `-7 // 2` resulta em `-4`.
- O operador `%` é muito útil para verificar se um número é par (`n % 2 == 0`) ou para reiniciar contadores cíclicos.
- Para raiz quadrada, pode-se usar `x ** 0.5` ou `math.sqrt(x)` (veja o tópico de *Bibliotecas Básicas*).
- Ao contrário do C, o Python **não possui limite** para o tamanho de números inteiros: `2 ** 200` funciona normalmente.

## **2. Precedência de Operadores**

Assim como na matemática, o Python respeita uma ordem ao avaliar expressões (da maior para a menor prioridade):

1. `()` — Parênteses
2. `**` — Potenciação
3. `+x`, `-x` — Sinais unários
4. `*`, `/`, `//`, `%` — Multiplicação e divisões
5. `+`, `-` — Adição e subtração

Operadores de mesma prioridade são avaliados da **esquerda para a direita**, exceto `**`, que é avaliado da direita para a esquerda.

```python
print(2 + 3 * 4)
print((2 + 3) * 4)
print(2 ** 3 ** 2)
print(-3 ** 2)
print(10 - 4 - 3)
```

```output
14
20
512
-9
3
```

> **Dica:** Na dúvida, use parênteses. Eles deixam a expressão explícita e mais fácil de ler.

**Exemplo — Média ponderada:**

```python
n1, n2, n3 = 7.5, 8.0, 6.5
media = (n1 * 2 + n2 * 3 + n3 * 5) / (2 + 3 + 5)
print(media)
```

```output
7.15
```

## **3. Operadores de Atribuição Compostos**

Combinam uma operação e uma atribuição em uma única instrução.

| Forma composta | Equivale a     |
| -------------- | -------------- |
| `x += 3`       | `x = x + 3`    |
| `x -= 3`       | `x = x - 3`    |
| `x *= 3`       | `x = x * 3`    |
| `x /= 3`       | `x = x / 3`    |
| `x //= 3`      | `x = x // 3`   |
| `x %= 3`       | `x = x % 3`    |
| `x **= 3`      | `x = x ** 3`   |

```python
contador = 10
contador += 5
contador *= 2
contador -= 10
contador //= 3
contador **= 2
print(contador)
```

```output
36
```

> **Atenção:** O Python **não possui** os operadores `++` e `--` do C. Use `x += 1` e `x -= 1`.

## **4. Conversão de Tipos**

Muitas vezes é necessário converter um valor de um tipo para outro. As funções de conversão são `int()`, `float()`, `str()` e `bool()`.

```python
print(int(3.9))
print(int("42"))
print(float(5))
print(float("3.14"))
print(str(2026))
print(bool(-7))
print(bool(0))
print(bool(7))
```

```output
3
42
5.0
3.14
2026
True
False
True
```

**Regras de conversão automática (promoção):**

Em operações entre `int` e `float`, o resultado é sempre `float`.

```python
print(3 + 2.0)
print(type(3 + 2.0))
```

```output
5.0
<class 'float'>
```

## **5. Operações com Strings**

Alguns operadores também funcionam com textos:

```python
nome = "Ada"
sobrenome = "Lovelace"

print(nome + " " + sobrenome)
print("-" * 20)
print("Python! " * 3)
```

```output
Ada Lovelace
--------------------
Python! Python! Python! 
```

> **Atenção:** Não é possível somar `str` com `int` diretamente. Converta com `str()` ou use *f-strings*: `f"Idade: {idade}"`.

```python
print("Luís " + str(1.84))
```

```output
Luís 1.84
```

## **6. Funções Matemáticas Embutidas**

O Python já possui algumas funções prontas, sem necessidade de importar nada:

| Função            | Descrição                                   | Exemplo                | Resultado    |
| ----------------- | ------------------------------------------- | ---------------------- | ------------ |
| `abs(x)`          | Valor absoluto                              | `abs(-8.5)`            | `8.5`        |
| `round(x, n)`     | Arredonda para `n` casas decimais           | `round(3.14159, 2)`    | `3.14`       |
| `pow(x, y)`       | Potência (equivale a `x ** y`)              | `pow(2, 10)`           | `1024`       |
| `divmod(a, b)`    | Retorna `(a // b, a % b)`                   | `divmod(17, 5)`        | `(3, 2)`     |
| `min(...)`        | Menor valor                                 | `min(4, 9, 1)`         | `1`          |
| `max(...)`        | Maior valor                                 | `max(4, 9, 1)`         | `9`          |
| `sum(iterável)`   | Soma dos elementos                          | `sum([1, 2, 3])`       | `6`          |

```python
quociente, resto = divmod(47, 6)
print(quociente, resto)

print(round(2.5))
print(round(3.5))
```

```python
7 5
2
4
```

> **Atenção:** O `round()` do Python 3 usa o *arredondamento do banqueiro*: valores exatamente na metade são arredondados para o **par** mais próximo.

## **7. Precisão de Números Reais**

Números `float` são armazenados em binário (padrão IEEE 754), portanto alguns valores decimais **não são representados exatamente**:

```python
print(0.1 + 0.2)
print(0.1 + 0.2 == 0.3)
```

```output
0.30000000000000004
False
```

Na biblioteca `math`, veremos a função `isclose()`, como exemplo para contornar esses erros numéricos.