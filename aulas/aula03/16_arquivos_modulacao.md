# **Arquivos e Modularização**

Até agora, todos os nossos dados "viviam" dentro do programa: quando ele terminava, tudo era perdido. Para **guardar resultados**, **ler medições** de experimentos ou **processar dados** de outras fontes, precisamos trabalhar com **arquivos**.

Além disso, à medida que os programas crescem, manter tudo em um único arquivo `.py` torna-se inviável. A **modularização** nos permite dividir o código em **módulos** e **pacotes** organizados e reutilizáveis.

**Objetivos:**

- Abrir, ler e escrever arquivos de texto com `open()` e `with`.
- Tratar erros comuns de arquivos (`FileNotFoundError`, `PermissionError`).
- Manipular caminhos e pastas com `pathlib` (e `os`).
- Trabalhar com arquivos **CSV** e **JSON**.
- Conhecer a leitura e escrita de arquivos **binários**.
- Criar **módulos** e **pacotes** próprios e organizá-los em um projeto.
- Aplicar `import`, `from ... import`, `as` e `if __name__ == "__main__"`.

---

# **PARTE 1 — ARQUIVOS**

## **1. Conceitos Básicos**

Um **arquivo** é um conjunto de dados armazenado de forma permanente (disco, SSD, pen drive), identificado por um **nome** e um **caminho**.

| Tipo            | Conteúdo                                       | Exemplos                              |
| --------------- | ---------------------------------------------- | ------------------------------------- |
| **Texto**       | Caracteres legíveis, divididos em linhas       | `.txt`, `.csv`, `.json`, `.py`, `.md` |
| **Binário**     | Bytes brutos, não legíveis em um editor comum  | `.png`, `.pdf`, `.mp3`, `.exe`, `.npy`|

### **1.1. Caminhos (*paths*)**

- **Caminho relativo:** a partir da pasta em que o programa está sendo executado (diretório de trabalho). Ex.: `dados/notas.txt`.
- **Caminho absoluto:** completo, desde a raiz do sistema. Ex.: `C:\Users\ana\dados\notas.txt` (Windows) ou `/home/ana/dados/notas.txt` (Linux/macOS).

```python
import os

print(os.getcwd())    # mostra o diretório de trabalho atual
```

> **Dica:** Use sempre `/` nos caminhos (funciona em todos os sistemas, inclusive Windows) ou o módulo `pathlib`. Se precisar da barra invertida, use strings *raw*: `r"C:\Users\ana\notas.txt"`.

---

## **2. Abrindo e Fechando Arquivos**

A função `open()` retorna um **objeto arquivo**.

```python
arquivo = open("notas.txt", "r", encoding="utf-8")
conteudo = arquivo.read()
arquivo.close()          # sempre feche o arquivo!
```

**Sintaxe:** `open(caminho, modo, encoding=...)`

### **2.1. Modos de abertura**

| Modo  | Significado                                                               | Se o arquivo não existe | Se existe                    |
| ----- | ------------------------------------------------------------------------- | ----------------------- | ---------------------------- |
| `"r"` | **Leitura** (padrão)                                                      | `FileNotFoundError`     | Lê do início                 |
| `"w"` | **Escrita**                                                               | Cria                    | **Apaga** todo o conteúdo!   |
| `"a"` | **Acréscimo** (*append*): escreve no final                                | Cria                    | Preserva e adiciona ao final |
| `"x"` | Criação **exclusiva**                                                     | Cria                    | `FileExistsError`            |
| `"r+"`| Leitura **e** escrita                                                     | `FileNotFoundError`     | Preserva                     |
| `"b"` | Modo **binário** (combina com os demais: `"rb"`, `"wb"`)                  | —                       | —                            |

> **Atenção:** o modo `"w"` **destrói** o conteúdo anterior do arquivo, sem pedir confirmação!

### **2.2. O comando `with` (recomendado)**

O `with` fecha o arquivo **automaticamente**, mesmo se ocorrer um erro dentro do bloco:

```python
with open("notas.txt", "r", encoding="utf-8") as arquivo:
    conteudo = arquivo.read()
# aqui o arquivo já foi fechado

print(conteudo)
```

### **2.3. Codificação (*encoding*)**

Sempre informe `encoding="utf-8"` ao abrir arquivos de texto. Sem isso, o Python usa a codificação padrão do sistema (no Windows, geralmente `cp1252`), e acentos podem aparecer errados (`Ã§Ã£o` em vez de `ção`).

---

## **3. Escrevendo em Arquivos**

### **3.1. `write()`**

Escreve **uma string** (não adiciona quebra de linha automaticamente):

```python
with open("notas.txt", "w", encoding="utf-8") as f:
    f.write("Ana;8.5\n")
    f.write("Bruno;7.0\n")
    f.write("Carla;9.2\n")
```

Conteúdo gerado:

```
Ana;8.5
Bruno;7.0
Carla;9.2
```

> `write()` só aceita `str`. Para números, converta: `f.write(str(10))` ou use f-strings.

```python
valores = [3.14, 2.71, 1.41]

with open("constantes.txt", "w", encoding="utf-8") as f:
    for v in valores:
        f.write(f"{v}\n")
```

### **3.2. `writelines()`**

Escreve uma **lista de strings** (também não adiciona `\n`):

```python
linhas = ["primeira\n", "segunda\n", "terceira\n"]

with open("texto.txt", "w", encoding="utf-8") as f:
    f.writelines(linhas)
```

### **3.3. `print()` com `file=`**

```python
with open("saida.txt", "w", encoding="utf-8") as f:
    print("Resultado final:", 42, file=f)
    print("Fim!", file=f)
```

### **3.4. Acrescentando ao final (`"a"`)**

```python
with open("log.txt", "a", encoding="utf-8") as f:
    f.write("Nova linha adicionada ao final\n")
```

Exemplo prático — registro de eventos (*log*) com data e hora:

```python
from datetime import datetime

def registrar(mensagem, arquivo="log.txt"):
    agora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
    with open(arquivo, "a", encoding="utf-8") as f:
        f.write(f"[{agora}] {mensagem}\n")

registrar("Programa iniciado")
registrar("Cálculo concluído")
```

### **3.5. Criando arquivos no Notebook**

É possível criar arquivos utilizando comandos com `!` ou `%%`. O bloco de código pode ser escrito como mostra o exemplo abaixo:

```python
%%writefile teste.txt
Exemplo de arquivo
```

---

## **4. Lendo Arquivos**

Considere o arquivo `notas.txt`:

```
Ana;8.5
Bruno;7.0
Carla;9.2
```

### **4.1. `read()` — lê tudo como uma única string**

```python
with open("notas.txt", encoding="utf-8") as f:
    texto = f.read()

print(texto)
print(len(texto))
```

Também aceita o número de caracteres a ler: `f.read(5)`.

### **4.2. `readline()` — lê uma linha por vez**

```python
with open("notas.txt", encoding="utf-8") as f:
    primeira = f.readline()
    segunda = f.readline()

print(repr(primeira))    # 'Ana;8.5\n'
print(repr(segunda))     # 'Bruno;7.0\n'
```

A linha lida **inclui o `\n`** no final. Retorna `""` (string vazia) quando chega ao fim do arquivo.

### **4.3. `readlines()` — lê tudo como uma lista de linhas**

```python
with open("notas.txt", encoding="utf-8") as f:
    linhas = f.readlines()

print(linhas)    # ['Ana;8.5\n', 'Bruno;7.0\n', 'Carla;9.2\n']
```

### **4.4. Percorrendo linha a linha (forma mais usada)**

O objeto arquivo é **iterável**. Esta forma é elegante e eficiente, pois não carrega o arquivo inteiro na memória:

```python
with open("notas.txt", encoding="utf-8") as f:
    for linha in f:
        linha = linha.strip()              # remove o "\n"
        nome, nota = linha.split(";")
        print(f"{nome} tirou {float(nota)}")
```

### **4.5. Limpando e tratando as linhas**

```python
notas = {}

with open("notas.txt", encoding="utf-8") as f:
    for linha in f:
        linha = linha.strip()
        if not linha:                  # ignora linhas em branco
            continue
        nome, nota = linha.split(";")
        notas[nome] = float(nota)

print(notas)                           # {'Ana': 8.5, 'Bruno': 7.0, 'Carla': 9.2}
print(sum(notas.values()) / len(notas))
```

### **4.6. Posição no arquivo: `tell()` e `seek()`**

Um arquivo aberto tem um "cursor". Os métodos de leitura avançam esse cursor.

```python
with open("notas.txt", encoding="utf-8") as f:
    print(f.read(3))     # Ana
    print(f.tell())      # 3   (posição atual)
    f.seek(0)            # volta ao início
    print(f.readline())  # Ana;8.5
```

---

## **5. Caminhos e Pastas com `pathlib`**

O módulo `pathlib` oferece uma forma moderna e portátil de manipular caminhos, funcionando igualmente em Windows, Linux e macOS.

```python
from pathlib import Path

p = Path("dados") / "notas.csv"       # o operador / junta partes do caminho

print(p)             # dados/notas.csv  (ou dados\notas.csv no Windows)
print(p.name)        # notas.csv
print(p.stem)        # notas
print(p.suffix)      # .csv
print(p.parent)      # dados
```

### **5.1. Principais operações**

| Operação                                  | Descrição                                 |
| ----------------------------------------- | ----------------------------------------- |
| `p.exists()`                              | O caminho existe?                         |
| `p.is_file()` / `p.is_dir()`              | É arquivo? É pasta?                       |
| `p.mkdir(parents=True, exist_ok=True)`    | Cria pasta (e as intermediárias)          |
| `p.iterdir()`                             | Itera sobre o conteúdo de uma pasta       |
| `p.glob("*.txt")`                         | Busca arquivos por padrão                 |
| `p.rglob("*.py")`                         | Busca **recursivamente** nas subpastas    |
| `p.read_text(encoding="utf-8")`           | Lê o arquivo inteiro como string          |
| `p.write_text(texto, encoding=...)`       | Escreve a string no arquivo               |
| `p.rename(novo)`                          | Renomeia/move                             |
| `p.unlink()`                              | Apaga o arquivo                           |
| `Path.cwd()` / `Path.home()`              | Pasta atual / pasta do usuário            |

```python
from pathlib import Path

pasta = Path("resultados")
pasta.mkdir(exist_ok=True)

arquivo = pasta / "saida.txt"
arquivo.write_text("Resultado: 42\n", encoding="utf-8")

print(arquivo.read_text(encoding="utf-8"))

if arquivo.exists():
    print("O arquivo existe!")

# Listando todos os arquivos .txt da pasta atual
for txt in Path(".").glob("*.txt"):
    print(txt.name)
```

### **5.2. Caminho relativo ao próprio script**

Para evitar erros quando o programa é executado de outra pasta:

```python
from pathlib import Path

PASTA_BASE = Path(__file__).parent         # pasta onde está o arquivo .py
caminho_dados = PASTA_BASE / "dados" / "notas.txt"
```

> `__file__` não existe em Notebooks/Jupyter nem no terminal interativo.

### **5.3. O módulo `os` e o `shutil`**

Ainda muito comuns em códigos existentes:

```python
import os
import shutil

print(os.getcwd())                   # pasta atual
print(os.listdir("."))               # lista nomes da pasta
print(os.path.exists("notas.txt"))   # existe?
print(os.path.join("dados", "notas.txt"))

os.makedirs("backup", exist_ok=True)
shutil.copy("notas.txt", "backup/notas_copia.txt")   # copia arquivo
os.remove("backup/notas_copia.txt")                  # apaga arquivo
```

---

## **6. Arquivos CSV**

**CSV** (*Comma-Separated Values*) é um formato de tabela em texto, em que cada linha é um registro e os campos são separados por um delimitador. É muito usado para trocar dados com planilhas (Excel, Google Sheets).

Exemplo `alunos.csv`:

```
nome,idade,nota
Ana,20,8.5
Bruno,22,7.0
Carla,21,9.2
```

> **Atenção:** o Excel em português costuma usar **ponto e vírgula** (`;`) como delimitador, pois a vírgula é o separador decimal. Use `delimiter=";"` nesses casos.

Para tratar corretamente aspas, vírgulas dentro de campos e quebras de linha, use o módulo `csv` em vez de `split(",")`.

### **6.1. Escrevendo CSV**

```python
import csv

with open("alunos.csv", "w", newline="", encoding="utf-8") as f:
    escritor = csv.writer(f)                     # delimiter=";" se necessário
    escritor.writerow(["nome", "idade", "nota"])
    escritor.writerow(["Ana", 20, 8.5])
    escritor.writerows([
        ["Bruno", 22, 7.0],
        ["Carla", 21, 9.2],
    ])
```

> O parâmetro `newline=""` evita linhas em branco extras no Windows.

### **6.2. Lendo CSV**

```python
import csv

with open("alunos.csv", newline="", encoding="utf-8") as f:
    leitor = csv.reader(f)
    cabecalho = next(leitor)           # lê e guarda a 1ª linha
    for linha in leitor:
        print(linha)                   # ['Ana', '20', '8.5']
```

> Todos os valores são lidos como **strings**; converta com `int()` ou `float()`.

### **6.3. `DictReader` e `DictWriter`: acessando por nome de coluna**

```python
import csv

with open("alunos.csv", newline="", encoding="utf-8") as f:
    leitor = csv.DictReader(f)
    notas = []
    for linha in leitor:
        print(linha["nome"], linha["nota"])
        notas.append(float(linha["nota"]))

print(f"Média da turma: {sum(notas) / len(notas):.2f}")
```

```python
import csv

alunos = [
    {"nome": "Ana", "idade": 20, "nota": 8.5},
    {"nome": "Bruno", "idade": 22, "nota": 7.0},
]

with open("saida.csv", "w", newline="", encoding="utf-8") as f:
    escritor = csv.DictWriter(f, fieldnames=["nome", "idade", "nota"])
    escritor.writeheader()
    escritor.writerows(alunos)
```

---

## **7. Arquivos JSON**

**JSON** (*JavaScript Object Notation*) é um formato de texto para dados **estruturados** (dicionários, listas, números, textos, booleanos). É o formato padrão de configurações e de comunicação entre sistemas e APIs.

```json
{
    "nome": "Ana",
    "notas": [8.5, 9.0],
    "ativo": true,
    "orientador": null
}
```

**Correspondência entre tipos:**

| Python            | JSON               |
| ----------------- | ------------------ |
| `dict`            | objeto `{ }`       |
| `list`, `tuple`   | array `[ ]`        |
| `str`             | string             |
| `int`, `float`    | número             |
| `True` / `False`  | `true` / `false`   |
| `None`            | `null`             |

### **7.1. Salvando e carregando**

```python
import json

dados = {
    "nome": "Ana",
    "notas": [8.5, 9.0],
    "ativo": True,
    "orientador": None,
}

# Salvar em arquivo
with open("aluno.json", "w", encoding="utf-8") as f:
    json.dump(dados, f, indent=4, ensure_ascii=False)

# Carregar de arquivo
with open("aluno.json", encoding="utf-8") as f:
    lido = json.load(f)

print(lido["notas"])      # [8.5, 9.0]
print(lido == dados)      # True
```

- `indent=4` deixa o arquivo legível.
- `ensure_ascii=False` mantém os acentos (caso contrário, saem como `\u00e7`).

### **7.2. Convertendo de/para strings**

```python
texto = json.dumps(dados, ensure_ascii=False)    # dict -> str
objeto = json.loads(texto)                       # str -> dict
```

> **Cuidados:** tuplas viram listas; chaves de dicionário viram sempre **strings**; objetos próprios (classes) não são serializados diretamente.

---

## **8. Arquivos Binários**

No modo binário (`"rb"`, `"wb"`), lemos e escrevemos **bytes**, sem nenhuma conversão de texto. Serve para imagens, áudio, PDFs etc.

```python
# Copiando um arquivo binário (por exemplo, uma imagem) em blocos
with open("foto.png", "rb") as origem, open("foto_copia.png", "wb") as destino:
    while True:
        bloco = origem.read(4096)       # lê até 4096 bytes
        if not bloco:                   # chegou ao fim
            break
        destino.write(bloco)
```

### **8.1. `pickle`: salvando objetos Python**

Grava objetos Python quaisquer (listas, dicionários, instâncias de classes) em binário:

```python
import pickle

dados = {"x": [1, 2, 3], "nome": "Ana"}

with open("dados.pkl", "wb") as f:
    pickle.dump(dados, f)

with open("dados.pkl", "rb") as f:
    recuperado = pickle.load(f)
```

> **Segurança:** **nunca** carregue (`pickle.load`) arquivos de origem desconhecida: eles podem executar código malicioso. Para dados simples, prefira JSON ou CSV.

### **8.2. Um aperitivo: arquivos numéricos com NumPy**

Para dados científicos, o NumPy (próximo tópico) facilita muito a leitura e o salvamento de tabelas numéricas:

```python
import numpy as np

tabela = np.loadtxt("medicoes.csv", delimiter=",", skiprows=1)
np.savetxt("resultado.csv", tabela * 2, delimiter=",")
```

---

## **9. Aplicações**

### **9.1. Estatísticas de medições**

Arquivo `medicoes.txt` (uma medida por linha):

```
12.5
13.1
11.8
abc
12.9

13.4
```

```python
import math

def ler_numeros(caminho):
    valores = []
    with open(caminho, encoding="utf-8") as f:
        for n, linha in enumerate(f, start=1):
            linha = linha.strip()
            if not linha:
                continue
            try:
                valores.append(float(linha))
            except ValueError:
                print(f"Aviso: linha {n} ignorada ({linha!r})")
    return valores

valores = ler_numeros("medicoes.txt")

n = len(valores)
media = sum(valores) / n
desvio = math.sqrt(sum((v - media) ** 2 for v in valores) / (n - 1))

with open("relatorio.txt", "w", encoding="utf-8") as f:
    f.write(f"Quantidade: {n}\n")
    f.write(f"Média: {media:.3f}\n")
    f.write(f"Desvio padrão: {desvio:.3f}\n")
```

### **9.2. Frequência de palavras em um texto**

```python
from pathlib import Path

texto = Path("livro.txt").read_text(encoding="utf-8").lower()

for simbolo in ".,;:!?()\"'":
    texto = texto.replace(simbolo, "")

frequencia = {}
for palavra in texto.split():
    frequencia[palavra] = frequencia.get(palavra, 0) + 1

# 5 palavras mais frequentes
top5 = sorted(frequencia.items(), key=lambda par: par[1], reverse=True)[:5]
for palavra, qtd in top5:
    print(f"{palavra:<15}{qtd:>5}")
```

### **9.3. Numerando as linhas de um arquivo**

```python
with open("entrada.txt", encoding="utf-8") as origem, \
     open("numerado.txt", "w", encoding="utf-8") as destino:
    for i, linha in enumerate(origem, start=1):
        destino.write(f"{i:03d}: {linha}")
```

### **9.4. Tabela de valores de uma função**

```python
import math

with open("seno.csv", "w", encoding="utf-8") as f:
    f.write("x,sen(x)\n")
    for grau in range(0, 361, 30):
        x = math.radians(grau)
        f.write(f"{grau},{math.sin(x):.4f}\n")
```

### **9.5. Agenda persistente com JSON**

```python
import json
from pathlib import Path

ARQUIVO = Path("agenda.json")

def carregar():
    if ARQUIVO.exists():
        return json.loads(ARQUIVO.read_text(encoding="utf-8"))
    return {}

def salvar(agenda):
    ARQUIVO.write_text(json.dumps(agenda, indent=4, ensure_ascii=False),
                       encoding="utf-8")

agenda = carregar()
agenda["Ana"] = "(81) 99999-0000"
salvar(agenda)
```

---

# **PARTE 2 — MODULARIZAÇÃO**

## **10. Por que Modularizar?**

Imagine um programa de 3.000 linhas em um único arquivo. Encontrar um erro, reaproveitar uma função em outro projeto ou dividir o trabalho com colegas seria muito difícil. A **modularização** consiste em **dividir o programa em partes independentes e coesas**.

**Vantagens:**

- **Organização:** cada arquivo trata de um assunto (cálculos, leitura de dados, interface...).
- **Reutilização:** o mesmo módulo serve para vários projetos.
- **Manutenção:** erros ficam localizados e mais fáceis de corrigir.
- **Trabalho em equipe:** cada pessoa cuida de um módulo.
- **Testabilidade:** módulos pequenos são mais fáceis de testar.
- **Evita conflitos de nomes:** cada módulo tem o seu próprio espaço de nomes (*namespace*).

**Terminologia:**

| Termo             | O que é                                                       |
| ----------------- | ------------------------------------------------------------- |
| **Módulo**        | Um único arquivo `.py` com funções, classes e variáveis       |
| **Pacote**        | Uma **pasta** que agrupa módulos (e possui um `__init__.py`)  |
| **Biblioteca**    | Conjunto de pacotes/módulos distribuído para uso (ex.: NumPy) |

---

## **11. Criando e Importando Módulos**

**Todo arquivo `.py` já é um módulo!**

**`geometria.py`:**

```python
"""Funções de geometria plana."""

import math

PI = math.pi

def area_circulo(raio):
    """Área de um círculo."""
    return PI * raio ** 2

def area_retangulo(base, altura):
    """Área de um retângulo."""
    return base * altura

def distancia(p1, p2):
    """Distância entre dois pontos (x, y)."""
    return math.hypot(p2[0] - p1[0], p2[1] - p1[1])
```

**`main.py`** (na mesma pasta):

```python
import geometria

print(geometria.area_circulo(2))
print(geometria.PI)
print(geometria.distancia((0, 0), (3, 4)))    # 5.0
```

### **11.1. Formas de importar**

```python
# 1) Módulo inteiro (mantém o prefixo: mais claro)
import geometria
geometria.area_circulo(2)

# 2) Módulo com apelido
import geometria as geo
geo.area_circulo(2)

# 3) Apenas itens específicos
from geometria import area_circulo, distancia
area_circulo(2)

# 4) Item específico com apelido
from geometria import area_retangulo as area_ret

# 5) Tudo (NÃO recomendado: polui o espaço de nomes)
from geometria import *
```

### **11.2. Como o Python encontra os módulos?**

Ao ver `import geometria`, o Python procura, **nesta ordem**:

1. Nos módulos embutidos (`sys`, `math`...).
2. Na **pasta do script** que está sendo executado.
3. Nas pastas listadas em `sys.path` (inclui bibliotecas instaladas com `pip`).

```python
import sys

for caminho in sys.path:
    print(caminho)
```

> **Atenção:** se você criar um arquivo `math.py` ou `random.py`, ele **esconde** o módulo padrão de mesmo nome! Escolha nomes que não conflitem.

### **11.3. O módulo é executado na importação (uma única vez)**

```python
# modulo_teste.py
print("Importando modulo_teste...")

def ola():
    print("Olá!")
```

```python
import modulo_teste     # imprime: Importando modulo_teste...
import modulo_teste     # nada acontece: já foi importado
```

O Python guarda os módulos já importados em cache (`sys.modules`). Se você alterar um módulo enquanto o programa roda (por exemplo, em um Notebook), é preciso reiniciar ou usar `importlib.reload(modulo)`.

---

## **12. `if __name__ == "__main__"`**

Todo módulo possui a variável especial `__name__`:

- Quando o arquivo é **executado diretamente** (`python geometria.py`), `__name__` vale `"__main__"`.
- Quando é **importado**, `__name__` vale o **nome do módulo** (`"geometria"`).

Isso permite colocar testes ou um programa de demonstração no módulo **sem que sejam executados na importação**:

```python
# geometria.py
import math

def area_circulo(raio):
    return math.pi * raio ** 2

def main():
    print("Testando o módulo...")
    print(area_circulo(1))      # 3.14159...

if __name__ == "__main__":
    main()
```

```bash
python geometria.py      # executa main()
```

```python
import geometria         # NÃO executa main()
```

---

## **13. Pacotes**

Um **pacote** é uma **pasta** contendo módulos. Tradicionalmente, ela inclui um arquivo `__init__.py` (que pode estar vazio) indicando que a pasta é um pacote.

```
meu_projeto/
├── main.py
└── calculos/                 <- pacote
    ├── __init__.py
    ├── estatistica.py
    └── geometria.py
```

**Importando de um pacote** (a partir do `main.py`):

```python
import calculos.estatistica
print(calculos.estatistica.media([1, 2, 3]))

from calculos import geometria
print(geometria.area_circulo(2))

from calculos.estatistica import media, desvio_padrao
print(media([4, 6, 8]))
```

### **13.1. O arquivo `__init__.py`**

Executa quando o pacote é importado. Pode ser usado para "expor" funções de forma mais prática:

```python
# calculos/__init__.py
from .estatistica import media, desvio_padrao
from .geometria import area_circulo
```

Agora, no `main.py`:

```python
from calculos import media, area_circulo     # mais direto
```

### **13.2. Importações relativas**

Dentro do pacote, módulos podem se importar usando o ponto:

```python
# calculos/geometria.py
from . import estatistica              # módulo irmão (mesma pasta)
from .estatistica import media         # item de um módulo irmão
from .. import outro_pacote            # pasta acima (dois pontos)
```

> Importações relativas só funcionam **dentro de pacotes**. Execute o programa a partir da **pasta raiz do projeto** (`python main.py`).

---

## **14. Exemplo Completo: Projeto Modularizado**

**Estrutura:**

```
analise_notas/
├── main.py
├── notas.csv
└── analise/
    ├── __init__.py
    ├── arquivos.py
    └── estatistica.py
```

**`analise/estatistica.py`:**

```python
"""Funções estatísticas básicas."""

import math

def media(valores):
    return sum(valores) / len(valores)

def desvio_padrao(valores):
    m = media(valores)
    return math.sqrt(sum((v - m) ** 2 for v in valores) / (len(valores) - 1))

def maior_e_menor(valores):
    return max(valores), min(valores)

if __name__ == "__main__":
    # Pequenos testes ao executar o módulo diretamente
    assert media([2, 4, 6]) == 4
    print("estatistica.py: testes OK")
```

**`analise/arquivos.py`:**

```python
"""Funções de leitura e escrita de dados."""

import csv

def ler_notas(caminho):
    """Lê um CSV (nome,nota) e retorna uma lista de dicionários."""
    registros = []
    with open(caminho, newline="", encoding="utf-8") as f:
        for linha in csv.DictReader(f):
            registros.append({"nome": linha["nome"], "nota": float(linha["nota"])})
    return registros

def salvar_relatorio(caminho, texto):
    with open(caminho, "w", encoding="utf-8") as f:
        f.write(texto)
```

**`analise/__init__.py`:**

```python
from .arquivos import ler_notas, salvar_relatorio
from .estatistica import media, desvio_padrao, maior_e_menor
```

**`main.py`:**

```python
from analise import ler_notas, salvar_relatorio, media, desvio_padrao

def main():
    registros = ler_notas("notas.csv")
    notas = [r["nota"] for r in registros]

    texto = (
        f"Alunos: {len(notas)}\n"
        f"Média: {media(notas):.2f}\n"
        f"Desvio padrão: {desvio_padrao(notas):.2f}\n"
    )

    print(texto)
    salvar_relatorio("relatorio.txt", texto)

if __name__ == "__main__":
    main()
```

Cada parte tem **uma responsabilidade**: `arquivos` cuida de entrada e saída, `estatistica` cuida dos cálculos e `main` apenas coordena.

---

## **15. Instalando Dependências e Ambientes (Revisão)**

Projetos reais dependem de bibliotecas externas. Registre-as em um arquivo `requirements.txt`:

```
numpy==1.26.4
matplotlib==3.8.4
sympy==1.12
```

```bash
pip install -r requirements.txt       # instala tudo de uma vez
pip freeze > requirements.txt         # gera o arquivo a partir do ambiente atual
```

Use um **ambiente virtual** (`python -m venv .venv`) para cada projeto, como visto em *Boas Práticas*.

---

## **16. Boas Práticas**

**Com arquivos:**

- Use **sempre** `with open(...)`.
- Informe `encoding="utf-8"`.
- Cuidado com o modo `"w"`: ele apaga o conteúdo. Use `"a"` ou `"x"` quando apropriado.
- Trate `FileNotFoundError` e `ValueError` ao ler dados externos.
- Use `pathlib` para montar caminhos (`Path("pasta") / "arquivo"`).
- **Não** carregue arquivos enormes inteiros com `read()`; percorra linha a linha.
- Para dados tabulares use `csv`; para dados estruturados, `json`.
- Nunca use `pickle.load` em arquivos não confiáveis.

**Com módulos:**

- Um módulo = **uma responsabilidade**.
- Coloque os `import` no topo do arquivo e evite `import *`.
- Use nomes `snake_case`, curtos e sem conflito com a biblioteca padrão.
- Proteja o código executável com `if __name__ == "__main__":`.
- Documente cada módulo com uma *docstring* no início.
- Evite importações circulares.
- Prefira um arquivo `main.py` enxuto, que apenas coordena os módulos.

---

## **17. Erros Comuns**

| Erro                                                      | Explicação / Correção                                                       |
| --------------------------------------------------------- | --------------------------------------------------------------------------- |
| `FileNotFoundError`                                       | Caminho errado ou programa executado em outra pasta; confira `os.getcwd()`  |
| Abrir com `"w"` um arquivo que deveria ser preservado     | O conteúdo é apagado; use `"a"`                                             |
| Esquecer `.close()` (sem `with`)                          | Dados podem não ser gravados; use `with`                                    |
| `f.write(42)`                                             | `TypeError`; use `f.write(str(42))`                                         |
| Esquecer o `\n` no `write()`                              | Tudo sai em uma única linha                                                 |
| Esquecer o `strip()` ao ler                               | A linha vem com `\n` e atrapalha `float()`/comparações                      |
| Ler o arquivo duas vezes com `read()`                     | A 2ª leitura retorna `""` (cursor no fim); use `seek(0)`                    |
| Acentos "estranhos"                                       | Falta `encoding="utf-8"`                                                    |
| `csv` sem `newline=""` no Windows                         | Linhas em branco entre os registros                                         |
| Valores do CSV usados como número                         | Tudo vem como `str`; converta                                               |
| Arquivo com nome de módulo padrão (`json.py`)             | Quebra os `import`; renomeie                                                |
| `ModuleNotFoundError` em pacote                           | Falta executar da pasta raiz ou instalar com `pip`                          |

---

## **Resumo**

**Arquivos:**

- Use `with open(caminho, modo, encoding="utf-8") as f:` para fechar o arquivo automaticamente.
- Modos: `"r"` (ler), `"w"` (escrever, **apaga**), `"a"` (acrescentar), `"x"` (criar exclusivo) e `"b"` (binário).
- Leitura: `read()`, `readline()`, `readlines()` ou, preferencialmente, `for linha in f`.
- Escrita: `write()`, `writelines()` e `print(..., file=f)`; o `\n` é por sua conta.
- Trate erros com `try/except` (`FileNotFoundError`, `PermissionError`, `ValueError`).
- `pathlib.Path` manipula caminhos e pastas de forma portátil.
- **CSV** (módulo `csv`) para tabelas; **JSON** (módulo `json`) para dados estruturados.

**Modularização:**

- Todo arquivo `.py` é um **módulo**; uma pasta com `__init__.py` é um **pacote**.
- Importe com `import`, `from ... import ...` e `as`; evite `import *`.
- `if __name__ == "__main__":` separa o que é **programa** do que é **biblioteca**.
- O Python procura módulos na pasta do script e em `sys.path`.
- Divida o projeto por responsabilidades, evite importações circulares e nomes que conflitem com a biblioteca padrão.
- Registre dependências em `requirements.txt` e use ambientes virtuais.

---

## **Referências**

- [Tutorial oficial: Entrada e Saída (arquivos)](https://docs.python.org/pt-br/3/tutorial/inputoutput.html)
- [Documentação: módulo `pathlib`](https://docs.python.org/pt-br/3/library/pathlib.html)
- [Documentação: módulo `csv`](https://docs.python.org/pt-br/3/library/csv.html)
- [Documentação: módulo `json`](https://docs.python.org/pt-br/3/library/json.html)
- [Tutorial oficial: Módulos e Pacotes](https://docs.python.org/pt-br/3/tutorial/modules.html)