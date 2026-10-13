# **Funções**

Ao longo do minicurso, vimos que um programa cresce rapidamente: entrada de dados, cálculos, condicionais, laços... Sem organização, o código vira uma longa sequência de instruções difícil de ler, testar e reaproveitar. As **funções** resolvem esse problema: são blocos de código com um **nome**, que realizam uma tarefa específica e podem ser **chamados quantas vezes forem necessárias**.

Você já usou várias funções prontas: `print()`, `input()`, `len()`, `range()`, `math.sqrt()`. Agora vamos aprender a **criar as nossas**.

**Objetivos:**

- Definir e chamar funções com `def`.
- Utilizar parâmetros, argumentos, valores padrão e retorno.
- Usar argumentos posicionais, nomeados, `*args` e `**kwargs`.
- Compreender o escopo de variáveis.
- Documentar funções com *docstrings* e anotações de tipo.
- Conhecer recursão e funções `lambda`.
- Organizar funções em módulos reutilizáveis.

---

## **1. Por que usar funções?**

- **Reutilização:** escreva uma vez, use muitas vezes (princípio DRY).
- **Organização:** divide um problema grande em partes menores.
- **Legibilidade:** um nome como `calcular_media(notas)` diz muito mais que cinco linhas de código.
- **Manutenção:** corrigir um erro em um único lugar corrige todos os usos.
- **Testabilidade:** é fácil testar uma função isoladamente.

**Sem funções:**

```python
n1 = [7.5, 8.0, 6.5]
print(sum(n1) / len(n1))

n2 = [9.0, 5.5, 7.0, 8.5]
print(sum(n2) / len(n2))

n3 = [6.0, 6.5]
print(sum(n3) / len(n3))
```

**Com funções:**

```python
def media(valores):
    return sum(valores) / len(valores)

print(media([7.5, 8.0, 6.5]))
print(media([9.0, 5.5, 7.0, 8.5]))
print(media([6.0, 6.5]))
```

---

## **2. Definindo e Chamando Funções**

**Sintaxe:**

```python
def nome_da_funcao(parametros):
    """Descrição opcional (docstring)."""
    # corpo da função (indentado)
    return resultado
```

**Exemplo mais simples — sem parâmetros e sem retorno:**

```python
def saudacao():
    print("Olá! Bem-vindo ao minicurso de Python.")

saudacao()   # chamada da função
saudacao()   # pode ser chamada várias vezes
```

**Com parâmetro:**

```python
def saudar(nome):
    print(f"Olá, {nome}!")

saudar("Ana")      # Olá, Ana!
saudar("Bruno")    # Olá, Bruno!
```

**Pontos de atenção:**

- A palavra reservada é `def`, seguida do nome, dos parênteses e de **dois-pontos** (`:`).
- O corpo da função deve estar **indentado** (4 espaços).
- A função só é executada quando **chamada**. A definição apenas "ensina" o Python o que fazer.
- A função precisa ser **definida antes** de ser chamada no código.

```python
ola()          # NameError: name 'ola' is not defined

def ola():
    print("Olá")
```

> **Diferença para o C:** Em Python não declaramos o tipo de retorno nem o tipo dos parâmetros, e não existe a função `main()` obrigatória.

---

## **3. Parâmetros e Argumentos**

- **Parâmetros** são as variáveis listadas na **definição** da função.
- **Argumentos** são os valores passados na **chamada**.

```python
def somar(a, b):        # a e b são parâmetros
    print(a + b)

somar(3, 5)             # 3 e 5 são argumentos
```

Chamar a função com a quantidade errada de argumentos gera erro:

```python
somar(3)         # TypeError: somar() missing 1 required positional argument: 'b'
somar(1, 2, 3)   # TypeError: somar() takes 2 positional arguments but 3 were given
```

### **3.1 Argumentos posicionais e nomeados**

```python
def potencia(base, expoente):
    return base ** expoente

print(potencia(2, 3))                    # 8  (posicionais)
print(potencia(expoente=3, base=2))      # 8  (nomeados: ordem livre)
print(potencia(2, expoente=3))           # 8  (misturando)
```

> **Regra:** argumentos posicionais devem vir **antes** dos nomeados.

### **3.2 Valores padrão (*default*)**

Tornam o parâmetro **opcional**:

```python
def potencia(base, expoente=2):
    return base ** expoente

print(potencia(5))      # 25  (usa expoente = 2)
print(potencia(5, 3))   # 125
```

Os parâmetros com valor padrão devem ficar **depois** dos parâmetros obrigatórios:

```python
def errada(a=1, b):    # SyntaxError
    pass
```

**Armadilha clássica — valor padrão mutável:**

```python
def adicionar(item, lista=[]):    # NÃO FAÇA ISSO
    lista.append(item)
    return lista

print(adicionar(1))   # [1]
print(adicionar(2))   # [1, 2]  <- a lista "lembrou" do valor anterior!
```

A lista padrão é criada **uma única vez**, quando a função é definida. A forma correta:

```python
def adicionar(item, lista=None):
    if lista is None:
        lista = []
    lista.append(item)
    return lista

print(adicionar(1))   # [1]
print(adicionar(2))   # [2]
```

### **3.3 Quantidade variável de argumentos: `*args`**

O `*args` reúne os argumentos posicionais extras em uma **tupla**:

```python
def somar_tudo(*numeros):
    total = 0
    for n in numeros:
        total += n
    return total

print(somar_tudo(1, 2))            # 3
print(somar_tudo(1, 2, 3, 4, 5))   # 15
print(somar_tudo())                # 0
```

### **3.4 Argumentos nomeados variáveis: `**kwargs`**

O `**kwargs` reúne os argumentos nomeados extras em um **dicionário**:

```python
def exibir_aluno(**dados):
    for chave, valor in dados.items():
        print(f"{chave}: {valor}")

exibir_aluno(nome="Ana", idade=20, curso="Eng. Eletrônica")
```

### **3.5 Combinando tudo**

A ordem dos parâmetros na definição é:

1. Posicionais obrigatórios
2. Com valor padrão
3. `*args`
4. Somente nomeados (após `*`)
5. `**kwargs`

```python
def exemplo(a, b=2, *args, c=10, **kwargs):
    print(a, b, args, c, kwargs)

exemplo(1)                       # 1 2 () 10 {}
exemplo(1, 5, 7, 8, c=0, x=99)   # 1 5 (7, 8) 0 {'x': 99}
```

### **3.6 Desempacotando coleções na chamada**

```python
def volume(c, l, a):
    return c * l * a

dimensoes = [2, 3, 4]
print(volume(*dimensoes))          # 24

dados = {"c": 2, "l": 3, "a": 4}
print(volume(**dados))             # 24
```

---

## **4. Retorno de Valores: `return`**

A instrução `return` **encerra** a função e **devolve** um valor ao ponto onde ela foi chamada.

```python
def quadrado(x):
    return x ** 2

resultado = quadrado(6)
print(resultado)              # 36
print(quadrado(2) + quadrado(3))   # 13
```

### **4.1 `print` × `return`**

Um erro muito comum entre iniciantes:

```python
def dobro_print(x):
    print(2 * x)       # apenas exibe; o valor é perdido

def dobro_return(x):
    return 2 * x       # devolve o valor para ser usado

a = dobro_print(5)     # imprime 10
print(a)               # None

b = dobro_return(5)
print(b + 1)           # 11
```

> **Regra prática:** funções que **calculam** algo devem usar `return`; deixe o `print` para quem chamou a função.

### **4.2 Funções sem `return` devolvem `None`**

```python
def saudar(nome):
    print(f"Oi, {nome}")

x = saudar("Ana")
print(x)    # None
```

### **4.3 Retornando vários valores**

Na verdade, o Python retorna uma **tupla**, que pode ser desempacotada:

```python
def estatisticas(valores):
    return min(valores), max(valores), sum(valores) / len(valores)

menor, maior, media = estatisticas([4, 8, 15, 16, 23, 42])
print(menor, maior, media)    # 4 42 18.0

resultado = estatisticas([1, 2, 3])
print(resultado)              # (1, 3, 2.0)
```

### **4.4 Múltiplos pontos de saída**

```python
def classificar(nota):
    if nota >= 7:
        return "Aprovado"
    if nota >= 5:
        return "Recuperação"
    return "Reprovado"

print(classificar(8.5))   # Aprovado
print(classificar(6.0))   # Recuperação
```

> Qualquer código escrito **após** um `return` executado é ignorado.

### **4.5 Funções que retornam booleanos**

```python
def eh_par(n):
    return n % 2 == 0

def eh_bissexto(ano):
    return (ano % 4 == 0 and ano % 100 != 0) or ano % 400 == 0

if eh_bissexto(2024):
    print("Bissexto!")
```

---

## **5. Escopo de Variáveis**

O **escopo** define **onde** uma variável pode ser acessada.

### **5.1 Variáveis locais e globais**

```python
x = 10              # variável global

def funcao():
    y = 5           # variável local (só existe dentro da função)
    print(x, y)     # pode LER a global x

funcao()            # 10 5
print(y)            # NameError: y não existe fora da função
```

### **5.2 Variável local "esconde" a global**

```python
x = 10

def funcao():
    x = 99          # cria uma NOVA variável local x
    print(x)

funcao()            # 99
print(x)            # 10 (a global não mudou)
```

### **5.3 A palavra `global`**

Para **modificar** uma variável global dentro da função:

```python
contador = 0

def incrementar():
    global contador
    contador += 1

incrementar()
incrementar()
print(contador)    # 2
```

> **Boa prática:** Evite `global`. Prefira passar valores como parâmetros e devolvê-los com `return`. Funções que dependem de variáveis globais são difíceis de testar e de entender.

```python
def incrementar(valor):
    return valor + 1

contador = 0
contador = incrementar(contador)
```

### **5.4 A regra LEGB**

Ao procurar um nome, o Python segue a ordem:

1. **L**ocal — dentro da função atual
2. **E**nclosing — funções externas (no caso de funções aninhadas)
3. **G**lobal — nível do módulo
4. **B**uilt-in — nomes embutidos (`print`, `len`, `sum`...)

---

## **6. Passagem de Argumentos: Mutáveis × Imutáveis**

O Python passa **referências** aos objetos. O comportamento depende da mutabilidade:

```python
def modificar_numero(n):
    n = n + 1           # cria um novo int; o original não muda

def modificar_lista(lista):
    lista.append(99)    # altera a própria lista original

x = 5
modificar_numero(x)
print(x)                # 5

l = [1, 2, 3]
modificar_lista(l)
print(l)                # [1, 2, 3, 99]
```

- **Imutáveis** (`int`, `float`, `str`, `tuple`): a função não consegue alterar o original.
- **Mutáveis** (`list`, `dict`, `set`): alterações dentro da função **afetam** o original.

Para evitar alterar a lista original, trabalhe com uma cópia:

```python
def ordenada(lista):
    copia = lista.copy()
    copia.sort()
    return copia
```

---

## **7. Documentando Funções**

### **7.1 Docstrings**

Uma *docstring* é um texto entre aspas triplas, logo abaixo do `def`. Ela fica disponível via `help()`.

```python
def area_triangulo(base, altura):
    """Calcula a área de um triângulo.

    Parâmetros:
        base (float): comprimento da base.
        altura (float): altura relativa à base.

    Retorna:
        float: a área do triângulo.
    """
    return base * altura / 2

help(area_triangulo)
print(area_triangulo.__doc__)
```

### **7.2 Anotações de tipo (*type hints*)**

Indicam, para quem lê, os tipos esperados. O Python **não os impõe** em tempo de execução, mas editores e ferramentas como `mypy` os utilizam.

```python
def area_circulo(raio: float) -> float:
    """Retorna a área de um círculo."""
    import math
    return math.pi * raio ** 2

def saudar(nome: str, vezes: int = 1) -> None:
    for _ in range(vezes):
        print(f"Olá, {nome}!")
```

---

## **8. Funções que Chamam Outras Funções**

Dividir um problema em funções pequenas, cada uma com uma responsabilidade, deixa o programa mais claro:

```python
import math

def ler_numero(mensagem):
    return float(input(mensagem))

def calcular_delta(a, b, c):
    return b ** 2 - 4 * a * c

def calcular_raizes(a, b, c):
    delta = calcular_delta(a, b, c)
    if delta < 0:
        return None
    raiz = math.sqrt(delta)
    return (-b + raiz) / (2 * a), (-b - raiz) / (2 * a)

def exibir_resultado(raizes):
    if raizes is None:
        print("Não há raízes reais.")
    else:
        x1, x2 = raizes
        print(f"x1 = {x1:.3f}, x2 = {x2:.3f}")

def main():
    a = ler_numero("a: ")
    b = ler_numero("b: ")
    c = ler_numero("c: ")
    exibir_resultado(calcular_raizes(a, b, c))

main()
```

### **8.1 O idioma `if __name__ == "__main__"`**

Permite que um arquivo seja usado **tanto como programa quanto como módulo** importável. O bloco só executa quando o arquivo é rodado diretamente.

```python
def main():
    print("Executando o programa principal")

if __name__ == "__main__":
    main()
```

---

## **9. Recursão**

Uma função **recursiva** é aquela que **chama a si mesma**. Toda função recursiva precisa de:

1. **Caso base:** condição que encerra a recursão.
2. **Passo recursivo:** chamada a si mesma com um problema "menor".

**Fatorial:** `n! = n · (n − 1)!`, com `0! = 1`.

```python
def fatorial(n):
    if n == 0:            # caso base
        return 1
    return n * fatorial(n - 1)    # passo recursivo

print(fatorial(5))   # 120
```

**Execução passo a passo:**

```
fatorial(4)
= 4 * fatorial(3)
= 4 * (3 * fatorial(2))
= 4 * (3 * (2 * fatorial(1)))
= 4 * (3 * (2 * (1 * fatorial(0))))
= 4 * 3 * 2 * 1 * 1 = 24
```

**Fibonacci:**

```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print([fibonacci(i) for i in range(10)])   # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

**Soma dos elementos de uma lista:**

```python
def soma_lista(lista):
    if not lista:
        return 0
    return lista[0] + soma_lista(lista[1:])
```

> **Atenção:**
> - Sem caso base (ou com um que nunca é atingido), ocorre `RecursionError: maximum recursion depth exceeded` (o limite padrão é cerca de 1000 chamadas).
> - Esta versão do `fibonacci` é **muito lenta** para valores grandes, pois recalcula os mesmos termos várias vezes. A versão iterativa (com laço) é muito mais eficiente.
> - Sempre que possível, compare a solução recursiva com a iterativa.

---

## **10. Funções `lambda`**

Funções **anônimas** de uma única expressão, úteis para trechos curtos.

```python
# Sintaxe: lambda parâmetros: expressão

quadrado = lambda x: x ** 2
print(quadrado(7))             # 49

soma = lambda a, b: a + b
print(soma(2, 3))              # 5
```

O uso mais comum é como argumento de outras funções:

```python
alunos = [("Ana", 8.5), ("Bruno", 6.0), ("Carla", 9.2)]

# Ordenar pela nota
print(sorted(alunos, key=lambda a: a[1]))
# [('Bruno', 6.0), ('Ana', 8.5), ('Carla', 9.2)]

# Ordenar pela nota, decrescente
print(sorted(alunos, key=lambda a: a[1], reverse=True))

# Maior elemento segundo um critério
print(max(alunos, key=lambda a: a[1]))   # ('Carla', 9.2)
```

### **10.1 `map` e `filter`**

```python
numeros = [1, 2, 3, 4, 5, 6]

quadrados = list(map(lambda x: x ** 2, numeros))
print(quadrados)    # [1, 4, 9, 16, 25, 36]

pares = list(filter(lambda x: x % 2 == 0, numeros))
print(pares)        # [2, 4, 6]
```

As compreensões de listas costumam ser mais legíveis:

```python
quadrados = [x ** 2 for x in numeros]
pares = [x for x in numeros if x % 2 == 0]
```

> **Dica:** Se a lógica ficou complicada, não use `lambda`: crie uma função com `def` e dê um nome a ela.

---

## **11. Funções São Objetos**

Em Python, funções podem ser guardadas em variáveis, passadas como argumento e colocadas em estruturas de dados.

```python
def dobro(x):
    return 2 * x

def aplicar(funcao, valor):
    return funcao(valor)

print(aplicar(dobro, 10))                # 20
print(aplicar(lambda x: x + 1, 10))      # 11

operacoes = {
    "+": lambda a, b: a + b,
    "-": lambda a, b: a - b,
    "*": lambda a, b: a * b,
    "/": lambda a, b: a / b,
}

print(operacoes["*"](6, 7))    # 42
```

Isso é essencial na computação científica, por exemplo, para passar uma função `f(x)` a um método numérico.

---

## **12. Aplicações em Cálculo Numérico**

### **12.1 Método da Bisseção**

```python
def bissecao(f, a, b, tol=1e-6, max_iter=100):
    """Encontra uma raiz de f no intervalo [a, b] pelo método da bisseção."""
    if f(a) * f(b) > 0:
        raise ValueError("f(a) e f(b) devem ter sinais opostos.")

    for _ in range(max_iter):
        c = (a + b) / 2
        if abs(f(c)) < tol or (b - a) / 2 < tol:
            return c
        if f(a) * f(c) < 0:
            b = c
        else:
            a = c
    return (a + b) / 2


def f(x):
    return x ** 3 - x - 2

raiz = bissecao(f, 1, 2)
print(f"Raiz ≈ {raiz:.4f}")    # Raiz ≈ 1.5214
```

### **12.2 Método de Newton**

```python
def newton(f, df, x0, tol=1e-10, max_iter=50):
    """Método de Newton-Raphson. df é a derivada de f."""
    x = x0
    for _ in range(max_iter):
        x_novo = x - f(x) / df(x)
        if abs(x_novo - x) < tol:
            return x_novo
        x = x_novo
    return x

raiz = newton(lambda x: x ** 2 - 2, lambda x: 2 * x, x0=1.0)
print(f"{raiz:.8f}")    # 1.41421356  (≈ √2)
```

### **12.3 Integração numérica (regra do trapézio)**

```python
import math

def trapezio(f, a, b, n=1000):
    """Aproxima a integral de f em [a, b] com n trapézios."""
    h = (b - a) / n
    soma = (f(a) + f(b)) / 2
    for i in range(1, n):
        soma += f(a + i * h)
    return h * soma

print(trapezio(lambda x: x ** 2, 0, 1))      # ≈ 0.333333
print(trapezio(math.sin, 0, math.pi))        # ≈ 2.0
```

### **12.4 Derivada numérica**

```python
def derivada(f, x, h=1e-6):
    """Aproxima f'(x) por diferença central."""
    return (f(x + h) - f(x - h)) / (2 * h)

print(derivada(lambda x: x ** 3, 2))    # ≈ 12.0
```

---

## **13. Boas Práticas com Funções**

- Dê nomes **verbais e descritivos**, em `snake_case`: `calcular_media`, `eh_primo`, `ler_nota`.
- Cada função deve fazer **uma única coisa** bem feita.
- Mantenha as funções **curtas**; se está muito longa, divida-a.
- Evite **efeitos colaterais** (alterar variáveis globais, modificar argumentos sem avisar).
- Prefira **retornar** valores a imprimi-los.
- Escreva uma **docstring** curta.
- Evite valores padrão mutáveis (`[]`, `{}`).
- Não repita código: se copiou e colou, provavelmente precisa de uma função.
- Teste suas funções com casos simples:

```python
def eh_primo(n):
    """Retorna True se n for primo."""
    if n < 2:
        return False
    for d in range(2, int(n ** 0.5) + 1):
        if n % d == 0:
            return False
    return True

# Pequenos testes com assert
assert eh_primo(2)
assert eh_primo(13)
assert not eh_primo(1)
assert not eh_primo(15)
print("Todos os testes passaram!")
```

---

## **14. Erros Comuns**

| Erro                                                      | Causa / Correção                                              |
| :-------------------------------------------------------- | :-------------------------------------------------------------|
| `NameError: name 'f' is not defined`                      | Função chamada antes de ser definida                          |
| `TypeError: ... missing 1 required positional argument`   | Faltaram argumentos na chamada                                |
| Esquecer os `()` ao chamar: `funcao`                      | Referencia a função, mas **não a executa**; use `funcao()`    |
| Usar `print` no lugar de `return`                         | A função devolve `None`                                       |
| `UnboundLocalError`                                       | Atribuir a uma variável global sem declarar `global`          |
| `RecursionError`                                          | Recursão sem caso base ou com caso base inalcançável          |
| Valor padrão mutável                                      | Use `None` e crie a lista dentro da função                    |
| Código após o `return`                                    | Nunca será executado                                          |

---

## **Resumo**

- Funções são criadas com `def` e executam apenas quando **chamadas**.
- **Parâmetros** estão na definição; **argumentos**, na chamada.
- É possível usar valores padrão, argumentos nomeados, `*args` e `**kwargs`.
- Use `return` para devolver valores; sem ele, a função devolve `None`.
- Variáveis criadas dentro da função são **locais**; evite `global`.
- Listas e dicionários passados como argumento **podem ser modificados** pela função.
- Documente com *docstrings* e, se desejar, *type hints*.
- **Recursão** exige caso base; `lambda` cria funções anônimas curtas.
- Funções podem ser organizadas em **módulos** e reutilizadas em outros programas.
- Funções são a base para os próximos tópicos: **Classes**, **NumPy**, **Matplotlib** e **SymPy**.