<div align="center">

#  IoT Production Manager

**Gerenciamento de produção com dispositivos IoT, em uma aplicação Django monolítica.**


</div>

---


## Sobre

O **IoT Production Manager** é um sistema web para acompanhar e gerenciar a produção a partir de dispositivos IoT.

A arquitetura é **monolítica**, com páginas geradas no servidor (Django Templates). Não há Docker: cada pessoa instala o ambiente direto na própria máquina, para entender como o ambiente de um projeto Python é organizado.

## Stack

| Camada            | Tecnologia                                   |
|-------------------|----------------------------------------------|
| Linguagem         | Python 3.10+                                 |
| Framework         | Django 5.2 (LTS)                             |
| Renderização      | Django Templates (server-side)               |
| Banco de dados    | SQLite (padrão) → PostgreSQL (próxima etapa) |
| Ambiente          | `venv` + `pip`                               |

## Estrutura do repositório

```text
.
├── README.md              # apresentação do projeto
├── .gitignore             # ignora venv, __pycache__, banco local e .env
└── backend/               # código do sistema
    ├── manage.py          # utilitário de linha de comando do Django
    ├── requirements.txt   # dependências Python
    ├── config/            # configuração do projeto (settings, urls, wsgi, asgi)
    └── core/              # app inicial
        ├── views.py       # view da página inicial
        ├── urls.py
        ├── tests.py
        └── templates/core/home.html
```

## Primeiros passos

### Pré-requisitos

- [Python 3.10+](https://www.python.org/downloads/) (`python --version`)
- [Git](https://git-scm.com/)

### 1. Clonar o repositório

Clone o repositório pelo botão **Code** do GitHub (HTTPS ou SSH) e entre na pasta do sistema:

```bash
git clone <endereço copiado no botão Code>
cd backend
```

(Se o clone criou uma pasta com o nome do repositório, entre nela antes: `cd` até ela e depois `cd backend`.)

### 2. Criar e ativar o ambiente virtual

<details open>
<summary><b>Linux / macOS</b></summary>

```bash
python3 -m venv venv
source venv/bin/activate
```
</details>

<details>
<summary><b>Windows (PowerShell)</b></summary>

```powershell
python -m venv venv
venv\Scripts\Activate.ps1
```
</details>

<details>
<summary><b>Windows (cmd)</b></summary>

```cmd
python -m venv venv
venv\Scripts\activate.bat
```
</details>

Com o ambiente ativo, o terminal mostra `(venv)` no início da linha.

### 3. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 4. Subir o servidor

```bash
python manage.py runserver
```

Acesse **http://127.0.0.1:8000/**. A página inicial mostra o nome *IoT Production Manager*.

Para parar o servidor, use `Ctrl + C`.

## Comandos úteis

Todos devem ser executados dentro de `backend/`, com o ambiente virtual ativo.

| Comando                              | O que faz                                      |
|--------------------------------------|------------------------------------------------|
| `python manage.py runserver`         | Sobe o servidor de desenvolvimento             |
| `python manage.py test`              | Roda os testes automatizados                   |
| `python manage.py check`             | Verifica problemas na configuração do projeto  |
| `pip install -r requirements.txt`    | Instala as dependências                        |
| `deactivate`                         | Desativa o ambiente virtual                    |

## Configuração

Os valores sensíveis vêm de variáveis de ambiente. Em desenvolvimento local, os padrões já funcionam e **nada precisa ser definido**.

| Variável     | Descrição                              | Padrão                    |
|--------------|----------------------------------------|---------------------------|
| `SECRET_KEY` | Chave secreta do Django                | chave de desenvolvimento  |
| `DEBUG`      | Modo de depuração (`True` ou `False`)  | `True`                    |

> ⚠️ A chave padrão serve **apenas** para desenvolvimento. Nunca use-a em produção.

Arquivos locais não são versionados: `venv/`, `__pycache__/`, `db.sqlite3` e `.env`. Se `git status` mostrar algum deles, o `.gitignore` não está sendo aplicado.

## Solução de problemas

<details>
<summary><b>"No module named django"</b></summary>

O ambiente virtual não está ativo, ou as dependências não foram instaladas. Ative o `venv` e rode `pip install -r requirements.txt`.
</details>

<details>
<summary><b>PowerShell bloqueia a ativação do venv (Windows)</b></summary>

Execute uma vez: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` e tente ativar de novo. Ou use o `cmd` com `venv\Scripts\activate.bat`.
</details>

<details>
<summary><b>"Port is already in use"</b></summary>

Outra aplicação está usando a porta 8000. Use outra porta: `python manage.py runserver 8001`.
</details>

<details>
<summary><b>"You have unapplied migrations"</b></summary>

Aviso normal nesta etapa: o Django lista as migrações do admin e da autenticação que ainda não foram aplicadas ao banco local. Não impede a página inicial de funcionar.
</details>

## Roadmap

- [x] Base do projeto Django (`backend/`, app `core`, página inicial)
- [ ] Migrar o banco para PostgreSQL
- [ ] Modelagem do banco de dados
- [ ] Demais funcionalidades, definidas nas próximas issues

## Como contribuir

1. Escolha uma issue aberta (ou abra uma nova descrevendo o que será feito).
2. Crie uma branch a partir da principal: `git checkout -b feat/nome-curto`.
3. Faça as alterações e rode `python manage.py test` antes de enviar.
4. Abra um Pull Request referenciando a issue.

Não commite o ambiente virtual, arquivos `.env` nem o banco local.
