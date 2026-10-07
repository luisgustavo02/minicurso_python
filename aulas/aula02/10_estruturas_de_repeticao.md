# **Estruturas de Repetição**

Imagine calcular a tabuada do 7 escrevendo dez `print()` seguidos, ou somar mil números digitados pelo usuário. As **estruturas de repetição** (ou **laços**, do inglês *loops*) permitem executar um bloco de código várias vezes, sem copiar e colar.

O Python possui dois laços principais: `while` e `for`.

**Objetivos:**

- Utilizar o laço `while` e o laço `for`.
- Gerar sequências com `range()`.
- Controlar laços com `break`, `continue` e `else`.
- Construir laços aninhados.
- Aplicar padrões clássicos: contador, acumulador, busca, maior/menor.
- Evitar laços infinitos.

---

## **1. O Laço `while`**

Repete um bloco **enquanto** a condição for verdadeira. É indicado quando **não sabemos de antemão** quantas repetições serão necessárias.

**Sintaxe:**

```python
while condição:
    # bloco repetido enquanto a condição for True
```

```python
contador = 1

while contador <= 5:
    print(f"Contagem: {contador}")
    contador += 1

print("Fim!")
```

**Saída:**

```
Contagem: 1
Contagem: 2
Contagem: 3
Contagem: 4
Contagem: 5
Fim!
```

Todo laço `while` bem construído possui:

1. **Inicialização** da variável de controle (`contador = 1`).
2. **Condição** de continuidade (`contador <= 5`).
3. **Atualização** da variável de controle (`contador += 1`).

### **1.1 Laço infinito**

Se a condição nunca se tornar falsa, o programa não termina:

```python
x = 1
while x > 0:
    x += 1
```

> **Dica:** Para interromper um programa travado no terminal, use `Ctrl + C`. Em Notebooks, use o botão *Interrupt*.

### **1.2 Validação de entrada com `while`**

```python
nota = float(input("Digite uma nota (0 a 10): "))

while nota < 0 or nota > 10:
    print("Nota inválida! Tente novamente.")
    nota = float(input("Digite uma nota (0 a 10): "))

print(f"Nota registrada: {nota}")
```

### **1.3 Laço com sentinela**

O valor **sentinela** indica o fim da leitura:

```python
soma = 0
numero = float(input("Digite um número (0 para encerrar): "))

while numero != 0:
    soma += numero
    numero = float(input("Digite um número (0 para encerrar): "))

print(f"Soma total: {soma}")
```

---

## **2. A Função `range()`**

Gera uma sequência de números inteiros, ideal para usar com o `for`.

| Chamada                | Sequência gerada       |
| ---------------------- | ---------------------- |
| `range(5)`             | 0, 1, 2, 3, 4          |
| `range(2, 6)`          | 2, 3, 4, 5             |
| `range(0, 10, 2)`      | 0, 2, 4, 6, 8          |
| `range(10, 0, -1)`     | 10, 9, 8, ..., 1       |
| `range(5, 0, -2)`      | 5, 3, 1                |

Sintaxe: `range(início, fim, passo)` — o `fim` **não é incluído**. Se omitido, o `início` vale 0 e o `passo` vale 1.

```python
print(list(range(5)))
print(list(range(1, 11)))
print(list(range(10, 0, -3)))
```

---

## **3. O Laço `for`**

Percorre os elementos de uma **sequência** (lista, texto, tupla, `range`, dicionário...). É indicado quando **sabemos o que ou quantas vezes** iterar.

**Sintaxe:**

```python
for variável in sequência:
    # bloco executado para cada elemento
```

**Com `range`:**

```python
for i in range(1, 6):
    print(i)
```

**Percorrendo listas:**

```python
notas = [7.5, 8.0, 6.5, 9.0]

for nota in notas:
    print(f"Nota: {nota}")
```

**Percorrendo textos:**

```python
for letra in "Python":
    print(letra, end=" ")
```

**Percorrendo dicionários:**

```python
estoque = {"maçã": 10, "banana": 25}

for fruta, qtd in estoque.items():
    print(f"{fruta}: {qtd}")

for fruta in estoque.keys():
    print(f"{fruta}")

for qtd in estoque.values():
    print(f"{qtd}")
```

> Diferentemente do `for` do C, o `for` do Python **não possui** inicialização, condição e incremento em sua sintaxe. Ele sempre itera sobre os elementos de uma sequência.

### **3.1 `for` por índice vs por elemento**

```python
nomes = ["Ana", "Bruno", "Carla"]

# Por elemento (preferível)
for nome in nomes:
    print(nome)

# Por índice
for i in range(len(nomes)):
    print(i, nomes[i])

# Ambos com enumerate (melhor opção quando precisa dos dois)
for i, nome in enumerate(nomes):
    print(i, nome)

for i, nome in enumerate(nomes, start=1):
    print(f"{i}. {nome}")
```

### **3.2 Percorrendo duas listas com `zip`**

```python
alunos = ["Ana", "Bruno", "Carla"]
medias = [8.5, 6.0, 9.2]

for aluno, media in zip(alunos, medias):
    print(f"{aluno}: {media}")
```

---

## **4. Quando Usar `for` ou `while`?**

| Situação                                                 | Laço sugerido |
| -------------------------------------------------------- | ------------- |
| Número de repetições conhecido                           | `for`         |
| Percorrer elementos de uma coleção                       | `for`         |
| Repetir até que algo aconteça (entrada válida, erro < ε) | `while`       |
| Menu interativo                                          | `while`       |

---

## **5. Controle de Laços: `break`, `continue` e `else`**

### **5.1 `break` — interrompe o laço**

```python
for n in range(1, 100):
    if n * n > 50:
        print(f"Primeiro n com n² > 50: {n}")   # 8
        break
```

### **5.2 `continue` — pula para a próxima iteração**

```python
for n in range(1, 11):
    if n % 2 == 0:
        continue
    print(n, end=" ")
```

### **5.3 `else` em laços**

O bloco `else` do laço é executado **somente se o laço terminar normalmente**, isto é, sem ser interrompido por um `break`.

```python
numero = 29

for divisor in range(2, numero):
    if numero % divisor == 0:
        print(f"{numero} não é primo (divisível por {divisor})")
        break
else:
    print(f"{numero} é primo")
```

### **5.4 `pass` — não faz nada**

Útil como "espaço reservado" onde a sintaxe exige um bloco:

```python
for i in range(3):
    # TODO: implementar depois
    pass
```

---

## **6. Laços Aninhados**

Um laço dentro de outro. O laço **interno** executa por completo a cada iteração do laço externo.

```python
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i} x {j} = {i * j}")
    print("-----")
```

**Exemplo — Triângulo de asteriscos:**

```python
altura = 5

for linha in range(1, altura + 1):
    print("*" * linha)
```

```
*
**
***
****
*****
```

**Exemplo — Tabuada de 1 a 5:**

```python
for i in range(1, 6):
    for j in range(1, 11):
        print(f"{i * j:4d}", end="")
    print()
```

---

## **7. Padrões Clássicos**

### **7.1 Contador**

```python
numeros = [4, 7, 2, 9, 12, 5]
pares = 0

for n in numeros:
    if n % 2 == 0:
        pares += 1

print(f"Quantidade de pares: {pares}")
```

### **7.2 Acumulador (soma e produto)**

```python
soma = 0
for i in range(1, 101):
    soma += i
print(soma)

fatorial = 1
n = 5
for i in range(2, n + 1):
    fatorial *= i
print(fatorial)
```

### **7.3 Maior e menor valor**

```python
valores = [12, 45, 7, 33, 21]

maior = menor = valores[0]
for v in valores:
    if v > maior:
        maior = v
    if v < menor:
        menor = v

print(maior, menor)
```

### **7.4 Média**

```python
notas = [7.5, 8.0, 6.5, 9.0]
soma = 0
for nota in notas:
    soma += nota
print(f"Média: {soma / len(notas):.2f}")
```

### **7.5 Busca**

```python
alvo = 33
posicao = -1

for i, v in enumerate(valores):
    if v == alvo:
        posicao = i
        break

print(f"Posição: {posicao}")
```

### **7.6 Sequência de Fibonacci**

```python
n = 10
a, b = 0, 1

for _ in range(n):
    print(a, end=" ")
    a, b = b, a + b
```

> O sublinhado `_` é uma convenção para variáveis cujo valor não será usado.

### **7.7 Menu interativo**

```python
opcao = ""

while opcao != "0":
    print("\n1 - Dobrar um número")
    print("2 - Quadrado de um número")
    print("0 - Sair")
    opcao = input("Escolha: ")

    if opcao == "1":
        x = float(input("Número: "))
        print(2 * x)
    elif opcao == "2":
        x = float(input("Número: "))
        print(x ** 2)
    elif opcao != "0":
        print("Opção inválida!")

print("Programa encerrado.")
```

---

## **8. Aplicação em Métodos Numéricos**

Laços `while` são fundamentais em métodos iterativos, em que repetimos um cálculo até atingir uma **tolerância**.

**Método de Newton para √2:**

A iteração é `x_novo = (x + N / x) / 2`.

```python
N = 2
x = 1.0
tolerancia = 1e-10
iteracoes = 0

while True:
    x_novo = (x + N / x) / 2
    iteracoes += 1
    if abs(x_novo - x) < tolerancia:
        break
    x = x_novo

print(f"√{N} ≈ {x_novo:.10f} (após {iteracoes} iterações)")
```

**Série de Taylor para e^x:**

```python
x = 1
termo = 1.0
soma = 1.0

for n in range(1, 15):
    termo *= x / n
    soma += termo

print(f"e^{x} ≈ {soma:.8f}")
```

---

## **9. Compreensão de Listas**

Uma maneira compacta de substituir certos laços `for`:

```python
# Com laço tradicional
quadrados = []
for x in range(1, 6):
    quadrados.append(x ** 2)

# Com compreensão de listas
quadrados = [x ** 2 for x in range(1, 6)]

# Com condição
pares = [x for x in range(20) if x % 2 == 0]
```

---

## **10. Erros Comuns**

| Erro                                                  | Consequência / Correção                                      |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| Esquecer de atualizar a variável do `while`           | Laço infinito                                                |
| Esquecer o `:` ou a indentação                        | `SyntaxError` / `IndentationError`                           |
| `range(1, 10)` esperando incluir o 10                 | O último valor é 9; use `range(1, 11)`                       |
| Modificar uma lista enquanto a percorre               | Resultados inesperados; percorra uma cópia (`lista[:]`)      |
| Esquecer de converter `input()`                       | `TypeError` ao operar com números                            |
| Inicializar o acumulador **dentro** do laço           | O valor é reiniciado a cada iteração                         |

---

## **Resumo**

- `while` repete enquanto a condição for verdadeira; `for` percorre os elementos de uma sequência.
- `range(início, fim, passo)` gera inteiros; o `fim` é excluído.
- `break` interrompe, `continue` pula a iteração, e o `else` do laço só roda se não houve `break`.
- `enumerate` e `zip` tornam a iteração mais elegante.
- Padrões essenciais: **contador**, **acumulador**, **maior/menor**, **busca**.
- Cuidado com laços infinitos: sempre atualize a variável de controle.