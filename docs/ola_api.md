# 👋 Olá API

![](imgs/codigo.jpg)

## ✍️ Escrevendo código

É chegada a tão esperada hora de escrevermos código, porém, como aprendemos que podemos ser guiados por testes para ajudar a concepção da arquitetura do nosso programa, faremos as coisas um pouco diferente.

Utilizaremos os ciclos do [TDD](tdd.md) para nos auxiliarem e assim garantiremos uma qualidade de código ao final.

Estão lembrados o que é a nossa aplicação? Caso não se recorde leia o nosso [planejamento](planejando.md) novamente.

Acho que podemos iniciar com os dois requisitos listados abaixo:

- [ ] Deve apresentar uma interface que possa ser consumida tanto por um website, tanto por um aplicativo para dispositivos móveis

- [ ] Deve prover um _endpoint_ que indique a saúde do sistema

Mas como fazer isto se não temos nem mesmo uma aplicação ainda? Por onde começo?

Inicie criando um diretório com o nome `tests`, onde colocaremos os testes do nosso programa.

Lá dentro, crie um arquivo com nome `test_api.py` que deve ficar da seguinte maneira:

```
.
├── LICENSE
├── poetry.lock
├── pyproject.toml
├── README.md
└── tests
    └── test_api.py
```

Agora vamos escrever nosso primeiro teste!

!!! info
    O código está escrito em português para ajudar na didática.

Para indicar a integridade do nosso sistema, vamos ter um _endpoint_ `/healthcheck`, que ao receber um requisição, deve retornar o código de status `200 OK` e este será nosso primeiro teste.

Traduzindo em um teste automatizado, vamos escrever o seguinte código no arquivo `tests/test_api.py`.

> tests/test_api.py
```python
from fastapi.testclient import TestClient
from http import HTTPStatus


def test_quando_verificar_integridade_devo_ter_como_retorno_codigo_de_status_200():
    cliente = TestClient(app)
    resposta = cliente.get("/healthcheck")
    assert resposta.status_code == HTTPStatus.OK
```


Note a utilização do prefixo `test`  nos diretórios e arquivos de testes. Isto é necessário para que a ferramenta de testes do Python consiga identificar os testes e executá-los.

Vamos rodar pela primeira vez os testes no nosso projeto.

Para não precisar digitar a todo momento os comandos em sua forma extensa `poetry run <comando>` vamos ativar nosso ambiente virtual com o comando `poetry shell`.

```
python -m pytest tests/
```
😱 Nossa! Ocorreu um erro!

```
$ python -m pytest tests/
======================================================================================= test session starts =======================================================================================
platform linux -- Python 3.9.7, pytest-6.2.5, py-1.10.0, pluggy-1.0.0
rootdir: /home/cassiobotaro/Projects/tutorial-pybr
collected 1 item                                                                                                                                                                                  

tests/test_api.py F                                                                                                                                                                         [100%]

============================================================================================ FAILURES =============================================================================================
__________________________________________________________ test_quando_verificar_integridade_devo_ter_como_retorno_codigo_de_status_200 ___________________________________________________________

    def test_quando_verificar_integridade_devo_ter_como_retorno_codigo_de_status_200():
>       cliente = TestClient(app)
E       NameError: name 'app' is not defined

tests/test_api.py:6: NameError
===================================================================================== short test summary info =====================================================================================
FAILED tests/test_api.py::test_quando_verificar_integridade_devo_ter_como_retorno_codigo_de_status_200 - NameError: name 'app' is not defined
======================================================================================== 1 failed in 0.23s ========================================================================================
```

O teste falha pois ainda não temos a nossa aplicação.

A primeira coisa que precisamos fazer é criar um  diretório onde colocaremos nossos códigos. vamos chama-lo de `api_pedidos`.

Dentro dele criamos um novo arquivo `api_pedidos/api.py`, e neste arquivo vamos iniciar uma aplicação da seguinte maneira.

> api_pedidos/api.py
```python
from fastapi import FastAPI


app = FastAPI()
```

Nosso diretório ficará da seguinte maneira:

```
.
├── api_pedidos
│   └── api.py
├── LICENSE
├── poetry.lock
├── pyproject.toml
├── README.md
└── tests
    └── test_api.py

```
Agora vamos voltar ao arquivo `tests/test_api.py` e adicionamos a seguinte linha.

> tests/test_api.py
```python
from api_pedidos.api import app
```

Rode novamente os testes.

```
python -m pytest tests/
```

❌ Os testes continuam falhando!

Agora temos nossa aplicação, mas nosso _endpoint_ ainda não foi criado.

No arquivo `api_pedidos/api.py` vamos criar o endpoint `/healthcheck`:

> api_pedidos/api.py
```python
@app.get("/healthcheck")
async def healthcheck():
    return
```

Rode novamente os testes.

```
python -m pytest
```
✔️ Legal! Temos um teste funcionando! Nossa aplicação está retornando o código de status 200 OK, ainda que a funcionalidade completa não esteja pronta.

👶 Damos o nome de `baby step`, esta maneira de construir uma aplicação dando pequenos passos de cada vez.

Nosso recurso deve ter o formato [json](http://json.org/), que é um formato textual estruturado, bem simples e leve para troca de informações.

Mas como checamos isto?

Vamos escrever um novo teste!

> tests/test_api.py
```python
def test_quando_verificar_integridade_formato_de_retorno_deve_ser_json():
    cliente = TestClient(app)
    resposta = cliente.get("/healthcheck")
    assert resposta.headers["Content-Type"] == "application/json"
```

Rode os testes novamente. Caso esqueça o comando, volte um pouco atrás e copie.

👀 O novo teste está passando ?!?!

Acontece que por padrão, o fastapi já define que o formato será "json".

Normalmente, queremos que testes falhem, porém este teste pode ser útil como documentação do seu recurso.

Vamos deixa-lo e vamos seguir em frente, mas agora tentando escrever um teste que realmente falhe.

Quando verificar integridade o retorno deve possuir o seguinte formato:
```json
{
    "status": "ok"
}
```

Assim se precisarmos adicionar mais detalhes neste _endpoint_ podemos adicionar novas chaves a esta estrutura.
Estes detalhes podem envolver o estado da conexão com o banco de dados.

> tests/test_api.py
```python
def test_quando_verificar_integridade_deve_conter_informacoes():
    cliente = TestClient(app)
    resposta = cliente.get("/healthcheck")
    assert resposta.json() == {
        "status": "ok"
    }
```

Rode novamente os testes.

❌ O teste falha e isto é bom!

Vamos continuar nosso ciclo e corrigir o código.

> api_pedidos/api.py
```python
@app.get("/healthcheck")
async def healthcheck():
    return {"status": "ok"}
```

✔️ Aew! Testes estão passando novamente!

🚦 Perceberam que estamos guiando o nosso desenvolvimento a partir dos testes? Pouco a pouco temos a funcionalidade de listagem sendo desenhada.

Neste passo os arquivos devem estar da seguinte maneira:

> api_pedidos/api.py
```python
from fastapi import FastAPI


app = FastAPI()


@app.get("/healthcheck")
async def healthcheck():
    return {"status": "ok"}
```

> tests/test_api.py
```python
from fastapi.testclient import TestClient
from http import HTTPStatus
from api_pedidos.api import app


def test_quando_verificar_integridade_devo_ter_como_retorno_codigo_de_status_200():
    cliente = TestClient(app)
    resposta = cliente.get("/healthcheck")
    assert resposta.status_code == HTTPStatus.OK


def test_quando_verificar_integridade_formato_de_retorno_deve_ser_json():
    cliente = TestClient(app)
    resposta = cliente.get("/healthcheck")
    assert resposta.headers["Content-Type"] == "application/json"


def test_quando_verificar_integridade_deve_conter_informacoes():
    cliente = TestClient(app)
    resposta = cliente.get("/healthcheck")
    assert resposta.json() == {
        "status": "ok",
    }
```

Os testes estão funcionando? Parabéns! 👏👏 👏 Agora vamos refatorar o código.

## 🧙 Refatorando o código

Se reparar bem, estamos repetindo a seguinte linha de código três vezes.

```
client = TestClient(app)
```

Vamos utilizar uma _fixture_ (é um ambiente usado para testar consistentemente algum item) para criar um cliente.

> tests/test_api.py
```python
from http import HTTPStatus

import pytest
from api_pedidos.api import app
from fastapi.testclient import TestClient


@pytest.fixture
def cliente():
    return TestClient(app)


def test_quando_verificar_integridade_devo_ter_como_retorno_codigo_de_status_200(cliente):
    resposta = cliente.get("/healthcheck")
    assert resposta.status_code == HTTPStatus.OK


def test_quando_verificar_integridade_formato_de_retorno_deve_ser_json(cliente):
    resposta = cliente.get("/healthcheck")
    assert resposta.headers["Content-Type"] == "application/json"


def test_quando_verificar_integridade_deve_conter_informacoes(cliente):
    resposta = cliente.get("/healthcheck")
    assert resposta.json() == {
        "status": "ok",
    }
```

Após esta mudança, os testes devem continuar passando.

## 🔧 Testando manualmente

Para testar nossa aplicação manualmente, precisamos colocar nossa aplicação no ar.

O comando para isto é `uvicorn --reload api_pedidos.api:app`.

Voilá, sua aplicação está no ar.  Vamos utilizar a ferramenta `httpie` para testar a aplicação.

Execute o seguinte comando:
```
http :8000/healthcheck
```

![implementação do healthcheck](imgs/healthcheck.png "implementação do healthcheck")

!!! info
    Como adicionamos a opção `--reload`, cada vez que modificamos o código, o resultado é modificado também, sem precisar desligar e rodar de novo a aplicação.

## 💾 Salvando a versão atual do código

Com tudo terminado, vamos salvar a versão atual do código.

Primeiro passo é checar o que foi feito até agora:

```bash
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        api_pedidos/
        tests/

nothing added to commit but untracked files present (use "git add" to track)
```

Vemos dois diretórios não rastreados e precisamos avisar ao controle de versão para monitora-los.

```
git add api_pedidos tests 
```

💾 Agora vamos marcar esta versão como salva.

```
git commit -m "Adicionando healthcheck"
```

🐱 Por fim envie ao github a versão atualizada do projeto.

```
git push
```

Parabéns! A aplicação está tomando forma! 🎉

Podemos marcar como pronto as seguintes tarefas:

- [x] Deve apresentar uma interface que possa ser consumida tanto por um website, tanto por um aplicativo para dispositivos móveis

- [x] Deve prover um _endpoint_ que indique a saúde do sistema

- [x] O sistema deve apresentar testes (Acabamos cumprindo uma tarefa a mais!)

!!! quote "🐂" 
        Uma API robusta provê maneiras de verificar sua integridade.

