# **Classes e Programação Orientada a Objetos (POO)**

> **Resumo** dos principais conceitos de classes em Python. A ideia é ter uma visão geral rápida, com exemplos curtos, para você começar a usar POO e saber onde se aprofundar.

Até aqui, organizamos nossos programas com **variáveis** e **funções**. Na **Programação Orientada a Objetos (POO)**, juntamos **dados** e **funções que operam sobre esses dados** em uma única estrutura: o **objeto**. Você já usa objetos o tempo todo: uma lista (`list`) possui dados (seus elementos) e métodos (`append`, `sort`...).

**Objetivos:**

- Entender os conceitos de classe, objeto, atributo e método.
- Criar classes com `__init__` e `self`.
- Conhecer métodos especiais (`__str__`, `__repr__`, `__add__`...).
- Aplicar encapsulamento, herança e polimorfismo.
- Saber quando usar (ou não) classes.

---

## **1. Conceitos Básicos**

| Conceito     | Significado                                                              | Analogia                    |
| ------------ | ------------------------------------------------------------------------ | --------------------------- |
| **Classe**   | Molde/modelo que define como são os objetos                              | Planta de uma casa          |
| **Objeto**   | Uma **instância** concreta da classe                                     | Uma casa construída         |
| **Atributo** | Variável que pertence ao objeto (os **dados**)                           | Cor, área, nº de quartos    |
| **Método**   | Função que pertence ao objeto (os **comportamentos**)                    | Abrir porta, acender luz    |

```python
print(type([1, 2, 3]))    # <class 'list'>  -> "list" é uma classe
lista = [3, 1, 2]         # "lista" é um objeto (instância) da classe list
lista.sort()              # "sort" é um método
```

---

## **2. Criando uma Classe**

Vamos construir uma classe para representar um aluno do minicurso, como exemplo.

```python
class Aluno:
    """Representa um aluno do minicurso."""

    def __init__(self, nome, curso):
        self.nome = nome          # atributo de instância
        self.curso = curso
        self.notas = []

    def adicionar_nota(self, nota):
        self.notas.append(nota)

    def media(self):
        if not self.notas:
            return 0
        return sum(self.notas) / len(self.notas)


# Criando objetos (instâncias)
ana = Aluno("Ana", "Eng. Eletrônica")
bruno = Aluno("Bruno", "Ciência da Computação")

ana.adicionar_nota(8.5)
ana.adicionar_nota(9.0)

print(ana.nome)         # Ana
print(ana.media())      # 8.75
print(bruno.media())    # 0
```

**Elementos importantes:**

- `class Nome:` define a classe. Por convenção, usa-se **PascalCase** (`ContaBancaria`).
- `__init__` é o **construtor**: roda automaticamente ao criar o objeto.
- `self` é a **referência ao próprio objeto**. É sempre o **primeiro parâmetro** dos métodos, mas **não** é passado na chamada (o Python faz isso sozinho).
- `self.atributo` cria/acessa um atributo do objeto.
- Cada objeto possui seus **próprios** atributos: `ana.notas` e `bruno.notas` são listas diferentes.

> **Erro comum:** esquecer o `self` na definição do método → `TypeError: ... takes 1 positional argument but 2 were given`.

---

## **3. Atributos de Classe × Atributos de Instância**

```python
class Circulo:
    PI = 3.14159                  # atributo de CLASSE (compartilhado)
    quantidade = 0

    def __init__(self, raio):
        self.raio = raio          # atributo de INSTÂNCIA (individual)
        Circulo.quantidade += 1

    def area(self):
        return Circulo.PI * self.raio ** 2


c1 = Circulo(2)
c2 = Circulo(5)
print(c1.area())            # 12.56636
print(Circulo.quantidade)   # 2
```

---

## **4. Métodos Especiais (*Dunder Methods*)**

Métodos com nome entre **dois underscores** (`__nome__`) permitem que nossos objetos se comportem como os tipos nativos.

| Método                 | Quando é chamado                       | Exemplo de uso      |
| ---------------------- | -------------------------------------- | ------------------- |
| `__init__(self, ...)`  | Criação do objeto                      | `Classe(...)`       |
| `__str__(self)`        | `print(obj)`, `str(obj)` (legível)     | `print(v)`          |
| `__repr__(self)`       | Representação "técnica" do objeto      | `repr(v)`, console  |
| `__add__(self, o)`     | Operador `+`                           | `a + b`             |
| `__sub__`, `__mul__`   | Operadores `-` e `*`                   | `a - b`, `a * 2`    |
| `__eq__(self, o)`      | Operador `==`                          | `a == b`            |
| `__lt__(self, o)`      | Operador `<` (permite usar `sorted`)   | `a < b`             |
| `__len__(self)`        | Função `len(obj)`                      | `len(obj)`          |
| `__getitem__(self, i)` | Indexação `obj[i]`                     | `obj[0]`            |
| `__abs__(self)`        | Função `abs(obj)`                      | `abs(v)`            |

**Exemplo — Vetor 2D:**

```python
import math

class Vetor2D:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Vetor2D({self.x}, {self.y})"

    def __add__(self, outro):
        return Vetor2D(self.x + outro.x, self.y + outro.y)

    def __mul__(self, k):                   # multiplicação por escalar
        return Vetor2D(self.x * k, self.y * k)

    def __eq__(self, outro):
        return self.x == outro.x and self.y == outro.y

    def __abs__(self):
        return math.hypot(self.x, self.y)


v1 = Vetor2D(1, 2)
v2 = Vetor2D(3, 4)

print(v1 + v2)        # Vetor2D(4, 6)
print(v1 * 3)         # Vetor2D(3, 6)
print(abs(v2))        # 5.0
print(v1 == Vetor2D(1, 2))   # True
```

Sem `__repr__`/`__str__`, o `print(objeto)` mostraria algo pouco útil, como `<__main__.Vetor2D object at 0x...>`.

---

## **5. Encapsulamento**

**Encapsulamento** é proteger os dados internos do objeto, controlando como são lidos e modificados. Em Python, isso é feito por **convenção**:

| Forma        | Significado                                                              |
| ------------ | ------------------------------------------------------------------------ |
| `atributo`   | Público: pode ser usado livremente                                       |
| `_atributo`  | "Protegido" por convenção: uso interno da classe, não mexa de fora       |
| `__atributo` | "Privado": o Python altera o nome internamente (*name mangling*)         |

### **Propriedades (`@property`)**

Permitem usar um método como se fosse um atributo, com validação:

```python
class ContaBancaria:
    def __init__(self, titular, saldo=0):
        self.titular = titular
        self._saldo = saldo

    @property
    def saldo(self):                      # leitura: conta.saldo
        return self._saldo

    def depositar(self, valor):
        if valor <= 0:
            raise ValueError("O depósito deve ser positivo.")
        self._saldo += valor

    def sacar(self, valor):
        if valor > self._saldo:
            raise ValueError("Saldo insuficiente.")
        self._saldo -= valor


conta = ContaBancaria("Ana", 100)
conta.depositar(50)
conta.sacar(30)
print(conta.saldo)        # 120
conta.saldo = 1000        # AttributeError: a propriedade é somente leitura
```

> **Filosofia Python:** *"Somos todos adultos aqui."* Nada impede o acesso a `_saldo`, mas a convenção indica que não deve ser feito.

---

## **6. Herança**

Uma classe **filha** (subclasse) **herda** atributos e métodos de uma classe **mãe** (superclasse), podendo estendê-los ou modificá-los. Promove **reuso de código**.

```python
class Forma:
    def __init__(self, nome):
        self.nome = nome

    def area(self):
        raise NotImplementedError("Implemente na subclasse.")

    def __str__(self):
        return f"{self.nome}: área = {self.area():.2f}"


class Retangulo(Forma):
    def __init__(self, base, altura):
        super().__init__("Retângulo")      # chama o construtor da classe mãe
        self.base = base
        self.altura = altura

    def area(self):
        return self.base * self.altura


class Circulo(Forma):
    def __init__(self, raio):
        super().__init__("Círculo")
        self.raio = raio

    def area(self):
        return 3.14159 * self.raio ** 2


r = Retangulo(3, 4)
print(r)                       # Retângulo: área = 12.00
print(isinstance(r, Forma))    # True
```

- `class Filha(Mae):` indica a herança.
- `super()` acessa a classe mãe (muito usado no `__init__`).
- `isinstance(obj, Classe)` testa se um objeto pertence à classe (ou a uma subclasse).
- Python permite **herança múltipla** (`class C(A, B):`), mas use com cuidado.

---

## **7. Polimorfismo**

Objetos de classes diferentes respondem à **mesma chamada** (mesmo nome de método) cada um do seu jeito. Quem usa não precisa saber o tipo exato.

```python
formas = [Retangulo(3, 4), Circulo(2), Retangulo(5, 5)]

for forma in formas:
    print(forma)          # cada uma calcula a área à sua maneira

area_total = sum(f.area() for f in formas)
```

Em Python vale o *duck typing*: *"se anda como pato e faz quack como pato, é um pato"*. Não importa a classe, basta o objeto ter o método esperado.

---

## **8. `@classmethod` e `@staticmethod`**

| Tipo             | Primeiro parâmetro | Uso típico                                         |
| ---------------- | ------------------ | -------------------------------------------------- |
| Método comum     | `self` (instância) | Opera sobre os dados do objeto                     |
| `@classmethod`   | `cls` (a classe)   | Construtores alternativos                          |
| `@staticmethod`  | nenhum             | Função utilitária que "mora" dentro da classe      |

```python
class Temperatura:
    def __init__(self, celsius):
        self.celsius = celsius

    @classmethod
    def de_fahrenheit(cls, f):               # construtor alternativo
        return cls((f - 32) * 5 / 9)

    @staticmethod
    def eh_valida(celsius):                  # não usa self nem cls
        return celsius >= -273.15


t = Temperatura.de_fahrenheit(212)
print(t.celsius)                        # 100.0
print(Temperatura.eh_valida(-300))      # False
```

---

## **9. Composição: "tem um" × Herança: "é um"**

- **Herança** → relação **"é um"**: um `Circulo` **é uma** `Forma`.
- **Composição** → relação **"tem um"**: um `Carro` **tem um** `Motor`.

```python
class Motor:
    def __init__(self, potencia):
        self.potencia = potencia

class Carro:
    def __init__(self, modelo, potencia):
        self.modelo = modelo
        self.motor = Motor(potencia)        # composição

carro = Carro("Hatch", 120)
print(carro.motor.potencia)    # 120
```

> **Dica:** Na dúvida, prefira composição à herança: ela é mais flexível.

---

## **10. Atalho: `dataclass`**

Para classes que servem principalmente para **guardar dados**, o módulo `dataclasses` gera `__init__`, `__repr__` e `__eq__` automaticamente:

```python
from dataclasses import dataclass

@dataclass
class Ponto:
    x: float
    y: float
    z: float = 0.0

p = Ponto(1.5, 2.0)
print(p)                        # Ponto(x=1.5, y=2.0, z=0.0)
print(p == Ponto(1.5, 2.0))     # True
```

---

## **11. Exemplo Científico: Polinômio**

```python
class Polinomio:
    """Polinômio representado por coeficientes [a0, a1, a2, ...]."""

    def __init__(self, coeficientes):
        self.coef = list(coeficientes)

    def __call__(self, x):                       # permite usar p(x)
        return sum(a * x ** k for k, a in enumerate(self.coef))

    def derivada(self):
        return Polinomio(k * a for k, a in enumerate(self.coef) if k > 0)

    def __repr__(self):
        return f"Polinomio({self.coef})"


p = Polinomio([-2, 0, 1])      # x² - 2
print(p(3))                    # 7
print(p.derivada())            # Polinomio([0, 2])  -> 2x
print(p.derivada()(5))         # 10
```

Com isso, os métodos numéricos que fizemos com funções (bisseção, Newton) funcionam diretamente com objetos `Polinomio`, pois ele é "chamável".

---

## **12. Quando Usar Classes?**

**Use classes quando:**

- Há **dados e comportamentos** que andam juntos (conta bancária, aluno, matriz, sensor).
- Você precisa de **vários objetos** do mesmo tipo, cada um com seu estado.
- Quer criar **tipos próprios** com operadores e métodos intuitivos.

**Funções simples bastam quando:**

- A tarefa é uma transformação direta de entrada em saída (como `media(lista)`).
- Não há estado a ser guardado entre as chamadas.

---

## **13. Erros Comuns**

| Erro                                                              | Explicação / Correção                                             |
| ----------------------------------------------------------------- | ----------------------------------------------------------------- |
| Esquecer `self` em um método                                      | `TypeError` ao chamar o método                                    |
| Esquecer `self.` ao usar atributos (`nome` em vez de `self.nome`) | `NameError` ou criação de variável local                          |
| Escrever `init` em vez de `__init__`                              | O construtor nunca é executado                                    |
| Atributo mutável na classe (`lista = []`)                         | É **compartilhado** entre todos os objetos; crie no `__init__`    |
| Esquecer o `return` em `__str__`/`__repr__`                       | `TypeError: __str__ returned non-string`                          |
| Esquecer `super().__init__()` na subclasse                        | Atributos da classe mãe não são criados                           |

---

## **Exercícios**

1. Crie a classe `Retangulo` com `base` e `altura`, e os métodos `area()` e `perimetro()`.
2. Crie a classe `ContaBancaria` com `depositar`, `sacar` (sem permitir saldo negativo) e `extrato`.
3. Crie `Vetor3D` com `+`, `-`, produto escalar, `abs()` e `__repr__`.
4. Crie a classe `Fracao` com numerador e denominador, `__add__`, `__mul__`, `__str__` e simplificação (use `math.gcd`).
5. Crie `Animal` (com `falar()`), e as subclasses `Cachorro` e `Gato`. Percorra uma lista de animais chamando `falar()`.
6. Crie a classe `Matriz` (lista de listas) com `__add__`, `__mul__` (produto matricial) e `transposta()`, reaproveitando o que foi visto na Aula 02.
7. Crie a classe `Turma` que guarde vários objetos `Aluno` e retorne o aluno de maior média e o ranking ordenado.
8. **Desafio:** Estenda a classe `Polinomio` com `__add__`, `__mul__` e `__repr__` que mostre o polinômio no formato `x^2 - 2`.

---

## **Resumo Rápido**

| Conceito              | Em uma frase                                                                  |
| --------------------- | ----------------------------------------------------------------------------- |
| **Classe**            | Molde que define atributos e métodos.                                         |
| **Objeto**            | Instância criada a partir da classe: `obj = Classe(...)`.                     |
| **`__init__`**        | Construtor, chamado ao criar o objeto.                                        |
| **`self`**            | Referência ao próprio objeto, 1º parâmetro dos métodos.                       |
| **Atributo**          | Dado do objeto (`self.x`); de classe é compartilhado por todos.               |
| **Método**            | Função definida dentro da classe.                                             |
| **Métodos especiais** | `__str__`, `__repr__`, `__add__`, `__eq__`... integram o objeto à linguagem.  |
| **Encapsulamento**    | Proteger os dados: `_atributo` e `@property`.                                 |
| **Herança**           | `class Filha(Mae)`; reaproveita e estende código ("é um").                    |
| **Polimorfismo**      | Mesmo método, comportamentos diferentes conforme o objeto.                    |
| **Composição**        | Um objeto contém outros ("tem um").                                           |
| **`dataclass`**       | Atalho para classes que apenas guardam dados.                                 |

---

## **Referências**

- [Tutorial oficial: Classes](https://docs.python.org/pt-br/3/tutorial/classes.html)
- [Documentação: modelo de dados (métodos especiais)](https://docs.python.org/pt-br/3/reference/datamodel.html)
- [Documentação: módulo `dataclasses`](https://docs.python.org/pt-br/3/library/dataclasses.html)