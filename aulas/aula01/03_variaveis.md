# **Variáveis:**

Em Python, temos quatro tipos de variáveis básicas para estudar nesse primeiro momento:

- `int`
- `float`
- `str`
- `bool`

O interessante do Python é que não é necessário explicitar o tipo da variável quando ela for declarada, então, para o pseudocódigo, temos o seguinte:

```
<nome_da_variavel> = <valor_atribuido>
```

Podemos verificar o tipo de cada variável com a função `type()`.

## `int`

O tipo `int` corresponde a variaveís numéricas do tipo exclusivamente inteiras. Vejamos um exemplo:

```python
idade = 20

print(idade)
print(type(idade))
```

```output
20
<class 'int'>
```

## `float`

O tipo `float` trata de números reais ou números de ponto flutuante. Vale ressaltar que não é usada a vírgula `,` para separar a parte inteira, mas sim o ponto `.`. Veja um exemplo:

```python
altura = 1.83

print(altura)
print(type(altura))
```

```output
1.83
<class 'float'>
```

## `str`

O tipo `str` armazena dados de texto, em forma de letras, números ou símbolos. Também são chamados de caracteres.

```python
texto = "Bom dia! Hoje eu acordei às 06h00."

print(texto)
print(type(texto))
```

```output
Bom dia! Hoje eu acordei às 06h00.
<class 'str'>
```

## `bool`

Por fim, temos o tipo `bool`, que representa os tipos referentes ao valores booleanos, `True` ou `False`, `1` ou `0`. São úteis para os conceitos de [estrutura condicional](../aula02/estrutura_condicional.md).

```python
bool1 = True
bool2 = False

print(bool1)
print(type(bool1))
print(bool2)
print(type(bool2))
```

```output
True
<class 'bool'>
False
<class 'bool'>
```

## Verificando os tipos

Para verificar os tipos das variáveis, podemos utilizar a função `type()`, como mostrada anteriormente, juntamente com os operadores `is` ou `==`. Veja os exemplos:

```python
idade = 24

print(type(idade) == int)
print(type(idade) == float)
print(type(idade) is str)
print(type(idade) is bool)
```

```output
True
False
False
False
```