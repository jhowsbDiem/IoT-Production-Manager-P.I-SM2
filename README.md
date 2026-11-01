# IoT Production Manager — montagem e teste de placas PCB 🏭

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-0.1.0-brightgreen.svg)](https://github.com/jhowsbDiem/IoT-Production-Manager-P.I-SM2)

O IoT Production Manager é um sistema web de gestão de montagem e teste de
placas PCB, voltado para a supervisora de uma equipe de produção. Reúne num só
lugar o que hoje se espalha entre planilhas e memória: quais produtos cada
cliente tem, como cada placa deve ser montada, quantas unidades estão em
produção em cada lote e quem fez cada parte de cada unidade. Desenvolvido como
Projeto Integrador.

---

## 🤝 Contribuindo

O fluxo de trabalho e os padrões de commit e de comentários estão em
[`CONTRIBUTING.md`](CONTRIBUTING.md).

---

## 📊 Estado Atual do Projeto

### ⏳ Em Andamento

* **Modelagem do domínio:** os conceitos, a hierarquia de dados e as decisões
  em aberto estão em [`arquitetura.md`](arquitetura.md). O diagrama de fluxo
  está em [`P.I.drawio`](P.I.drawio) e abre em
  [app.diagrams.net](https://app.diagrams.net).
* **Esqueleto do sistema:** o projeto Django com PostgreSQL sobre o qual os
  blocos são construídos.

### 🗺️ Próximos Passos Planejados

O sistema se organiza em cinco blocos, construídos nesta ordem:

| # | Bloco | Descrição |
| :---: | :--- | :--- |
| 1 | **Clientes e Catálogo** | Cadastro de clientes e dos produtos de cada cliente |
| 2 | **Receita de Montagem** | Componentes, quantidades, foto e o procedimento de montagem de cada placa |
| 3 | **Lotes e Produção** | Abertura de lotes, número de unidades e acompanhamento do andamento |
| 4 | **Apontamentos** | Registro de quem montou e quem testou cada parte de cada unidade |
| 5 | **Consulta por IA** | Perguntas em linguagem natural sobre os dados do sistema (em avaliação) |

O bloco 4 é o núcleo do projeto: é ele que resolve o problema de rastrear
trabalho fragmentado entre várias pessoas na mesma placa. Os blocos 1 a 3
existem para sustentá-lo.

---

## ✒️ Autores e Licença

* **Desenvolvedor:** Jonathan dos Santos Bezerra.
* **Licença:** MIT — veja [LICENSE](LICENSE).
