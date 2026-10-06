# **Operadores Comparativos e Lógicos**

Referência: [python.org: comparisons](https://docs.python.org/3/reference/expressions.html#comparisons)

Até agora, nossos programas executaram sempre as mesmas instruções, na mesma ordem. Para que um programa **tome decisões**, precisamos de expressões que resultem em **verdadeiro** (`True`) ou **falso** (`False`). Essas expressões são construídas com os operadores comparativos e lógicos, base das estruturas condicionais e de repetição que veremos a seguir.

**Objetivos:**

- Entender o tipo booleano (`bool`).
- Utilizar os operadores comparativos (`==`, `!=`, `<`, `>`, `<=`, `>=`).
- Combinar condições com `and`, `or` e `not`.
- Compreender a precedência e a avaliação em curto-circuito.
- Diferenciar `==` de `is`, e usar `in` / `not in`.
- Saber o que o Python considera "verdadeiro" ou "falso".

---

## **1. O Tipo Booleano**

O tipo `bool` possui apenas dois valores: `True` e `False` (com a primeira letra **maiúscula**).

```python
aprovado = True
chovendo = False

print(type(aprovado))
print(aprovado, chovendo)
```

Internamente, `True` vale `1` e `False` vale `0`:

```python
print(True + True)
print(False + 5)
```

---

## **2. Operadores Comparativos (Relacionais)**

Comparam dois valores e retornam um `bool`.

| Operador | Significado        | Exemplo    | Resultado |
| -------- | ------------------ | ---------- | --------- |
| `==`     | Igual a            | `5 == 5`   | `True`    |
| `!=`     | Diferente de       | `5 != 3`   | `True`    |
| `>`      | Maior que          | `3 > 5`    | `False`   |
| `<`      | Menor que          | `3 < 5`    | `True`    |
| `>=`     | Maior ou igual a   | `5 >= 5`   | `True`    |
| `<=`     | Menor ou igual a   | `6 <= 5`   | `False`   |

```python
idade = 20

print(idade >= 18)
print(idade == 21)
print(idade != 30)
```

> **Atenção:** `=` é **atribuição** e `==` é **comparação**. Confundi-los é um dos erros mais comuns de iniciantes (em Python, `if x = 5:` gera `SyntaxError`).

### **2.1 Comparação encadeada**

O Python permite escrever comparações como na matemática:

```python
x = 7

print(0 < x < 10)
print(1 <= x <= 5)
```

Isso não é possível em C, onde seria necessário `0 < x && x < 10`.

### **2.2 Comparando textos (strings)**

Strings são comparadas **caractere a caractere**, de acordo com o código Unicode. Maiúsculas vêm antes de minúsculas.

```python
print("abc" == "abc")
print("abc" == "ABC")
print("casa" < "mesa")
print("Z" < "a")
```

### **2.3 Comparando números reais**

Lembre-se da imprecisão do `float`. Para comparar com tolerância, use `math.isclose`:

```python
import math

print(0.1 + 0.2 == 0.3)
print(math.isclose(0.1 + 0.2, 0.3))
print(math.isclose(1.0, 1.001, abs_tol=0.01))
```

### **2.4 Comparando tipos diferentes**

```python
print(5 == 5.0)
print(5 == "5")
print(5 < "7")
```

---

## **3. Operadores Lógicos**

Combinam expressões booleanas.

| Operador | Significado | Descrição                                           |
| -------- | ----------- | --------------------------------------------------- |
| `and`    | E           | Verdadeiro se **ambas** forem verdadeiras           |
| `or`     | OU          | Verdadeiro se **pelo menos uma** for verdadeira     |
| `not`    | NÃO         | Inverte o valor lógico                              |

**Tabela-verdade:**

| `A`     | `B`     | `A and B` | `A or B` | `not A` |
| ------- | ------- | --------- | -------- | ------- |
| `True`  | `True`  | `True`    | `True`   | `False` |
| `True`  | `False` | `False`   | `True`   | `False` |
| `False` | `True`  | `False`   | `True`   | `True`  |
| `False` | `False` | `False`   | `False`  | `True`  |

> **Atenção:** Em Python, usam-se as palavras `and`, `or`, `not` — e **não** `&&`, `||`, `!` como em C.

```python
idade = 25
tem_carteira = True

pode_dirigir = idade >= 18 and tem_carteira
print(pode_dirigir)

dia = "sábado"
fim_de_semana = dia == "sábado" or dia == "domingo"
print(fim_de_semana)

print(not fim_de_semana)
```

**Exemplo — Faixa de valores:**

```python
nota = 8.5

valida = nota >= 0 and nota <= 10
invalida = nota < 0 or nota > 10
print(valida, invalida)
```

---

## **4. Precedência dos Operadores**

Da **maior** para a **menor** prioridade:

1. `()` — parênteses
2. `**`
3. `*`, `/`, `//`, `%`
4. `+`, `-`
5. `==`, `!=`, `<`, `>`, `<=`, `>=`, `in`, `not in`, `is`, `is not`
6. `not`
7. `and`
8. `or`

```python
print(True or False and False)
print((True or False) and False)
print(not True == False)
```

No último caso, a comparação (`==`) é avaliada antes do `not`: `not (True == False)` = `not False` = `True`.

> **Dica:** Use parênteses para deixar a intenção explícita, mesmo quando não forem obrigatórios:
> `(a > 0 and b > 0) or c == 0`

---

## **5. Avaliação em Curto-Circuito**

O Python interrompe a avaliação assim que o resultado já é conhecido:

- `A and B`: se `A` é falso, `B` **nem é avaliado**.
- `A or B`: se `A` é verdadeiro, `B` **nem é avaliado**.

Isso é útil para evitar erros:

```python
divisor = 0

# Seguro: a divisão só ocorre se divisor != 0
if divisor != 0 and 10 / divisor > 2:
    print("Maior que 2")
else:
    print("Divisão evitada")
```

### **Os operadores `and` e `or` retornam valores, não apenas `True`/`False`**

```python
print(0 or "padrão")
print("Ana" or "padrão")
print("Ana" and 42)
print(0 and 42)
```

Essa característica é usada, por exemplo, para definir valores padrão:

```python
nome = input("Nome: ") or "Visitante"
```

---

## **6. Valores "Verdadeiros" e "Falsos" (*Truthy* e *Falsy*)**

Em contextos lógicos, o Python converte qualquer valor para `bool`. São considerados **falsos**:

- `False` e `None`
- Zero: `0`, `0.0`
- Coleções e textos **vazios**: `""`, `[]`, `()`, `{}`

Todo o resto é considerado **verdadeiro**.

```python
print(bool(0))
print(bool(-3))
print(bool(""))
print(bool("False"))
print(bool([]))
print(bool([0]))
print(bool(None))
```

Por isso, é comum escrever:

```python
lista = []
if not lista:
    print("A lista está vazia")
```

---

## **7. Operadores de Pertinência: `in` e `not in`**

Verificam se um elemento está presente em uma sequência (texto, lista, tupla, dicionário...).

```python
print("a" in "banana")
print("xyz" in "banana")
print(3 in [1, 2, 3])
print(10 not in [1, 2, 3])

vogais = "aeiou"
letra = "e"
print(letra in vogais)

aluno = {"nome": "Ana", "idade": 20}
print("nome" in aluno)
```

---

## **8. Operadores de Identidade: `is` e `is not`**

- `==` compara **valores** (conteúdo).
- `is` compara **identidade** (se são o **mesmo objeto** na memória).

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)
print(a is b)
print(a is c)
```

O uso mais comum de `is` é para comparar com `None`:

```python
resultado = None
print(resultado is None)
print(resultado is not None)
```

---

## **9. Exemplos Práticos**

**Ano bissexto:** divisível por 4 e não por 100, ou divisível por 400.

```python
ano = 2024
bissexto = (ano % 4 == 0 and ano % 100 != 0) or ano % 400 == 0
print(bissexto)
```

**Verificar se um número está no intervalo e é par:**

```python
n = 18
print(10 <= n <= 20 and n % 2 == 0)
```

**Triângulo válido** (cada lado menor que a soma dos outros dois):

```python
a, b, c = 3, 4, 5
valido = a < b + c and b < a + c and c < a + b
print(valido)
```

---

## **Resumo**

- Comparações retornam `True` ou `False`.
- `==` compara valores; `=` atribui; `is` compara identidade.
- Python permite comparações encadeadas: `0 < x < 10`.
- Operadores lógicos são `and`, `or` e `not` (e não `&&`, `||`, `!`).
- A avaliação é em curto-circuito.
- Valores vazios ou zero são considerados falsos; os demais, verdadeiros.
- `in` e `not in` testam pertinência a sequências.