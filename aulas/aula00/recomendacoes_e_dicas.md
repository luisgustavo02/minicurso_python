# **Recomendações e Dicas**

Neste documento, vamos destacar as principais recomendações, dicas e pré-requisitos para quem deseja iniciar a programar em linguagem Python.

## **Pré-requisitos**

Para começar a programar em **Python** na sua máquina, é necessário instalar o **Python**, por meio do site oficial [python.org/downloads](https://www.python.org/downloads/) ou loja do seu sistema (Windows Store, por exemplo).

Com o **Python** instalado, é possível começar a programar, seja via VS Code, PyCharm ou outro ambiente de desenvolvimento (IDE).

## **Python vs Notebook Python**

Existem dois tipos de arquivos principais, um código-fonte Python, no formato `.py` e um arquivo notebook, no formato `.ipynb`.

Um arquivo de extensão `.py` contém puramente o código Python com os comandos, instruções e operações. Já o arquivo `.ipynb` é uma mistura de blocos de códigos com blocos de texto em **Markdown**.

## **Ambiente de Desenvolvimento**

Para toda linguagem de programação, é comum desenvolvermos os projetos em ambientes de desenvolvimento ou IDEs. Aqui seguem algumas alternativas e comentários pessoais:

### **Google Colab**

<img src="https://thumb.wikimedia.org/wikipedia/commons/thumb/9/9b/Google_Colab_pic.png/1280px-Google_Colab_pic.png?utm_source=en.wikipedia.org&utm_campaign=index&utm_content=thumbnail" alt="Logo do Google Colab" height=100>

O [Google Colab](https://colab.research.google.com) é um ambiente virtual de desenvolvimento por meio de arquivos **Notebook Python**. A principal vantagem do Google Colab são a conectividade com sua conta do Google, onde seus arquivos são sempre salvos no Google Drive, e a computação na nuvem, ou seja toda a programação realizada é executada nos servidores da Google.

### **Jupyter Notebook**

<img src="https://thumb.wikimedia.org/wikipedia/commons/thumb/3/38/Jupyter_logo.svg/1280px-Jupyter_logo.svg.png?utm_source=pt.wikipedia.org&utm_campaign=index&utm_content=thumbnail" alt="Logo do Projeto Jupyter" height=100>

O [Jupyter Notebook](https://jupyter.org/#jupyter-notebook-the-classic-notebook-interface) é um software de programação disponibilizado pelo [Projeto Jupyter](https://jupyter.org/). É um projeto *open-source* que nasceu do projeto IPython em 2014.

### **OnlineGDB**

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS-l9TKiySANCP27Xwp3M9yyr3GxE5p_3iXhxSpjclwauwTIluUsBG0b0Y2&s=10" alt="Logo do OnlineGDB" height=100>

O [OnlineGDB](https://www.onlinegdb.com/#) é uma plataforma que permite a construção e execução de códigos em diversas linguagens de programação, como Python, C, C++, Java, PHP, etc. Não é necessário criar conta, mas caso deseje, é possível salvar códigos e projetos no site.

### **PyCharm**

<img src="https://lp.jetbrains.com/static/upl/5154/2025/09/01/172017-0.9229018.png" alt="Logo do PyCharm" height=100>

Disponibilizado pela empresa JetBrains, o [PyCharm](https://www.jetbrains.com/pycharm/) é uma IDE específica e completa para Python, com artifícios para trabalhar com base de dados, web, FastAPI, Jupyter, *Machine Learning*, entre outras funcionalidades e objetivos.

### **VS Code**

<img src="https://images-eds-ssl.xboxlive.com/image?url=4rt9.lXDC4H_93laV1_eHHFT949fUipzkiFOBH3fAiZZUCdYojwUyX2aTonS1aIwMrx6NUIsHfUHSLzjGJFxxj7kCzMIlSC20SNjaJf9GmESvWFqgy6FNrwzWSIu2lzePyWSz8zg09RAX43OFexidzEE3_7l3auaKk4w9ktJdqg-&format=source" alt="Logo do VS Code" height=100>

O [VS Code](https://code.visualstudio.com/) é um editor do texto que permite a instalação de extensões, as quais suportam inúmeras linguagens de programação, auxiliam no desenvolvimento e permitem a execução de código. Também é mantido pela Microsoft.

## **Boas Práticas**

Para todas as linguagens de programação, temos alguns aspectos em comum pelos programadores, ditas como as boas práticas de programação. Caso interesse, fica como recomendação os os materiais:

**Recomendação para todos:**

- [Playlist do Livro Clean Code - Código Fonte TV](https://youtube.com/playlist?list=PLVc5bWuiFQ8H5P-7QB1_3LOJkOZNMnnpg&si=0VsSQqeT5hiVMwyv)
- [Curso de Algoritmos e Lógica de Programação - CursoEmVideo](https://www.cursoemvideo.com/curso/curso-de-algoritmo/)

### **Comentários**

É sempre importante comentar no código suas ideias, observações e até autoria do código em alguns projetos. Para isso, o Python tem duas maneiras principais de se comentar em um *script*: com uma hashtag `#`, comentando em uma única linha, ou com três aspas no início e no fim, sejam elas simples `'''` ou duplas `"""`, e permitindo que se comentem em múltiplas linhas. Veja o exemplo a seguir:

```python
"""
author: Luís Gustavo
GitHub: luisgustavo02

Projeto de calculadora
"""

# Imagine aqui uma função de soma
def soma():
    pass

# Imagine aqui a função de subtração
def subtracao():
    pass

# TODO: Terminar função de multiplicação
def multiplicacao():
    pass

# ...

# Função da calculadora
def calculadora():
    pass
```

Com os comentários, o próprio autor não se perde nas ideias e facilita a organização, além de facilitar a leitura para outros programadores.

### **Nomenclatura de Variáveis e Funções**

Para criar variáveis ou funções, existem alguns padrões que os programadores seguem:

- `PascalCase`
- `camelCase`
- `snake_case`
- `SCREAMING_SNAKE_CASE`

Comumente, variáveis com o nome todo em maiúsculo, são constantes durante todo o código.