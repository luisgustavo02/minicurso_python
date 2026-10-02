# **Bibliotecas Básicas**

Uma das maiores forças do Python é o seu ecossistema de **bibliotecas** (também chamadas de **módulos**): conjuntos de funções, constantes e classes prontas para uso. Em C, o equivalente seria o `#include <math.h>`; em Python, usamos `import`.

O Python já vem com a chamada **Biblioteca Padrão** (*Standard Library*), que dispensa instalação. Nesta seção veremos as mais úteis para o início.

**Objetivos:**

- Importar módulos de diferentes formas.
- Usar as bibliotecas `math`, `random`, `statistics`, `time` e `datetime`.
- Consultar a documentação de um módulo com `help()` e `dir()`.
- Conhecer o `pip` para instalar bibliotecas de terceiros.

---

## **1. Como Importar**

Existem quatro formas principais de importar:

**1.1. Importar o módulo inteiro**

```python
import math
print(math.sqrt(16))
```

```output
4.0
```

**1.2. Importar o módulo inteiro com um apelido**

```python
import math as m
print(m.sqrt(16))
```

```output
4.0
```

**1.3. Importar apenas o que será usado**

```python
from math import sqrt, pi
print(sqrt(16), pi)
```

```output
4.0 3.141592653589793
```

**1.4. Importa tudo (NÃO recomendado)**

```python
from math import *
print(sqrt(16))
```

```output
4.0
```

| Forma                   | Vantagem                                | Desvantagem                                    |
| ----------------------- | --------------------------------------- | ---------------------------------------------- |
| `import modulo`         | Deixa claro de onde vem cada função     | Código um pouco mais longo                     |
| `import modulo as apelido` | Abrevia nomes longos (`np`, `plt`)   | Exige conhecer a convenção do apelido          |
| `from modulo import x`  | Código mais curto                       | Pode gerar conflito de nomes                   |
| `from modulo import *`  | Nenhuma                                 | Polui o código e dificulta a leitura           |

> **Boa prática:** Coloque todos os `import` no **início** do arquivo.

---

## **2. Biblioteca `math`**

Funções e constantes matemáticas para números reais.

**Constantes:**

```python
import math

print(math.pi)    # 3.141592653589793
print(math.e)     # 2.718281828459045
print(math.inf)   # inf
```

**Principais funções:**

| Função                 | Descrição                              | Exemplo                         | Resultado |
| ---------------------- | -------------------------------------- | ------------------------------- | --------- |
| `math.sqrt(x)`         | Raiz quadrada                          | `math.sqrt(25)`                 | `5.0`     |
| `math.pow(x, y)`       | Potência (retorna `float`)             | `math.pow(2, 3)`                | `8.0`     |
| `math.ceil(x)`         | Arredonda para cima                    | `math.ceil(4.1)`                | `5`       |
| `math.floor(x)`        | Arredonda para baixo                   | `math.floor(4.9)`               | `4`       |
| `math.trunc(x)`        | Remove a parte decimal                 | `math.trunc(-4.9)`              | `-4`      |
| `math.factorial(n)`    | Fatorial                               | `math.factorial(5)`             | `120`     |
| `math.gcd(a, b)`       | Máximo divisor comum                   | `math.gcd(12, 18)`              | `6`       |
| `math.log(x)`          | Logaritmo natural                      | `math.log(math.e)`              | `1.0`     |
| `math.log10(x)`        | Logaritmo base 10                      | `math.log10(1000)`              | `3.0`     |
| `math.log(x, b)`       | Logaritmo na base `b`                  | `math.log(8, 2)`                | `3.0`     |
| `math.exp(x)`          | e elevado a x                          | `math.exp(0)`                   | `1.0`     |
| `math.hypot(a, b)`     | Hipotenusa `sqrt(a² + b²)`             | `math.hypot(3, 4)`              | `5.0`     |
| `math.isclose(a, b)`   | Compara reais com tolerância           | `math.isclose(0.1+0.2, 0.3)`    | `True`    |

**Trigonometria** (os ângulos são em **radianos**):

```python
import math

angulo_graus = 30
angulo_rad = math.radians(angulo_graus)

print(math.sin(angulo_rad))
print(math.cos(angulo_rad))
print(math.tan(angulo_rad))
print(math.degrees(math.pi))
```

Também existem `math.asin`, `math.acos`, `math.atan`, `math.atan2`, `math.sinh`, `math.cosh` e `math.tanh`.

**Exemplo — Resolvendo uma equação do 2º grau:**

```python
import math

a, b, c = 1, -5, 6
delta = b ** 2 - 4 * a * c

x1 = (-b + math.sqrt(delta)) / (2 * a)
x2 = (-b - math.sqrt(delta)) / (2 * a)

print(f"Raízes: x1 = {x1}, x2 = {x2}")   # Raízes: x1 = 3.0, x2 = 2.0
```

> **Atenção:** `math.sqrt` de número negativo gera `ValueError`. Para números complexos, existe a biblioteca `cmath`.

---

## **3. Biblioteca `random`**

Gera números pseudoaleatórios.

```python
import random

print(random.random())            # float em [0.0, 1.0)
print(random.randint(1, 6))       # inteiro entre 1 e 6 (inclusive)
print(random.uniform(1.5, 4.5))   # float entre 1.5 e 4.5
print(random.randrange(0, 10, 2)) # 0, 2, 4, 6 ou 8

frutas = ["maçã", "banana", "uva", "manga"]
print(random.choice(frutas))      # um elemento aleatório
random.shuffle(frutas)            # embaralha a lista
print(frutas)
print(random.sample(frutas, 2))   # 2 elementos distintos
```

**Reprodutibilidade com `seed`:**

Como os números são *pseudoaleatórios*, é possível fixar a sequência com uma **semente**, o que é essencial para reproduzir experimentos e simulações.

```python
import random

random.seed(42)
print(random.randint(1, 100))   # sempre o mesmo valor a cada execução
```

**Exemplo — Jogo de adivinhação:**

```python
import random

secreto = random.randint(1, 10)
palpite = int(input("Adivinhe o número entre 1 e 10: "))

if palpite == secreto:
    print("Acertou!")
else:
    print(f"Errou! O número era {secreto}.")
```

> **Nota:** O módulo `random` **não é seguro** para criptografia. Para senhas e tokens, existe o módulo `secrets`.

---

## **4. Biblioteca `statistics`**

Estatística descritiva básica sobre listas de números.

```python
import statistics as st

notas = [7.5, 8.0, 6.5, 9.0, 8.0, 5.5]

print(st.mean(notas))      # média
print(st.median(notas))    # mediana
print(st.mode(notas))      # moda -> 8.0
print(st.stdev(notas))     # desvio padrão amostral
print(st.variance(notas))  # variância amostral
```

Para conjuntos de dados grandes e cálculos numéricos pesados, usaremos o **NumPy** na Aula 03.

---

## **5. Bibliotecas `time` e `datetime`**

**`time`** — medir tempo de execução e pausar o programa:

```python
import time

inicio = time.perf_counter()

soma = 0
for i in range(1_000_000):
    soma += i

fim = time.perf_counter()
print(f"Tempo de execução: {fim - inicio:.4f} s")

time.sleep(2)   # pausa por 2 segundos
```

> *Obs.:* O `for` será estudado em detalhes na Aula 02; aqui ele serve apenas de exemplo.

**`datetime`** — datas e horas:

```python
from datetime import datetime, date, timedelta

agora = datetime.now()
print(agora)                                # 2026-09-30 14:35:12.123456
print(agora.strftime("%d/%m/%Y %H:%M"))     # 30/09/2026 14:35

hoje = date.today()
prova = date(2026, 12, 15)
print((prova - hoje).days, "dias até a prova")

daqui_a_7_dias = hoje + timedelta(days=7)
print(daqui_a_7_dias)
```

Códigos de formatação mais usados em `strftime`: `%d` (dia), `%m` (mês), `%Y` (ano com 4 dígitos), `%H` (hora), `%M` (minuto), `%S` (segundo).

---

## **6. Outras Bibliotecas Úteis da Biblioteca Padrão**

Aqui, vemos algumas das principais bibliotecas já inclusas no Python.

| Módulo        | Para que serve                                                |
| ------------- | ------------------------------------------------------------- |
| `collections` | Estruturas de dados especializadas (`Counter`, `deque`)       |
| `csv`         | Leitura e escrita de arquivos CSV                             |
| `decimal`     | Números decimais com precisão controlada                      |
| `fractions`   | Números racionais exatos                                      |
| `itertools`   | Ferramentas para iteração avançada                            |
| `json`        | Leitura e escrita de arquivos JSON                            |
| `os`          | Interação com o sistema operacional (pastas, arquivos)        |
| `pathlib`     | Manipulação moderna de caminhos de arquivos                   |
| `re`          | Manipulação de expressões regulares                           |
| `sys`         | Informações do interpretador e argumentos de linha de comando |
| `typing`      | Manipulação de tipos de variáveis                             |

```python
import sys

print(sys.version)   # versão do Python em uso
```

---

## **7. Explorando um Módulo: `dir()` e `help()`**

Não é preciso decorar tudo! O próprio Python ajuda:

```python
import math

print(dir(math))        # lista todos os nomes disponíveis no módulo
help(math.hypot)        # exibe a documentação da função
help(math)              # documentação completa do módulo
```

A documentação oficial também é excelente: [docs.python.org/pt-br/3/library](https://docs.python.org/pt-br/3/library/index.html).

---

## **8. Bibliotecas de Terceiros e o `pip`**

Além da biblioteca padrão, existem centenas de milhares de pacotes criados pela comunidade, disponíveis no repositório [PyPI](https://pypi.org/). O gerenciador de pacotes que os instala é o **`pip`**.

O `pip` vem junto com o Python, quando instalado. Para verificar, pode executar um dos comandos abaixo:

```bash
python -m pip --version
pip --version
pip -V
```

Para executar esses comandos em um Notebook Python, pode utilizar `!` ou `%%` antes do comando.

Vejamos a lista dos principais comandos:

| Comando                       | O que faz?                            |
| ----------------------------- | ------------------------------------- |
| `pip install *pacote*`        | Instala o *pacote* inserido           |
| `pip list`                    | Mostra a lista de pacotes instalados  |
| `pip show *pacote*`           | Mostra as informações do *pacote*     |
| `pip uninstall *pacote*`      | Desinstala o *pacote* inserido        |
| `pip --help` ou `pip -h`      | Mostra um texto de ajuda              |
| `pip --version` ou `pip -V`   | Mostra a versão atual do `pip`        |

Bibliotecas que veremos na **Aula 03**:

- **NumPy** — arrays e computação numérica.
- **Matplotlib** — gráficos.
- **SymPy** — matemática simbólica.

> **Dica:** Se o comando `pip` não for reconhecido, tente `python -m pip install *pacote*` (ou `python3 -m pip instal *pacote*` no Linux/macOS).