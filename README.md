# IoT Production Manager

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Em%20defini%C3%A7%C3%A3o-orange.svg)]()

Sistema de gestão de montagem e teste de placas PCB, voltado para a supervisora
de uma equipe de produção. Reúne num só lugar o que hoje se espalha entre
planilhas e memória: quais produtos cada cliente tem, como cada placa deve ser
montada, quantas unidades estão em produção em cada lote e quem fez o quê em
cada uma delas.

> 🚧 **Projeto em fase inicial de definição.** As seções vazias abaixo serão
> preenchidas conforme as decisões técnicas forem tomadas.

---

## 📋 Sobre o Projeto

Numa bancada de montagem de placas PCB, a informação que mais importa é também a
que menos sobrevive: quem soldou o quê, em qual placa, de qual lote. Uma placa
raramente é montada por uma pessoa só — cada uma coloca componentes diferentes.
Quando um defeito aparece semanas depois, saber apenas "quem montou a placa" não
ajuda: é preciso saber quem montou **aquela parte**.

O mesmo vale para o conhecimento de montagem. A forma certa de montar cada placa
costuma existir apenas na cabeça de quem já montou antes. Quem entra na equipe
depois aprende olhando, e o que se perde nesse caminho vira retrabalho.

Este sistema ataca os dois problemas: registra a **receita** de cada placa — os
componentes e a melhor forma de montá-la — e registra o **histórico** de cada
unidade produzida, com granularidade suficiente para saber quem fez cada parte.

Desenvolvido como Projeto Integrador.

---

## ✨ Funcionalidades

O sistema é organizado em cinco blocos, construídos nesta ordem:

| # | Bloco | Descrição | Estado |
| :---: | :--- | :--- | :---: |
| 1 | **Clientes e Catálogo** | Cadastro de clientes e dos produtos de cada cliente | 🗺️ |
| 2 | **Receita de Montagem** | Componentes, quantidades, foto e o procedimento de montagem de cada placa | 🗺️ |
| 3 | **Lotes e Produção** | Abertura de lotes, número de unidades e acompanhamento do andamento | 🗺️ |
| 4 | **Apontamentos** | Registro de quem montou e quem testou cada parte de cada unidade | 🗺️ |
| 5 | **Consulta por IA** | Perguntas em linguagem natural sobre os dados do sistema | 💭 |

**Legenda:** ✅ pronto · ⏳ em andamento · 🗺️ planejado · 💭 em avaliação

O bloco 4 é o núcleo do projeto: é ele que resolve o problema de rastrear
trabalho fragmentado entre várias pessoas na mesma placa. Os blocos 1 a 3
existem para sustentá-lo.

---

## 🏗️ Arquitetura

Os conceitos do domínio, a hierarquia de dados, o fluxo de navegação e as
decisões ainda em aberto estão documentados em [`arquitetura.md`](arquitetura.md).

O diagrama de fluxo inicial está em [`P.I.drawio`](P.I.drawio) e pode ser aberto
em [app.diagrams.net](https://app.diagrams.net).

---

## 🛠️ Tecnologias

---

## 📁 Estrutura de Pastas

---

## 🚀 Como Executar

---

## 👥 Equipe

---

## 📄 Licença

Distribuído sob a licença MIT. Veja [`LICENSE`](LICENSE) para o texto completo.
