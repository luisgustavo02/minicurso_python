# **Estrutura Condicional**

Referência: [python.org: controlflow](https://docs.python.org/3/tutorial/controlflow.html)

Com os operadores comparativos e lógicos, já sabemos construir condições. Agora vamos usá-las para que o programa **escolha qual caminho seguir**, executando certos blocos de código apenas quando uma condição for verdadeira.

**Objetivos:**

- Utilizar `if`, `elif` e `else`.
- Compreender a importância da indentação.
- Construir condições compostas e condicionais aninhadas.
- Usar o operador ternário.
- Conhecer o `match-case` (para versões Python 3.10+).
- Validar entradas do usuário.

---

## **1. A Estrutura `if`**

O bloco indentado abaixo do `if` só é executado se a condição for verdadeira.

```python
idade = int(input("Digite sua idade: "))

if idade >= 18:
    print("Você é maior de idade.")

print("Fim do programa.")
```

**Sintaxe:**

```python
if condição:
    # bloco executado se a condição for True
```

**Pontos de atenção:**

- Não esqueça os **dois-pontos** (`:`) no final da linha do `if`.
- A **indentação** (4 espaços) define o que pertence ao bloco. Diferentemente do C, **não há chaves `{ }`**.
- Não são necessários parênteses ao redor da condição (embora sejam permitidos).

```python
# Errado: falta a indentação
if idade >= 18:
print("Maior de idade")
```

> **ERROR: IndentationError**

```python
# Errado: falta o ':'
if idade >= 18
    print("Maior de idade")
```

> **ERROR: SyntaxError**

---

## **2. A Estrutura `if ... else`**

O `else` define o que fazer quando a condição for **falsa**.

```python
numero = int(input("Digite um número: "))

if numero % 2 == 0:
    print("O número é par.")
else:
    print("O número é ímpar.")
```

---

## **3. A Estrutura `if ... elif ... else`**

Para testar **várias condições** em sequência, usamos `elif` (abreviação de *else if*). O Python avalia de cima para baixo e executa **apenas o primeiro bloco** cuja condição seja verdadeira.

```python
nota = float(input("Digite a nota: "))

if nota >= 9:
    conceito = "A"
elif nota >= 7:
    conceito = "B"
elif nota >= 5:
    conceito = "C"
else:
    conceito = "D"

print(f"Conceito: {conceito}")
```

> **Atenção:** A **ordem** das condições importa! Se invertêssemos o exemplo acima e testássemos `nota >= 5` primeiro, uma nota 9.5 cairia no conceito "C".

> **Observação:** Em Python **não existe** `switch`/`case` clássico como em C. Use `elif` ou o `match-case` (seção 7).

## **4. Condições Compostas**

Use `and`, `or` e `not` para combinar condições.

```python
idade = int(input("Idade: "))
tem_ingresso = input("Tem ingresso? (s/n): ").lower() == "s"

if idade >= 18 and tem_ingresso:
    print("Entrada liberada.")
elif idade < 18 and tem_ingresso:
    print("Entrada liberada com responsável.")
else:
    print("Entrada negada.")
```

**Exemplo — Ano bissexto:**

```python
ano = int(input("Ano: "))

if (ano % 4 == 0 and ano % 100 != 0) or ano % 400 == 0:
    print(f"{ano} é bissexto.")
else:
    print(f"{ano} não é bissexto.")
```

**Exemplo — Comparação encadeada:**

```python
temperatura = float(input("Temperatura (°C): "))

if temperatura < 0:
    print("Congelante")
elif 0 <= temperatura < 20:
    print("Frio")
elif 20 <= temperatura < 30:
    print("Agradável")
else:
    print("Quente")
```

---

## **5. Condicionais Aninhadas**

É possível colocar um `if` dentro de outro. Use com moderação para não prejudicar a legibilidade.

```python
usuario = input("Usuário: ")
senha = input("Senha: ")

if usuario == "admin":
    if senha == "1234":
        print("Acesso concedido.")
    else:
        print("Senha incorreta.")
else:
    print("Usuário não encontrado.")
```

Muitas vezes, o aninhamento pode ser simplificado com `and`:

```python
if usuario == "admin" and senha == "1234":
    print("Acesso concedido.")
else:
    print("Usuário ou senha incorretos.")
```

---

## **6. Operador Ternário (Expressão Condicional)**

Para escolher entre dois valores em uma única linha:

```python
valor = expressão_se_verdadeiro if condição else expressão_se_falso
```

```python
idade = 20
situacao = "maior" if idade >= 18 else "menor"
print(situacao)

n = -7
modulo = n if n >= 0 else -n
print(modulo)
```

> **Dica:** Use o ternário apenas para casos simples. Se ficar difícil de ler, prefira o `if` tradicional.

---

## **7. `match-case` (Python 3.10+)**

O `match-case` oferece uma forma organizada de comparar um valor com vários padrões, parecido com o `switch` do C.

```python
opcao = input("Escolha (1-Somar, 2-Subtrair, 3-Sair): ")

match opcao:
    case "1":
        print("Você escolheu somar.")
    case "2":
        print("Você escolheu subtrair.")
    case "3":
        print("Saindo...")
    case _:
        print("Opção inválida.")
```

- O `case _` funciona como o "caso padrão" (*default*).
- Não é necessário `break`: apenas o primeiro `case` correspondente é executado.
- Vários valores no mesmo caso: `case "s" | "S" | "sim":`.

```python
dia = int(input("Dia da semana (1-7): "))

match dia:
    case 1:
        print("Domingo")
    case 2 | 3 | 4 | 5 | 6:
        print("Dia útil")
    case 7:
        print("Sábado")
    case _:
        print("Dia inválido")
```

> Para verificar a versão instalada: `python --version`. Em versões anteriores à 3.10, use `if/elif/else`.

---

## **8. Valores Verdadeiros e Falsos em Condições**

Como vimos, o Python avalia qualquer valor em um contexto lógico:

```python
nome = input("Nome: ")

if nome:
    print(f"Olá, {nome}!")
else:
    print("Você não digitou nada.")

lista = []
if not lista:
    print("Lista vazia")
```

---

## **9. Validação de Entradas**

Condicionais são muito usadas para evitar dados inválidos:

```python
import math

x = float(input("Digite um número: "))

if x < 0:
    print("Não existe raiz quadrada real de número negativo.")
else:
    print(f"Raiz quadrada: {math.sqrt(x):.4f}")
```

**Divisão segura:**

```python
a = float(input("Dividendo: "))
b = float(input("Divisor: "))

if b == 0:
    print("Erro: divisão por zero.")
else:
    print(f"Resultado: {a / b:.2f}")
```

---

## **10. Exemplos Completos**

**Equação do 2º grau:**

```python
import math

a = float(input("a: "))
b = float(input("b: "))
c = float(input("c: "))

if a == 0:
    print("Não é uma equação do 2º grau.")
else:
    delta = b ** 2 - 4 * a * c

    if delta > 0:
        x1 = (-b + math.sqrt(delta)) / (2 * a)
        x2 = (-b - math.sqrt(delta)) / (2 * a)
        print(f"Duas raízes reais: {x1:.3f} e {x2:.3f}")
    elif delta == 0:
        x = -b / (2 * a)
        print(f"Uma raiz real: {x:.3f}")
    else:
        print("Não há raízes reais.")
```

---

## **11. Erros Comuns**

| Erro                                          | Explicação                                                                |
| --------------------------------------------- | ------------------------------------------------------------------------- |
| `if x = 5:`                                   | Usou `=` no lugar de `==` (`SyntaxError`)                                 |
| Esquecer o `:`                                | `SyntaxError`                                                             |
| Indentação inconsistente                      | `IndentationError`                                                        |
| `if x == 1 or 2:`                             | Sempre verdadeiro! Correto: `if x == 1 or x == 2:` ou `if x in (1, 2):`   |
| Comparar `input()` com número sem converter   | `"5" == 5` é `False`                                                      |
| Ordem errada de `elif`                        | Condições mais gerais mascaram as mais específicas                        |

## **Resumo**

- `if`, `elif` e `else` controlam o fluxo do programa.
- A **indentação** e os **dois-pontos** são obrigatórios.
- Apenas o **primeiro** bloco verdadeiro de uma cadeia `if/elif/else` é executado.
- O operador ternário (`a if cond else b`) reduz casos simples a uma linha.
- `match-case` (Python 3.10+) substitui o `switch` do C.
- Use condicionais para validar dados antes de operar sobre eles.