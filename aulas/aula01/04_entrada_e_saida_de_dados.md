# **Entrada e Saída de Dados**

Em todo programa ou código, é necessário definir bem os parâmetros de entrada e saída. Para isso, veremos as duas principais maneiras de realizar o *input* e *output* do programa, com as funções `print()` e `input()`.

## `print()`

Como já citado anteriormente, a função `print()` vai mostrar na tela ou no terminal do computador um texto ou valor que for inserido como parâmetro, independente do tipo de variável e da quantidade de parâmetros.

O principal método para imprimir texto é o `print()` com aspas simples `''` ou aspas duplas `""`, junto com operações, formatações ou inserção de variáveis.

Também existem as `f-strings`, que são strings formatadas, onde variáveis podem ser inseridas no meio delas. Vejamos alguns exemplos.

```python
name = "Luís Gustavo"
lastName = "Gomes"
age = 24
birthYear = 2002

print("Bom dia! Meu nome é ", name)
print("Nome com último sobrenome: {} {}".format(name, lastName))
print(f"Eu tenho {age} anos e nasci em {birthYear}")
```

```output
Bom dia! Meu nome é  Luís Gustavo
Nome com último sobrenome: Luís Gustavo Gomes
Eu tenho 24 anos e nasci em 2002
```

Também existem alguns comandos especiais para strings, como a quebra de linha e tabulação. São eles:

| Expressão regular | Significado     |
| :---------------- | :-------------: |
| \n                | Quebra de linha |
| \t                | Tabulação       |

No assunto de [Bibliotecas Básicas](aulas/aula01/06_bibliotecas_basicas), veremos um pouco mais de expressões regulares com a biblioteca `re`.

```python
print("Exemplo\nde\ntexto\ncom\nquebra de linha")
print("Exemplo\tde\ttexto\tcom\ttabulação")
```

```output
Exemplo
de
texto
com
quebra de linha
Exemplo	de	texto	com	tabulação
```

# `input()`

A principal entrada de dados por meio do usuário é a função de `input()`. Isso acontece por dois motivos principais:

- A função pode imprimir texto também, da mesma forma que a função `print()`.
- A saída da função é o valor, texto ou comando que o usuário inseriu, ou seja o `input()` é atribuído a alguma variável.

Um detalhe importante é que TODA a saída da função `input()` é uma `str`, mas pode ser convertida para `int`, `float` ou `bool` como mostra os exemplos abaixo:

```python
name = input("Digite seu nome: ")
age = int(input("Digite sua idade: "))
born = input("Digite sua data de nascimento (DD/MM/AAAA): ")
height = float(input("Digite sua altura: "))
isMarried = bool(input("É casado? (True | False) "))

print(f"Nome: {name} -> Tipo: {type(name)}")
print(f"Idade: {age} -> Tipo: {type(age)}")
print(f"Data de nascimento: {born} -> Tipo: {type(born)}")
print(f"Altura: {height} -> Tipo: {type(height)}")
print(f"Casado: {isMarried} -> Tipo: {type(isMarried)}")
```

```input
Luís Gustavo
24
19/07/2002
1.84
False
```

```output
Digite seu nome: 
Digite sua idade: 
Digite sua data de nascimento (DD/MM/AAAA): 
Digite sua altura: 
É casado? (True | False) 
Nome: Luís Gustavo -> Tipo: <class 'str'>
Idade: 24 -> Tipo: <class 'int'>
Data de nascimento: 19/07/2002 -> Tipo: <class 'str'>
Altura: 1.84 -> Tipo: <class 'float'>
Casado: True -> Tipo: <class 'bool'>
```