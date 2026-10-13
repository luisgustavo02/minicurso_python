# **Strings: Particionamento e Métodos Embutidos**

**Strings** (`str`) são sequências de caracteres usadas para representar textos: nomes, mensagens, linhas de arquivos, comandos, dados lidos do teclado. Elas aparecem em praticamente todo programa, e o Python oferece um conjunto muito rico de ferramentas para manipulá-las.

Diferentemente do C, onde um texto é um *array* de `char` terminado em `\0`, em Python a string é um **objeto** com dezenas de métodos prontos.

**Objetivos:**

- Criar strings de diferentes formas e usar caracteres especiais.
- Acessar caracteres e **particionar** strings com índices e fatias (*slicing*).
- Entender a **imutabilidade** das strings.
- Utilizar os principais **métodos embutidos** (busca, transformação, divisão, verificação).
- Formatar saídas com f-strings, `format()` e especificadores de formato.
- Resolver problemas clássicos com texto.

---

## **1. Criando Strings**

```python
a = 'Python'                 # aspas simples
b = "Python"                 # aspas duplas (equivalente)
c = "It's a string"          # aspas simples dentro de duplas
d = 'Ele disse "oi"'         # aspas duplas dentro de simples

e = """Texto em
várias linhas
com aspas triplas."""

vazia = ""
print(type(a))               # <class 'str'>
print(len(a))                # 6
```

Conversões:

```python
print(str(2026))       # "2026"
print(str(3.14))       # "3.14"
print(int("42"))       # 42
print(float("2.5"))    # 2.5
print(list("abc"))     # ['a', 'b', 'c']
```

### **1.1 Caracteres de escape**

| Sequência | Significado                  |
| --------- | ---------------------------- |
| `\n`      | Quebra de linha              |
| `\t`      | Tabulação                    |
| `\\`      | Barra invertida              |
| `\'`      | Aspa simples                 |
| `\"`      | Aspa dupla                   |
| `\u00e9`  | Caractere Unicode (`é`)      |

```python
print("Linha 1\nLinha 2")
print("Nome:\tAna")
print("Caminho: C:\\Users\\ana")
print("Ele disse: \"olá\"")
```

### **1.2 Strings *raw* (`r"..."`)**

Ignoram os escapes. Úteis para caminhos do Windows e expressões regulares:

```python
print("C:\novo\teste")     # \n e \t são interpretados!
print(r"C:\novo\teste")    # C:\novo\teste
```

---

## **2. Operações Básicas**

```python
nome = "Ada"
sobrenome = "Lovelace"

print(nome + " " + sobrenome)     # Ada Lovelace   (concatenação)
print("-" * 20)                   # --------------------  (repetição)
print(len("Python"))              # 6
print("py" in "python")           # True
print("PY" in "python")           # False (diferencia maiúsculas)
print("x" not in "python")        # True
```

### **2.1 Comparação**

As strings são comparadas caractere a caractere, pelo código Unicode:

```python
print("abc" == "abc")        # True
print("abc" == "ABC")        # False
print("banana" < "cereja")   # True  (ordem alfabética)
print("Zebra" < "abelha")    # True  (maiúsculas vêm antes das minúsculas)
```

### **2.2 Percorrendo uma string**

```python
for letra in "Python":
    print(letra, end=" ")        # P y t h o n

for i, letra in enumerate("abc"):
    print(i, letra)              # 0 a / 1 b / 2 c
```

### **2.3 Códigos de caracteres: `ord()` e `chr()`**

```python
print(ord("A"))     # 65
print(ord("a"))     # 97
print(chr(66))      # B
print(chr(ord("a") + 1))   # b
```

---

## **3. Indexação**

Cada caractere ocupa uma posição, começando em **0**. Índices **negativos** contam a partir do final.

```
  Texto:     P   y   t   h   o   n
  Índice:    0   1   2   3   4   5
  Negativo: -6  -5  -4  -3  -2  -1
```

```python
s = "Python"

print(s[0])     # P
print(s[3])     # h
print(s[-1])    # n  (último)
print(s[-2])    # o
print(s[10])    # IndexError: string index out of range
```

### **3.1 Strings são imutáveis**

Não é possível alterar um caractere diretamente:

```python
s = "Python"
s[0] = "J"      # TypeError: 'str' object does not support item assignment
```

Para "modificar", criamos uma **nova** string:

```python
s = "J" + s[1:]
print(s)        # Jython
```

---

## **4. Particionamento (*Slicing*)**

Sintaxe: `texto[início:fim:passo]`

- `início`: posição onde começa (**incluída**). Padrão: 0.
- `fim`: posição onde termina (**excluída**). Padrão: fim da string.
- `passo`: de quanto em quanto avançar. Padrão: 1.

```python
s = "Python"
#    P y t h o n
#    0 1 2 3 4 5

print(s[0:2])     # Py       (posições 0 e 1)
print(s[2:5])     # tho      (posições 2, 3 e 4)
print(s[2:])      # thon     (do 2 até o final)
print(s[:3])      # Pyt      (do início até a posição 2)
print(s[:])       # Python   (cópia completa)
```

### **4.1 Fatias com índices negativos**

```python
print(s[-3:])     # hon      (últimos 3 caracteres)
print(s[:-1])     # Pytho    (todos menos o último)
print(s[-4:-1])   # tho
```

### **4.2 Fatias com passo**

```python
print(s[::2])     # Pto      (de 2 em 2: posições 0, 2, 4)
print(s[1::2])    # yhn      (posições 1, 3, 5)
print(s[1:5:2])   # yh       (posições 1 e 3)
```

### **4.3 Passo negativo: invertendo a string**

```python
print(s[::-1])      # nohtyP
print(s[4:1:-1])    # oht   (do 4 até o 2, andando para trás)
```

### **4.4 Fatias nunca geram `IndexError`**

Diferentemente da indexação simples, índices fora do intervalo em fatias são ajustados:

```python
print(s[2:100])    # thon
print(s[10:20])    # ""  (string vazia)
```

### **4.5 Resumo visual das fatias**

| Expressão  | Resultado | Descrição                              |
| ---------- | --------- | -------------------------------------- |
| `s[2]`     | `t`       | Um caractere                           |
| `s[:2]`    | `Py`      | Os 2 primeiros                         |
| `s[-2:]`   | `on`      | Os 2 últimos                           |
| `s[1:-1]`  | `ytho`    | Remove o primeiro e o último           |
| `s[::2]`   | `Pto`     | Caracteres em posições pares           |
| `s[::-1]`  | `nohtyP`  | String invertida                       |

### **4.6 Exemplos práticos de fatiamento**

```python
# Extraindo partes de uma data
data = "30/09/2026"
dia = data[:2]        # "30"
mes = data[3:5]       # "09"
ano = data[6:]        # "2026"

# Extensão de um arquivo
arquivo = "relatorio.pdf"
print(arquivo[-3:])   # pdf

# Primeira letra maiúscula (manual)
nome = "ana"
print(nome[0].upper() + nome[1:])   # Ana

# Verificando um palíndromo
palavra = "arara"
print(palavra == palavra[::-1])     # True
```

---

## **5. Métodos Embutidos**

**Métodos** são funções que pertencem ao objeto e são chamadas com a notação `texto.metodo()`. Como as strings são **imutáveis**, os métodos **não alteram** a string original: eles **retornam uma nova**.

```python
nome = "ana"
nome.upper()
print(nome)       # ana     <- continua igual!

nome = nome.upper()
print(nome)       # ANA     <- precisa reatribuir
```

> **Dica:** Para ver todos os métodos disponíveis, use `dir(str)`; para a documentação de um deles, `help(str.split)`.

---

### **5.1 Maiúsculas e minúsculas**

| Método          | Descrição                                       | Exemplo                                 |
| --------------- | ----------------------------------------------- | --------------------------------------- |
| `upper()`       | Tudo em maiúsculas                              | `"python".upper()` → `"PYTHON"`         |
| `lower()`       | Tudo em minúsculas                              | `"PYTHON".lower()` → `"python"`         |
| `capitalize()`  | Primeira letra maiúscula, resto minúsculo       | `"pYTHON".capitalize()` → `"Python"`    |
| `title()`       | Primeira letra de cada palavra maiúscula        | `"ola mundo".title()` → `"Ola Mundo"`   |
| `swapcase()`    | Inverte maiúsculas e minúsculas                 | `"Python".swapcase()` → `"pYTHON"`      |
| `casefold()`    | Minúsculas agressivo, para comparações          | `"ß".casefold()` → `"ss"`               |

```python
resposta = input("Deseja continuar? (s/n): ")
if resposta.lower() == "s":
    print("Continuando...")
```

### **5.2 Removendo espaços e prefixos/sufixos**

| Método                  | Descrição                                            |
| ----------------------- | ---------------------------------------------------- |
| `strip()`               | Remove espaços (e `\n`, `\t`) das duas extremidades  |
| `lstrip()`              | Remove à esquerda                                    |
| `rstrip()`              | Remove à direita                                     |
| `strip(chars)`          | Remove os caracteres indicados das extremidades      |
| `removeprefix(p)`       | Remove o prefixo `p` (Python 3.9+)                   |
| `removesuffix(s)`       | Remove o sufixo `s` (Python 3.9+)                    |

```python
print("  oi  ".strip())            # "oi"
print("  oi  ".lstrip())           # "oi  "
print("  oi  ".rstrip())           # "  oi"
print("xxoixx".strip("x"))         # "oi"
print("...fim...".strip("."))      # "fim"

print("arquivo.txt".removesuffix(".txt"))   # "arquivo"
print("https://site.com".removeprefix("https://"))   # "site.com"
```

> **Importante:** O `strip()` não remove espaços do **meio** da string.

### **5.3 Busca e contagem**

| Método                 | Descrição                                                       |
| ---------------------- | --------------------------------------------------------------- |
| `find(sub)`            | Posição da 1ª ocorrência, ou `-1` se não existir                |
| `rfind(sub)`           | Posição da **última** ocorrência, ou `-1`                       |
| `index(sub)`           | Como `find`, mas gera `ValueError` se não existir               |
| `count(sub)`           | Quantidade de ocorrências (sem sobreposição)                    |
| `startswith(prefixo)`  | `True` se começa com o prefixo                                  |
| `endswith(sufixo)`     | `True` se termina com o sufixo                                  |

```python
s = "banana"

print(s.find("an"))        # 1
print(s.rfind("an"))       # 3
print(s.find("x"))         # -1
print(s.index("x"))        # ValueError: substring not found
print(s.count("a"))        # 3
print(s.count("an"))       # 2

print(s.find("an", 2))     # 3  (procura a partir da posição 2)

print("foto.png".endswith(".png"))              # True
print("foto.png".endswith((".png", ".jpg")))    # True (aceita tupla)
print("Python".startswith("Py"))                # True
```

### **5.4 Substituição**

```python
s = "banana"

print(s.replace("a", "o"))        # bonono
print(s.replace("a", "o", 1))     # bonana  (só a 1ª ocorrência)
print(s.replace("an", ""))        # ba      (remove "an")
print("a  b   c".replace(" ", ""))   # abc
```

### **5.4.1 Várias substituições: `translate` e `maketrans`**

```python
tabela = str.maketrans("aeiou", "12345")
print("python e divertido".translate(tabela))   # pyth4n 2 d3v2rt3d4

sem_pontuacao = str.maketrans("", "", ".,;!?")
print("Oi, tudo bem?".translate(sem_pontuacao))   # Oi tudo bem
```

### **5.5 Dividindo strings: `split`, `rsplit`, `splitlines`, `partition`**

**`split(sep)`** divide a string em uma **lista**:

```python
print("a,b,c".split(","))             # ['a', 'b', 'c']
print("2026-10-16".split("-"))        # ['2026', '10', '16']

# Sem argumento: divide por qualquer quantidade de espaços
print("um  dois   três".split())      # ['um', 'dois', 'três']

# Limitando o número de divisões
print("a,b,c,d".split(",", 1))        # ['a', 'b,c,d']
print("a,b,c,d".rsplit(",", 1))       # ['a,b,c', 'd']
```

**`splitlines()`** divide por quebras de linha:

```python
texto = "linha 1\nlinha 2\nlinha 3"
print(texto.splitlines())     # ['linha 1', 'linha 2', 'linha 3']
```

**`partition(sep)`** divide em exatamente **3 partes** (antes, separador, depois), usando a **primeira** ocorrência:

```python
print("nome=Ana=Silva".partition("="))     # ('nome', '=', 'Ana=Silva')
print("nome=Ana=Silva".rpartition("="))    # ('nome=Ana', '=', 'Silva')
print("abc".partition("="))                # ('abc', '', '')  (separador ausente)

chave, _, valor = "idade=20".partition("=")
print(chave, valor)                        # idade 20
```

**Lendo vários números em uma linha:**

```python
entrada = "3 4 5"
numeros = list(map(int, entrada.split()))
print(numeros)       # [3, 4, 5]

dia, mes, ano = map(int, "30/09/2026".split("/"))
print(ano)           # 2026
```

### **5.6 Juntando strings: `join`**

É o inverso do `split`: une os elementos de um iterável usando a string como separador.

```python
print("-".join(["2026", "10", "16"]))       # 2026-10-16
print(" ".join(["Python", "é", "ótimo"]))   # Python é ótimo
print("".join(["a", "b", "c"]))             # abc
print(", ".join(["maçã", "uva", "pera"]))   # maçã, uva, pera

# Todos os elementos devem ser strings
numeros = [1, 2, 3]
print(", ".join(map(str, numeros)))         # 1, 2, 3
# ", ".join(numeros)  -> TypeError
```

> **Dica de desempenho:** em vez de acumular com `+=` dentro de um laço (cria uma nova string a cada passo), construa uma lista e use `join` no final.

```python
# Lento para muitos elementos
resultado = ""
for i in range(5):
    resultado += str(i) + ","

# Melhor
partes = [str(i) for i in range(5)]
resultado = ",".join(partes)
```

### **5.7 Verificando o conteúdo**

Todos retornam `True` ou `False`:

| Método         | `True` quando...                                   |
| -------------- | -------------------------------------------------- |
| `isalpha()`    | só há letras                                       |
| `isdigit()`    | só há dígitos (`0-9`)                              |
| `isnumeric()`  | só há caracteres numéricos (inclui `½`, `²`)       |
| `isalnum()`    | só há letras e/ou dígitos                          |
| `isspace()`    | só há espaços/tabs/quebras de linha                |
| `isupper()`    | todas as letras são maiúsculas                     |
| `islower()`    | todas as letras são minúsculas                     |
| `istitle()`    | cada palavra começa com maiúscula                  |
| `isascii()`    | todos os caracteres são ASCII                      |

```python
print("Python".isalpha())     # True
print("Python3".isalpha())    # False
print("123".isdigit())        # True
print("12.5".isdigit())       # False  (o ponto não é dígito)
print("abc123".isalnum())     # True
print("   ".isspace())        # True
print("ABC".isupper())        # True
print("".isalpha())           # False  (string vazia sempre dá False)
```

**Validando entradas:**

```python
idade = input("Idade: ")

if idade.isdigit():
    idade = int(idade)
    print(f"Ano de nascimento aproximado: {2026 - idade}")
else:
    print("Digite apenas números inteiros positivos.")
```

> `isdigit()` não reconhece números negativos nem decimais. Para esses casos, use `try/except` com `float()`.

### **5.8 Alinhamento e preenchimento**

| Método              | Descrição                                 | Exemplo                              |
| ------------------- | ----------------------------------------- | ------------------------------------ |
| `center(n, c)`      | Centraliza em `n` caracteres              | `"oi".center(6, "*")` → `**oi**`     |
| `ljust(n, c)`       | Alinha à esquerda                         | `"oi".ljust(6, ".")` → `oi....`      |
| `rjust(n, c)`       | Alinha à direita                          | `"oi".rjust(6)` → `    oi`           |
| `zfill(n)`          | Completa com zeros à esquerda             | `"42".zfill(5)` → `00042`            |

```python
print("RELATÓRIO".center(21, "="))
# ======RELATÓRIO======

print("-42".zfill(5))      # -0042
```

### **5.9 Codificação: `encode` e `decode`**

```python
texto = "ação"
dados = texto.encode("utf-8")
print(dados)                    # b'a\xc3\xa7\xc3\xa3o'
print(len(texto), len(dados))   # 4 6  (ç e ã usam 2 bytes cada)
print(dados.decode("utf-8"))    # ação
```

---

## **6. Formatação de Strings**

### **6.1 f-strings (recomendado)**

Prefixe a string com `f` e coloque expressões entre chaves `{ }`:

```python
nome = "Ana"
idade = 20
altura = 1.6789

print(f"{nome} tem {idade} anos.")
print(f"Daqui a 5 anos terá {idade + 5} anos.")
print(f"Altura: {altura:.2f} m")
print(f"Nome em maiúsculas: {nome.upper()}")
print(f"{nome=}")                  # nome='Ana'  (útil para depuração)
```

### **6.2 Especificadores de formato**

Sintaxe: `{valor:[alinhamento][largura][.precisão][tipo]}`

| Especificador   | Efeito                                  | Exemplo                        | Resultado        |
| --------------- | --------------------------------------- | ------------------------------ | ---------------- |
| `.2f`           | 2 casas decimais                        | `f"{3.14159:.2f}"`             | `3.14`           |
| `10.2f`         | Largura 10, 2 casas                     | `f"{12345.6789:10.2f}"`        | `  12345.68`     |
| `05d`           | Inteiro com zeros à esquerda            | `f"{42:05d}"`                  | `00042`          |
| `,`             | Separador de milhar                     | `f"{1234567.891:,.2f}"`        | `1,234,567.89`   |
| `.1%`           | Percentual                              | `f"{0.256:.1%}"`               | `25.6%`          |
| `e`             | Notação científica                      | `f"{12345.678:.2e}"`           | `1.23e+04`       |
| `b`, `x`, `o`   | Binário, hexadecimal, octal             | `f"{255:b}"`, `f"{255:x}"`     | `11111111`, `ff` |
| `<`, `>`, `^`   | Alinha à esquerda, direita, centro      | `f"{'oi':^6}"`                 | `  oi  `         |

```python
# Tabela alinhada
produtos = [("Caneta", 2.5, 10), ("Caderno", 15.9, 3), ("Mochila", 120.0, 1)]

print(f"{'Produto':<10}{'Preço':>10}{'Qtd':>5}")
print("-" * 25)
for nome, preco, qtd in produtos:
    print(f"{nome:<10}{preco:>10.2f}{qtd:>5}")
```

```
Produto        Preço  Qtd
-------------------------
Caneta          2.50   10
Caderno        15.90    3
Mochila       120.00    1
```

### **6.3 Método `format()` e operador `%`**

Formas mais antigas, ainda encontradas em muitos códigos:

```python
print("{} tem {} anos".format("Ana", 20))
print("{0} {1} {0}".format("a", "b"))              # a b a
print("{nome} tem {idade}".format(nome="Ana", idade=20))
print("{:.2f}".format(3.14159))                     # 3.14

print("%s tem %d anos" % ("Ana", 20))
print("%.2f" % 3.14159)                             # 3.14
```

---

## **7. Aplicações Clássicas**

### **7.1 Contar vogais**

```python
def contar_vogais(texto):
    return sum(1 for letra in texto.lower() if letra in "aeiouáéíóúâêôãõà")

print(contar_vogais("Programação em Python"))   # 7
```

### **7.2 Verificar palíndromo (ignorando espaços e maiúsculas)**

```python
def eh_palindromo(texto):
    limpo = "".join(c.lower() for c in texto if c.isalnum())
    return limpo == limpo[::-1]

print(eh_palindromo("A sacada da casa"))   # True
print(eh_palindromo("Python"))             # False
```

### **7.3 Contar palavras**

```python
texto = "O rato roeu a roupa do rei de Roma. O rato roeu!"

palavras = texto.lower().translate(str.maketrans("", "", ".,!?")).split()
contagem = {}

for palavra in palavras:
    contagem[palavra] = contagem.get(palavra, 0) + 1

print(contagem["rato"])    # 2
print(len(palavras))       # 12
```

### **7.4 Cifra de César**

```python
def cifra_cesar(texto, chave):
    resultado = ""
    for c in texto:
        if c.isascii() and c.isalpha():
            base = ord("A") if c.isupper() else ord("a")
            resultado += chr((ord(c) - base + chave) % 26 + base)
        else:
            resultado += c
    return resultado

codificado = cifra_cesar("Python", 3)
print(codificado)                      # Sbwkrq
print(cifra_cesar(codificado, -3))     # Python
```

### **7.5 Anagramas**

```python
def eh_anagrama(a, b):
    return sorted(a.lower()) == sorted(b.lower())

print(eh_anagrama("amor", "roma"))     # True
```

### **7.6 Remover acentos**

```python
import unicodedata

def remover_acentos(texto):
    decomposto = unicodedata.normalize("NFD", texto)
    return "".join(c for c in decomposto if unicodedata.category(c) != "Mn")

print(remover_acentos("ação, coração, você"))   # acao, coracao, voce
```

### **7.7 Interpretando uma linha de dados**

```python
linha = "Ana;20;8.5"
nome, idade, nota = linha.split(";")
idade = int(idade)
nota = float(nota)
print(f"{nome} ({idade} anos) tirou {nota}")
```

*(Na próxima seção do curso, veremos como ler **arquivos** inteiros linha a linha, usando justamente essas técnicas.)*

### **7.8 Ordenando textos**

```python
nomes = ["carla", "Ana", "bruno", "Davi"]

print(sorted(nomes))                  # ['Ana', 'Davi', 'bruno', 'carla']
print(sorted(nomes, key=str.lower))   # ['Ana', 'bruno', 'carla', 'Davi']
print(sorted(nomes, key=len))         # ['Ana', 'Davi', 'carla', 'bruno']
```

---

## **8. Tabela-Resumo dos Métodos**

| Categoria       | Métodos                                                                                  |
| --------------- | ---------------------------------------------------------------------------------------- |
| Caixa           | `upper`, `lower`, `capitalize`, `title`, `swapcase`, `casefold`                          |
| Limpeza         | `strip`, `lstrip`, `rstrip`, `removeprefix`, `removesuffix`                              |
| Busca           | `find`, `rfind`, `index`, `rindex`, `count`, `startswith`, `endswith`                    |
| Transformação   | `replace`, `translate`, `maketrans`, `zfill`, `center`, `ljust`, `rjust`                 |
| Divisão/união   | `split`, `rsplit`, `splitlines`, `partition`, `rpartition`, `join`                       |
| Verificação     | `isalpha`, `isdigit`, `isnumeric`, `isalnum`, `isspace`, `isupper`, `islower`, `istitle` |
| Formatação      | `format`, f-strings                                                                      |
| Codificação     | `encode` (e `bytes.decode`)                                                              |

---

## **9. Erros Comuns**

| Erro                                                     | Explicação / Correção                                                          |
| -------------------------------------------------------- | ------------------------------------------------------------------------------ |
| `s[0] = "X"`                                             | `TypeError`: strings são imutáveis; crie uma nova string                       |
| `s.upper()` sem atribuir o resultado                     | A string original **não muda**; faça `s = s.upper()`                           |
| `"Idade: " + 20`                                         | `TypeError`; use `str(20)` ou f-string                                         |
| `s[10]` em string curta                                  | `IndexError`; verifique `len(s)` (fatias não geram esse erro)                  |
| Esperar que `s[1:4]` inclua a posição 4                  | O `fim` da fatia é **excluído**                                                |
| `s.find("x")` usado em `if`                              | `-1` é verdadeiro e `0` é falso! Compare: `if s.find("x") != -1` ou use `in`   |
| `", ".join([1, 2, 3])`                                   | `TypeError`; converta com `map(str, ...)`                                      |
| `"12.5".isdigit()` para validar números                  | Retorna `False`; use `try/except` com `float()`                                |
| Comparar textos sem considerar a caixa                   | Use `.lower()` / `.casefold()` nos dois lados                                  |

---

## **Exercícios**

1. Peça uma palavra ao usuário e exiba: o tamanho, a primeira e a última letra, a palavra invertida e em maiúsculas.
2. Dada a string `"Programação Python"`, use fatias para exibir: os 5 primeiros caracteres, os 6 últimos, os caracteres de posição par e a string sem o primeiro e o último caracteres.
3. Peça o nome completo de uma pessoa e exiba apenas o **primeiro nome**, o **último sobrenome** e as **iniciais** (ex.: "Ana Maria Souza" → `A. M. S.`).
4. Peça uma data no formato `dd/mm/aaaa` e exiba o dia, o mês e o ano separadamente, e a data no formato `aaaa-mm-dd`.
5. Conte quantas vezes uma letra, informada pelo usuário, aparece em uma frase (sem usar `count`, e depois usando `count`).
6. Peça uma frase e exiba quantas **palavras** ela possui e qual é a **maior** palavra.
7. Receba uma frase e exiba-a com as **palavras na ordem inversa** (ex.: "eu gosto de Python" → "Python de gosto eu").
8. Verifique se uma frase é um **palíndromo**, ignorando espaços, acentos e maiúsculas.
9. Dado `email = "  Maria.Silva@Exemplo.COM "`, limpe os espaços, converta para minúsculas e separe o **usuário** e o **domínio** (use `partition`).
10. Valide uma senha: mínimo de 8 caracteres, pelo menos uma letra maiúscula, uma minúscula e um dígito (use `isupper`, `islower`, `isdigit`).
11. Substitua todas as vogais de uma frase por `*` e conte quantas foram substituídas.
12. Implemente a **cifra de César** com chave informada pelo usuário, preservando maiúsculas, minúsculas e símbolos, e uma função para decifrar.
13. Dada a lista `[("Caneta", 2.5), ("Caderno", 15.9), ("Mochila", 120)]`, imprima uma tabela alinhada com cabeçalho e o **total** ao final, usando f-strings.
14. Converta um número inteiro positivo para binário **sem usar** `bin()` nem `format()`, usando divisões sucessivas e construindo a string. Compare com `f"{n:b}"`.
15. Dada a frase `"O rato roeu a roupa do rei de Roma"`, gere um dicionário com a **frequência de cada letra** (ignorando espaços e maiúsculas) e exiba as 3 mais frequentes.
16. **Desafio:** Escreva uma função `comprimir(texto)` que comprima sequências repetidas (ex.: `"aaabccdddd"` → `"a3b1c2d4"`) e outra `descomprimir` que faça o inverso.
17. **Desafio:** Escreva uma função que verifique se os parênteses, colchetes e chaves de uma expressão estão **balanceados** (ex.: `"{[()]}"` → `True`, `"([)]"` → `False`).

---

## **Resumo**

- Strings são **sequências imutáveis** de caracteres; criam-se com aspas simples, duplas ou triplas.
- A **indexação** começa em 0 e aceita índices negativos: `s[0]`, `s[-1]`.
- O **fatiamento** `s[início:fim:passo]` exclui o `fim`, aceita passos negativos (`s[::-1]` inverte) e nunca gera `IndexError`.
- Os **métodos** retornam novas strings, sem alterar a original: lembre-se de reatribuir (`s = s.strip()`).
- Os mais usados: `upper/lower`, `strip`, `find/count`, `replace`, `split/join`, `startswith/endswith` e os verificadores `isdigit/isalpha`.
- `split` + `join` são complementares; `partition` divide em três partes.
- Prefira **f-strings** para formatar saídas, com especificadores como `:.2f`, `:>10`, `:05d` e `:,`.
- Use `join` em vez de `+=` para montar textos grandes.
- Strings se combinam bem com listas, dicionários e laços para resolver problemas de processamento de texto.

---

## **Referências**

- [Tutorial oficial: Strings](https://docs.python.org/pt-br/3/tutorial/introduction.html#text)
- [Métodos de string (documentação)](https://docs.python.org/pt-br/3/library/stdtypes.html#string-methods)
- [Mini-linguagem de especificação de formato](https://docs.python.org/pt-br/3/library/string.html#format-specification-mini-language)
- [Módulo `unicodedata`](https://docs.python.org/pt-br/3/library/unicodedata.html)