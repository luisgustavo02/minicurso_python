# **Tratamento de Erros**

Todo programa, cedo ou tarde, encontra situações inesperadas: o usuário digita letras no lugar de números, um arquivo não existe, uma divisão por zero aparece no meio de um cálculo, uma conexão cai. Um programa **robusto** não é aquele que nunca encontra problemas, e sim aquele que **sabe lidar com eles**: avisa o usuário com clareza, recupera-se quando possível e falha de forma controlada quando não é.

Neste tópico, aprenderemos a **ler** mensagens de erro, a **capturar** exceções com `try/except`, a **levantar** as nossas próprias exceções e a **depurar** programas.

**Objetivos:**

- Diferenciar erros de sintaxe, exceções e erros lógicos.
- Ler e interpretar um *traceback*.
- Usar `try`, `except`, `else` e `finally`.
- Levantar exceções com `raise` e criar exceções personalizadas.
- Validar entradas do usuário de forma robusta.
- Registrar erros com `logging` e depurar com `breakpoint()`.
- Tratar problemas numéricos (divisão por zero, domínio, não convergência).
- Aplicar boas práticas e evitar armadilhas comuns.

---

## **1. Tipos de Erros**

### **1.1. Erros de sintaxe (*SyntaxError*)**

Ocorrem quando o código está **escrito de forma inválida**. São detectados **antes** da execução: o programa sequer começa.

```python
print("Olá"          # falta o parêntese de fechamento
```

```
SyntaxError: '(' was never closed
```

```python
if x > 5
    print("maior")   # falta o ":"
```

```
SyntaxError: expected ':'
```

Erros de indentação são uma subclasse (`IndentationError`).

### **1.2. Exceções (erros em tempo de execução)**

O código é válido, mas **algo dá errado durante a execução**. O Python interrompe o programa e exibe uma mensagem. É disso que trata a maior parte deste tópico.

```python
print(10 / 0)          # ZeroDivisionError: division by zero
int("abc")             # ValueError: invalid literal for int() with base 10: 'abc'
lista = [1, 2, 3]
lista[5]               # IndexError: list index out of range
```

### **1.3. Erros lógicos**

O programa **executa sem nenhuma mensagem**, mas o resultado está **errado**. São os mais difíceis de achar, pois o Python não avisa.

```python
a, b = 8, 6
media = a + b / 2      # esperado: (a + b) / 2 = 7.0
print(media)           # 11.0  <- errado, e nenhum erro foi mostrado
```

Para esses casos, ajudam: testes, `print` de valores intermediários e o depurador (seção 12).

| Tipo           | Quando aparece       | Mensagem do Python | Como encontrar                     |
| -------------- | -------------------- | ------------------ | ---------------------------------- |
| **Sintaxe**    | Antes de executar    | Sim                | Ler a mensagem e a linha indicada  |
| **Exceção**    | Durante a execução   | Sim                | *Traceback* e `try/except`         |
| **Lógico**     | Resultado errado     | **Não**            | Testes, depuração                  |

---

## **2. Lendo um *Traceback***

Quando uma exceção não é tratada, o Python exibe o **traceback**: o "caminho" percorrido até o erro.

```python
def dividir(a, b):
    return a / b

def calcular(x):
    return dividir(x, 0)

calcular(10)
```

```
Traceback (most recent call last):
  File "programa.py", line 7, in <module>
    calcular(10)
  File "programa.py", line 5, in calcular
    return dividir(x, 0)
  File "programa.py", line 2, in dividir
    return a / b
ZeroDivisionError: division by zero
```

**Como ler (de baixo para cima):**

1. **Última linha:** o **tipo** do erro (`ZeroDivisionError`) e a **descrição** (`division by zero`).
2. **Imediatamente acima:** a linha onde o erro **ocorreu** (`return a / b`, linha 2 de `dividir`).
3. **Linhas anteriores:** quem **chamou** quem (`<module>` → `calcular` → `dividir`).

> **Dica:** Leia a última linha primeiro. Se não for suficiente, procure no traceback a **última linha do seu código**: é lá que costuma estar a causa.

---

## **3. Os Principais Tipos de Exceção**

| Exceção               | Quando ocorre                                          | Exemplo                              |
| --------------------- | ------------------------------------------------------ | ------------------------------------ |
| `ZeroDivisionError`   | Divisão (ou módulo) por zero                           | `10 / 0`                             |
| `ValueError`          | Valor com tipo certo, mas conteúdo inadequado          | `int("abc")`, `math.sqrt(-1)`        |
| `TypeError`           | Operação entre tipos incompatíveis                     | `"a" + 1`                            |
| `NameError`           | Variável não definida                                  | `print(y)`                           |
| `UnboundLocalError`   | Variável local usada antes de receber valor            | Atribuir e ler global sem `global`   |
| `IndexError`          | Índice fora do intervalo                               | `[1, 2][5]`                          |
| `KeyError`            | Chave inexistente em dicionário                        | `{"a": 1}["b"]`                      |
| `AttributeError`      | Atributo/método inexistente                            | `"abc".foo()`                        |
| `FileNotFoundError`   | Arquivo ou pasta inexistente                           | `open("nada.txt")`                   |
| `PermissionError`     | Sem permissão de acesso                                | Gravar em pasta protegida            |
| `ModuleNotFoundError` | Módulo não encontrado                                  | `import biblioteca_inexistente`      |
| `OverflowError`       | Resultado numérico grande demais                       | `math.exp(1000)`                     |
| `RecursionError`      | Recursão excedeu o limite                              | Função recursiva sem caso base       |
| `AssertionError`      | Falha em um `assert`                                   | `assert 1 == 2`                      |
| `KeyboardInterrupt`   | Usuário pressionou `Ctrl + C`                          | —                                    |
| `EOFError`            | `input()` sem dados (fim da entrada)                   | `Ctrl + D` / `Ctrl + Z`              |
| `StopIteration`       | Iterador esgotado                                      | `next(iter([]))`                     |
| `NotImplementedError` | Método que deveria ser implementado na subclasse       | Classes abstratas informais          |

### **3.1. Hierarquia de exceções**

As exceções formam uma **árvore de herança**. Capturar uma classe "mãe" captura também todas as "filhas".

```
BaseException
├── SystemExit
├── KeyboardInterrupt
└── Exception
    ├── ArithmeticError
    │   ├── ZeroDivisionError
    │   ├── OverflowError
    │   └── FloatingPointError
    ├── AssertionError
    ├── AttributeError
    ├── EOFError
    ├── ImportError
    │   └── ModuleNotFoundError
    ├── LookupError
    │   ├── IndexError
    │   └── KeyError
    ├── NameError
    │   └── UnboundLocalError
    ├── OSError
    │   ├── FileNotFoundError
    │   ├── FileExistsError
    │   ├── PermissionError
    │   └── IsADirectoryError
    ├── RuntimeError
    │   ├── NotImplementedError
    │   └── RecursionError
    ├── SyntaxError
    │   └── IndentationError
    ├── TypeError
    └── ValueError
        └── UnicodeError
```

Por exemplo, `except LookupError` captura tanto `IndexError` quanto `KeyError`; `except ArithmeticError` captura `ZeroDivisionError` e `OverflowError`.

---

## **4. `try` e `except`**

**Sintaxe:**

```python
try:
    # código que PODE gerar uma exceção
except TipoDeExcecao:
    # o que fazer se ela ocorrer
```

**Exemplo básico:**

```python
try:
    numero = int(input("Digite um número inteiro: "))
    print(f"O dobro é {2 * numero}")
except ValueError:
    print("Isso não é um número inteiro válido!")

print("O programa continua...")
```

**Fluxo de execução:**

1. O Python executa o bloco `try` linha a linha.
2. Se **nenhuma** exceção ocorrer, o bloco `except` é **ignorado**.
3. Se ocorrer uma exceção, o restante do `try` é **abandonado** e o Python procura um `except` correspondente.
4. Se encontrar, executa-o e o programa **segue normalmente** depois do `try/except`.
5. Se **não** encontrar, a exceção continua "subindo" (e pode encerrar o programa).

### **4.1. Vários `except`**

Trate cada tipo de erro de forma adequada:

```python
try:
    a = float(input("Dividendo: "))
    b = float(input("Divisor: "))
    print(f"Resultado: {a / b:.4f}")
except ValueError:
    print("Digite apenas números.")
except ZeroDivisionError:
    print("Não é possível dividir por zero.")
```

### **4.2. Capturando vários tipos no mesmo `except`**

```python
try:
    resultado = calcular(dados)
except (ValueError, TypeError):
    print("Os dados estão em formato inválido.")
```

### **4.3. Acessando a exceção: `as`**

```python
try:
    valor = int("abc")
except ValueError as erro:
    print(f"Erro capturado: {erro}")
    print(f"Tipo: {type(erro).__name__}")
```

```
Erro capturado: invalid literal for int() with base 10: 'abc'
Tipo: ValueError
```

### **4.4. A ordem dos `except` importa**

O Python usa o **primeiro** `except` compatível. Coloque os **mais específicos primeiro** e os mais gerais depois:

```python
try:
    x = [1, 2, 3][10]
except LookupError:         # captura IndexError também!
    print("Erro de busca")
except IndexError:          # NUNCA será executado
    print("Índice inválido")
```

Correto:

```python
try:
    x = [1, 2, 3][10]
except IndexError:
    print("Índice inválido")
except LookupError:
    print("Erro de busca (chave ou índice)")
```

### **4.5. `except Exception` e o *bare except***

```python
try:
    ...
except Exception as e:      # captura "quase tudo"
    print(f"Erro inesperado: {e}")
```

```python
try:
    ...
except:                     # EVITE: captura até Ctrl+C e SystemExit!
    pass
```

> **Regra:** capture **as exceções que você sabe tratar**. O `except Exception` serve como "rede de segurança" no nível mais externo do programa (por exemplo, para registrar o erro em um log). O `except:` sem tipo deve ser **evitado**, e o `except: pass` é considerado uma das piores práticas, pois **esconde erros**.

---

## **5. `else` e `finally`**

A estrutura completa é:

```python
try:
    # código que pode falhar
except TipoDeErro:
    # executa se ocorreu o erro
else:
    # executa SOMENTE se NÃO ocorreu exceção
finally:
    # executa SEMPRE (com ou sem erro)
```

```python
def ler_arquivo(caminho):
    try:
        f = open(caminho, encoding="utf-8")
    except FileNotFoundError:
        print(f"Arquivo '{caminho}' não encontrado.")
        return None
    else:
        conteudo = f.read()
        print("Leitura concluída com sucesso.")
        return conteudo
    finally:
        print("Fim da tentativa de leitura.")
```

**Por que usar o `else`?** Ele separa claramente o código que **pode falhar** (no `try`) do código que **depende do sucesso**. Assim, o `try` fica pequeno e você não captura, sem querer, erros de outras instruções.

**Por que usar o `finally`?** É o lugar para **liberar recursos** (fechar arquivos e conexões, restaurar estados), pois **sempre executa**, mesmo que haja `return`, `break` ou uma exceção não tratada.

```python
def teste():
    try:
        return "do try"
    finally:
        print("finally executou antes de retornar!")

print(teste())
# finally executou antes de retornar!
# do try
```

> Na prática, o comando `with` (visto em *Arquivos*) cuida do fechamento de recursos automaticamente, substituindo muitos usos do `finally`.

### **5.1. Resumo do fluxo**

| Situação                              | `try`            | `except` | `else`  | `finally`             |
| ------------------------------------- | :--------------: | :------: | :-----: | :-------------------: |
| Sem erro                              | ✔                | ✘        | ✔       | ✔                     |
| Erro tratado por um `except`          | ✔ (até o erro)   | ✔        | ✘       | ✔                     |
| Erro **não** tratado                  | ✔ (até o erro)   | ✘        | ✘       | ✔ (e a exceção segue) |

---

## **6. Levantando Exceções: `raise`**

Você também pode **provocar** exceções quando detecta uma situação inválida, em vez de deixar o programa seguir com dados errados.

```python
def raiz_quadrada(x):
    if x < 0:
        raise ValueError(f"Não existe raiz real de {x}.")
    return x ** 0.5

print(raiz_quadrada(9))      # 3.0
print(raiz_quadrada(-4))     # ValueError: Não existe raiz real de -4.
```

**Validando parâmetros de uma função:**

```python
def calcular_imc(peso, altura):
    if not isinstance(peso, (int, float)) or not isinstance(altura, (int, float)):
        raise TypeError("Peso e altura devem ser numéricos.")
    if peso <= 0 or altura <= 0:
        raise ValueError("Peso e altura devem ser positivos.")
    return peso / altura ** 2
```

Quem **chama** a função decide o que fazer:

```python
try:
    imc = calcular_imc(70, -1.75)
except ValueError as e:
    print(f"Dados inválidos: {e}")
```

> **Princípio:** uma função que não consegue cumprir sua tarefa deve **sinalizar o problema** com uma exceção, em vez de retornar um valor "mágico" (como `-1` ou `None`) que o chamador pode esquecer de verificar.

### **6.1. Relançando uma exceção (`raise` sozinho)**

Útil para fazer algo (como registrar um log) e deixar o erro seguir adiante:

```python
try:
    processar(dados)
except ValueError as e:
    print(f"[LOG] Falha ao processar: {e}")
    raise                    # relança a MESMA exceção
```

### **6.2. Encadeando exceções: `raise ... from`**

Converte uma exceção de baixo nível em outra mais significativa, **preservando a causa original**:

```python
def carregar_configuracao(texto):
    try:
        return int(texto)
    except ValueError as erro:
        raise RuntimeError("Configuração inválida: esperava um inteiro.") from erro
```

O traceback exibirá as duas exceções, indicando que a primeira foi a causa direta da segunda.

### **6.3. Exceções propagam pela pilha de chamadas**

Uma exceção não tratada **sobe** de função em função até encontrar um `except`:

```python
def nivel3():
    return 1 / 0

def nivel2():
    return nivel3()

def nivel1():
    try:
        return nivel2()
    except ZeroDivisionError:
        print("Tratado em nivel1!")
        return None

nivel1()    # Tratado em nivel1!
```

> **Boa prática:** trate a exceção **onde você sabe o que fazer** com ela. Muitas vezes isso é em uma camada superior (interface com o usuário), e não dentro da função de cálculo.

---

## **7. Exceções Personalizadas**

Para erros específicos do seu domínio, crie classes que **herdam de `Exception`** (ou de uma exceção mais específica):

```python
class ErroDeConvergencia(Exception):
    """Levantada quando um método iterativo não converge."""
    pass


class SaldoInsuficienteError(Exception):
    """Levantada ao tentar sacar mais do que o saldo disponível."""

    def __init__(self, saldo, valor):
        self.saldo = saldo
        self.valor = valor
        super().__init__(
            f"Saldo de R$ {saldo:.2f} insuficiente para sacar R$ {valor:.2f}."
        )
```

Uso:

```python
class ContaBancaria:
    def __init__(self, saldo=0):
        self.saldo = saldo

    def sacar(self, valor):
        if valor <= 0:
            raise ValueError("O valor do saque deve ser positivo.")
        if valor > self.saldo:
            raise SaldoInsuficienteError(self.saldo, valor)
        self.saldo -= valor


conta = ContaBancaria(100)

try:
    conta.sacar(250)
except SaldoInsuficienteError as e:
    print(e)                                  # Saldo de R$ 100.00 insuficiente ...
    print(f"Faltam R$ {e.valor - e.saldo:.2f}")
```

**Boas práticas para exceções próprias:**

- Nome terminado em `Error` (`ErroDeConvergencia`, `SaldoInsuficienteError`).
- Crie uma **classe base** para o seu módulo e derive as demais dela:

```python
class ErroMetodoNumerico(Exception):
    """Base para erros dos métodos numéricos."""

class ErroDeConvergencia(ErroMetodoNumerico): ...
class IntervaloInvalidoError(ErroMetodoNumerico): ...
```

Assim, quem usa seu módulo pode capturar `ErroMetodoNumerico` para tratar todos de uma vez.

---

## **8. `assert`: Verificando Suposições**

O `assert` testa uma condição; se for falsa, levanta `AssertionError`. Serve para **verificar suposições internas** do programa e para testes simples.

```python
def media(valores):
    assert len(valores) > 0, "A lista não pode estar vazia"
    return sum(valores) / len(valores)

media([])     # AssertionError: A lista não pode estar vazia
```

**Testes simples com `assert`:**

```python
def fatorial(n):
    return 1 if n == 0 else n * fatorial(n - 1)

assert fatorial(0) == 1
assert fatorial(5) == 120
print("Todos os testes passaram!")
```

> **Atenção:** os `assert` são **desativados** quando o Python roda com a opção `-O`. **Nunca** use `assert` para validar **dados do usuário** ou condições que precisam sempre ser verificadas: use `if` + `raise`.

| Use `assert` para...                                  | Use `raise` para...                               |
| ----------------------------------------------------- | ------------------------------------------------- |
| Suposições internas ("isso nunca deveria acontecer")  | Entradas inválidas, condições esperadas de erro   |
| Testes rápidos                                        | Validação de parâmetros públicos                  |

---

## **9. Padrões Úteis**

### **9.1. EAFP × LBYL**

Duas filosofias para lidar com situações de risco:

- **LBYL** (*Look Before You Leap*): "olhe antes de pular", **verifique** antes de agir.
- **EAFP** (*Easier to Ask Forgiveness than Permission*): "é mais fácil pedir perdão do que permissão", **tente** e trate a exceção.

```python
dados = {"nome": "Ana"}

# LBYL
if "idade" in dados:
    idade = dados["idade"]
else:
    idade = None

# EAFP (estilo mais "pythônico")
try:
    idade = dados["idade"]
except KeyError:
    idade = None

# Para dicionários, o mais simples:
idade = dados.get("idade")
```

O EAFP é especialmente indicado quando a verificação prévia **não é confiável**: por exemplo, verificar se um arquivo existe e depois abri-lo (o arquivo pode ser apagado entre as duas operações).

### **9.2. Laço de validação de entrada**

```python
def ler_inteiro(mensagem, minimo=None, maximo=None):
    """Pede um inteiro ao usuário até receber um valor válido."""
    while True:
        try:
            valor = int(input(mensagem))
        except ValueError:
            print("  Entrada inválida: digite um número inteiro.")
            continue

        if minimo is not None and valor < minimo:
            print(f"  O valor deve ser pelo menos {minimo}.")
        elif maximo is not None and valor > maximo:
            print(f"  O valor deve ser no máximo {maximo}.")
        else:
            return valor


idade = ler_inteiro("Idade (0 a 120): ", minimo=0, maximo=120)
```

Versão com tratamento de `Ctrl + C` e fim de entrada:

```python
try:
    idade = ler_inteiro("Idade: ", 0, 120)
except (KeyboardInterrupt, EOFError):
    print("\nEntrada cancelada pelo usuário.")
```

### **9.3. Tentar novamente (*retry*)**

```python
import time

def tentar_ler(caminho, tentativas=3, espera=1.0):
    for n in range(1, tentativas + 1):
        try:
            with open(caminho, encoding="utf-8") as f:
                return f.read()
        except OSError as e:
            print(f"Tentativa {n}/{tentativas} falhou: {e}")
            if n == tentativas:
                raise                 # esgotou as tentativas: propaga o erro
            time.sleep(espera)
```

### **9.4. Ignorando uma exceção de forma explícita: `suppress`**

```python
from contextlib import suppress
import os

with suppress(FileNotFoundError):
    os.remove("temporario.txt")      # se não existir, tudo bem
```

Muito mais claro do que um `try/except: pass` genérico.

### **9.5. Processando uma lista sem parar no primeiro erro**

```python
entradas = ["10", "3.5", "abc", "", "7", "xyz"]
validos, invalidos = [], []

for texto in entradas:
    try:
        validos.append(float(texto))
    except ValueError:
        invalidos.append(texto)

print(f"Válidos: {validos}")          # [10.0, 3.5, 7.0]
print(f"Inválidos: {invalidos}")      # ['abc', '', 'xyz']
```

### **9.6. Mantenha o `try` pequeno**

Coloque dentro do `try` **apenas** o código que pode gerar o erro esperado:

```python
# Ruim: o try cobre coisas demais e pode esconder bugs
try:
    n = int(input("n: "))
    resultado = processar(n)
    salvar(resultado)
    exibir(resultado)
except ValueError:
    print("Entrada inválida")

# Melhor
try:
    n = int(input("n: "))
except ValueError:
    print("Entrada inválida")
else:
    resultado = processar(n)
    salvar(resultado)
    exibir(resultado)
```

---

## **10. Registrando Erros: o Módulo `logging`**

Em vez de `print`, programas maiores usam **logs**: mensagens com **nível de severidade**, data/hora e destino configurável (tela ou arquivo).

| Nível       | Uso                                            |
| ----------- | ---------------------------------------------- |
| `DEBUG`     | Detalhes para depuração                        |
| `INFO`      | Funcionamento normal                           |
| `WARNING`   | Algo inesperado, mas o programa segue          |
| `ERROR`     | Uma operação falhou                            |
| `CRITICAL`  | Falha grave; o programa pode não continuar     |

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(message)s",
    filename="programa.log",
    encoding="utf-8",
)

logger = logging.getLogger(__name__)

logger.info("Programa iniciado")

try:
    resultado = 10 / 0
except ZeroDivisionError:
    logger.exception("Falha ao calcular a razão")   # registra a mensagem + traceback completo

logger.warning("Valor fora do intervalo esperado")
```

> `logger.exception()` só deve ser chamado **dentro de um `except`**; ele inclui automaticamente o *traceback* no log.

Para exibir o traceback manualmente:

```python
import traceback

try:
    1 / 0
except ZeroDivisionError:
    traceback.print_exc()                 # imprime o traceback na tela
    texto = traceback.format_exc()        # ou guarda como string
```

---

## **11. Erros Numéricos e Científicos**

### **11.1. Exceções do módulo `math`**

```python
import math

math.sqrt(-1)       # ValueError: math domain error
math.log(0)         # ValueError: math domain error
math.exp(1000)      # OverflowError: math range error
math.acos(2)        # ValueError: math domain error
```

```python
def log_seguro(x):
    try:
        return math.log(x)
    except ValueError:
        return float("nan")      # "not a number"
```

### **11.2 `inf` e `nan`**

Alguns cálculos com `float` não geram exceção, e sim valores especiais:

```python
print(1e308 * 10)                  # inf
print(float("inf") - float("inf")) # nan
print(float("nan") == float("nan"))  # False! (nan nunca é igual a nada)

import math
print(math.isnan(float("nan")))    # True
print(math.isinf(float("inf")))    # True
print(math.isfinite(3.5))          # True
```

> **Armadilha:** `nan` **se propaga** silenciosamente pelos cálculos e contamina o resultado. Verifique com `math.isnan` / `np.isnan`.

### **11.3 NumPy: avisos em vez de exceções**

O NumPy, por padrão, **não** levanta exceção em divisão por zero ou domínio inválido: ele emite um **aviso** e devolve `inf` ou `nan`.

```python
import numpy as np

a = np.array([1.0, 0.0, -1.0])

print(1 / a)           # [ 1. inf -1.]   (RuntimeWarning: divide by zero)
print(np.sqrt(a))      # [ 1.  0. nan]   (RuntimeWarning: invalid value)
```

Para transformar esses avisos em **exceções**, use `np.errstate`:

```python
with np.errstate(divide="raise", invalid="raise"):
    try:
        print(1 / a)
    except FloatingPointError as e:
        print(f"Erro numérico: {e}")      # divide by zero encountered in divide
```

### **11.4 Método de Newton robusto**

Um exemplo completo que reúne **exceções personalizadas**, **validação** e **tratamento**:

```python
class ErroDeConvergencia(Exception):
    """O método iterativo não convergiu no número máximo de iterações."""


def newton(f, df, x0, tol=1e-10, max_iter=50):
    """Método de Newton-Raphson com verificações de segurança."""
    if tol <= 0:
        raise ValueError("A tolerância deve ser positiva.")

    x = x0
    for i in range(1, max_iter + 1):
        derivada = df(x)
        if derivada == 0:
            raise ZeroDivisionError(f"Derivada nula em x = {x}; o método falhou.")

        x_novo = x - f(x) / derivada

        if abs(x_novo - x) < tol:
            return x_novo, i
        x = x_novo

    raise ErroDeConvergencia(f"Sem convergência após {max_iter} iterações.")


# Uso
try:
    raiz, iteracoes = newton(lambda x: x ** 2 - 2, lambda x: 2 * x, x0=1.0)
    print(f"Raiz ≈ {raiz:.8f} ({iteracoes} iterações)")
except ZeroDivisionError as e:
    print(f"Falha: {e}")
except ErroDeConvergencia as e:
    print(f"Falha: {e}")

# Caso problemático: f(x) = x² + 1 não tem raiz real
try:
    newton(lambda x: x ** 2 + 1, lambda x: 2 * x, x0=1.0)
except (ZeroDivisionError, ErroDeConvergencia) as e:
    print(f"Falha: {e}")        # Falha: Derivada nula em x = 0.0; o método falhou.
```

### **11.5 Bisseção com validação do intervalo**

```python
def bissecao(f, a, b, tol=1e-6, max_iter=100):
    if a >= b:
        raise ValueError("É necessário a < b.")
    if f(a) * f(b) > 0:
        raise ValueError("f(a) e f(b) devem ter sinais opostos (sem garantia de raiz).")

    for _ in range(max_iter):
        c = (a + b) / 2
        if abs(f(c)) < tol or (b - a) / 2 < tol:
            return c
        if f(a) * f(c) < 0:
            b = c
        else:
            a = c
    raise ErroDeConvergencia("A bisseção não atingiu a tolerância.")
```

### **11.6 Leitura robusta de dados experimentais**

```python
def carregar_medicoes(caminho):
    """Lê valores numéricos de um arquivo, relatando as linhas problemáticas."""
    valores, problemas = [], []

    try:
        with open(caminho, encoding="utf-8") as f:
            for n, linha in enumerate(f, start=1):
                texto = linha.strip()
                if not texto:
                    continue
                try:
                    valores.append(float(texto))
                except ValueError:
                    problemas.append((n, texto))
    except FileNotFoundError:
        raise FileNotFoundError(f"Arquivo de medições '{caminho}' não encontrado.") from None

    if not valores:
        raise ValueError("Nenhum valor numérico válido foi encontrado.")

    return valores, problemas
```

---

## **12. Depuração (*Debugging*)**

Quando o erro é **lógico**, o Python não ajuda com mensagens. Algumas estratégias:

### **12.1 `print()` estratégico**

```python
def media_ponderada(notas, pesos):
    print(f"[DEBUG] notas={notas}, pesos={pesos}")
    soma = sum(n * p for n, p in zip(notas, pesos))
    print(f"[DEBUG] soma ponderada={soma}")
    return soma / sum(pesos)
```

Simples e eficaz, mas lembre-se de **remover** depois (ou use `logging.debug`).

### **12.2 O depurador: `breakpoint()`**

Insere um **ponto de parada**: o programa pausa e abre o depurador interativo (`pdb`).

```python
def calcular(x):
    resultado = x * 2
    breakpoint()                  # a execução pausa aqui
    return resultado + 1
```

| Comando | Ação                                        |
| ------- | ------------------------------------------- |
| `n`     | *next*: executa a próxima linha             |
| `s`     | *step*: entra dentro da função chamada      |
| `c`     | *continue*: segue até o próximo ponto       |
| `p x`   | imprime o valor de `x`                      |
| `l`     | lista o código ao redor                     |
| `q`     | encerra a depuração                         |

> Editores como **VS Code** e **PyCharm** possuem depuradores gráficos: clique ao lado do número da linha para definir um *breakpoint* e use "Depurar" para executar passo a passo, inspecionando as variáveis.

### **12.3 Método sistemático para achar bugs**

1. **Reproduza** o erro com o menor exemplo possível.
2. **Leia** a mensagem e o traceback.
3. **Formule uma hipótese** do que está errado.
4. **Verifique** a hipótese (`print`, depurador, teste).
5. **Corrija** e **teste novamente**, inclusive casos que já funcionavam.
6. Pergunte-se: *"posso evitar que esse erro volte?"* (um `assert`, uma validação, um teste).

> **Dica clássica:** explique o código, em voz alta, para alguém (ou para um "patinho de borracha"). Muitas vezes você descobre o erro no meio da explicação.

### **12.4 Testes automatizados (noção)**

Para projetos maiores, use bibliotecas de teste como **`pytest`**:

```python
# test_matematica.py
import pytest

def dividir(a, b):
    if b == 0:
        raise ZeroDivisionError("divisor nulo")
    return a / b

def test_divisao_normal():
    assert dividir(10, 2) == 5

def test_divisao_por_zero():
    with pytest.raises(ZeroDivisionError):
        dividir(1, 0)
```

```bash
pip install pytest
pytest
```

---

## **13. Avisos (*Warnings*)**

Nem todo problema merece interromper o programa. Os **avisos** informam sem parar a execução:

```python
import warnings

def raiz(x):
    if x < 0:
        warnings.warn("Valor negativo recebido; usando o módulo.", UserWarning)
        x = abs(x)
    return x ** 0.5

print(raiz(-9))     # exibe o aviso e retorna 3.0
```

Para ignorar ou transformar avisos em erros:

```python
warnings.simplefilter("ignore")   # ignora todos (use com cautela)
warnings.simplefilter("error")    # transforma avisos em exceções
```

---

## **14. Boas Práticas**

- **Capture exceções específicas.** Evite `except:` e `except Exception:` (exceto no nível mais externo, para registrar o erro).
- **Nunca** silencie erros com `except: pass`.
- Mantenha o bloco `try` **pequeno**; use `else` para o que depende do sucesso.
- Use `finally` ou `with` para **liberar recursos**.
- **Levante exceções** com mensagens **claras**, indicando o valor problemático e o que era esperado.
- Valide **entradas externas** (usuário, arquivos, redes). Dados internos já validados não precisam de verificação repetida.
- Trate o erro onde há **algo útil a fazer**; caso contrário, deixe-o propagar.
- Crie **exceções próprias** para erros do seu domínio, com uma classe base.
- Use `raise ... from` para preservar a causa original.
- Use **`logging`** em vez de `print` em programas maiores.
- Não use exceções como *substituto de lógica* simples, quando um `if` é mais claro.
- Escreva **testes** para os casos de erro também.

---

## **15. Erros Comuns ao Tratar Erros**

| Problema                                                    | Explicação / Correção                                                                   |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `except: pass`                                              | Esconde erros e bugs; trate algo específico ou ao menos registre o erro                 |
| `except Exception` logo no começo                           | Captura demais e impede os `except` específicos seguintes                               |
| `except` genérico antes do específico                       | O específico nunca é executado; inverta a ordem                                         |
| `try` cobrindo dezenas de linhas                            | Dificulta saber a origem; reduza o escopo                                               |
| Usar `assert` para validar dados do usuário                 | Pode ser desativado com `-O`; use `raise`                                               |
| Retornar `-1` ou `None` para sinalizar erro                 | Fácil de ignorar; prefira levantar uma exceção                                          |
| Esquecer que o `finally` sempre executa                     | Um `return` no `finally` sobrescreve o do `try` e engole exceções                       |
| Mensagem de erro genérica (`"Erro!"`)                       | Inclua o que falhou, o valor e o esperado                                               |
| Capturar e apenas imprimir (`print(e)`) em código de biblioteca | Quem chama não fica sabendo; relance ou use `logging`                               |
| Comparar com `nan` por `==`                                 | Sempre `False`; use `math.isnan` / `np.isnan`                                           |
| Esperar exceção do NumPy em divisão por zero                | Ele só avisa e devolve `inf`/`nan`; use `np.errstate`                                   |
| `except ValueError, TypeError:` (sem parênteses)            | Só é aceito no Python 3.14+ (e sem `as`); use sempre `except (ValueError, TypeError):`  |

---

## **Resumo Rápido**

| Comando / Recurso              | Para que serve                                                       |
| ------------------------------ | -------------------------------------------------------------------- |
| `try / except`                 | Capturar e tratar exceções                                           |
| `except (A, B) as e`           | Tratar vários tipos e acessar a exceção                              |
| `else`                         | Executa se **não** houve exceção                                     |
| `finally`                      | Executa **sempre** (limpeza de recursos)                             |
| `raise Erro("mensagem")`       | Levantar uma exceção                                                 |
| `raise` (sozinho)              | Relançar a exceção atual                                             |
| `raise A from B`               | Encadear exceções, preservando a causa                               |
| `class MeuErro(Exception)`     | Criar exceção personalizada                                          |
| `assert cond, "msg"`           | Verificar suposições internas / testes                               |
| `contextlib.suppress(Erro)`    | Ignorar explicitamente uma exceção                                   |
| `logging` / `logger.exception` | Registrar erros com nível e traceback                                |
| `traceback.print_exc()`        | Exibir o traceback atual                                             |
| `breakpoint()`                 | Abrir o depurador                                                    |
| `warnings.warn`                | Emitir avisos sem interromper o programa                             |
| `np.errstate`                  | Controlar avisos/erros de ponto flutuante no NumPy                   |

---

## **Resumo**

- Existem erros de **sintaxe**, **exceções** (execução) e erros **lógicos** (resultado errado, sem mensagem).
- O ***traceback*** deve ser lido **de baixo para cima**: tipo do erro, linha onde ocorreu e a cadeia de chamadas.
- `try/except` captura exceções; use `except` **específicos** e na ordem correta (do mais específico ao mais geral).
- `else` roda se **não** houve erro; `finally` roda **sempre**.
- `raise` levanta exceções (inclusive **personalizadas**, herdando de `Exception`); `raise ... from` preserva a causa.
- Valide entradas externas, **sinalize problemas com exceções** (e não com valores mágicos) e **nunca** esconda erros com `except: pass`.
- Use `assert` para suposições internas e testes, não para validar dados do usuário.
- `logging`, `breakpoint()` e testes ajudam a **encontrar e prevenir** erros.
- Em computação numérica, fique atento a `ZeroDivisionError`, erros de domínio, `inf`/`nan`, avisos do NumPy e não convergência de métodos iterativos.

---

## **Referências**

- [Tutorial oficial: Erros e Exceções](https://docs.python.org/pt-br/3/tutorial/errors.html)
- [Documentação: Exceções embutidas](https://docs.python.org/pt-br/3/library/exceptions.html)
- [Documentação: módulo `logging`](https://docs.python.org/pt-br/3/library/logging.html)
- [Documentação: depurador `pdb`](https://docs.python.org/pt-br/3/library/pdb.html)
- [Documentação: `contextlib.suppress`](https://docs.python.org/pt-br/3/library/contextlib.html#contextlib.suppress)
- [`pytest`](https://docs.pytest.org/)