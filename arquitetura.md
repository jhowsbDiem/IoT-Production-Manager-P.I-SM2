# Arquitetura Inicial — IoT Production Manager

> **Para quem é este documento:** o grupo de desenvolvimento.
> **Para que serve:** alinhar o entendimento do problema e registrar o que
> ainda precisa ser decidido, antes de escrever código.
> **Estado:** rascunho de trabalho. Nenhuma decisão técnica foi fechada.

---

## 1. Problema e Contexto

A supervisora de uma equipe de montagem de placas PCB precisa responder,
a qualquer momento, três perguntas:

1. **Quanto falta?** — quantas unidades de um lote ainda não ficaram prontas.
2. **Quem fez?** — quem montou e quem testou cada unidade produzida.
3. **Como se faz?** — qual é a forma correta de montar cada placa.

Hoje essas respostas vivem em planilhas e na memória das pessoas. Isso cria
dois problemas distintos:

**Rastreabilidade quebrada.** Uma placa raramente é montada por uma pessoa só.
Cada uma coloca componentes diferentes na mesma placa. Quando um defeito
aparece semanas depois, saber "quem montou a placa" não resolve — é preciso
saber quem montou **aquela parte**. Uma planilha com uma coluna "montador" não
consegue registrar isso.

**Conhecimento que não se transfere.** A melhor forma de montar cada placa
costuma existir só na cabeça de quem já montou antes. Quem entra depois aprende
observando, e o que se perde no caminho vira retrabalho.

O sistema ataca os dois: guarda a **receita** de cada placa e o **histórico**
de cada unidade produzida, com granularidade suficiente para saber quem fez
cada parte.

---

## 2. Conceitos do Domínio

Estes são os substantivos do projeto. Vale o grupo concordar com eles antes de
qualquer código, porque viram tabelas, telas e URLs depois.

| Conceito | Definição | Exemplo |
| :--- | :--- | :--- |
| **Cliente** | A empresa para quem a equipe produz | Empresa A |
| **Produto** | Um item que um cliente encomenda | "Sensor-X v2" |
| **Placa** | Uma PCB que compõe um produto | Placa principal, placa de alimentação |
| **Receita** | Como montar uma placa: componentes, quantidades, foto e procedimento | — |
| **Lote** | Uma ordem de produção: N unidades de um produto, numa data | Lote "09/09/26", 50 unidades |
| **Unidade** | Uma peça física específica dentro de um lote | A 7ª unidade do lote |
| **Apontamento** | O registro de que uma pessoa fez algo numa unidade | "João soldou os capacitores" |
| **Supervisora** | Quem administra o sistema | — |
| **Funcionário** | Quem monta ou testa, e aparece nos apontamentos | — |

### A distinção que sustenta tudo

**Receita ≠ Unidade.** É o ponto mais importante deste documento.

```
RECEITA                              UNIDADE
(a instrução)                        (a peça física)

"Sensor-X v2, do Cliente A"          "a 7ª placa do lote 09/09/26"
 lista de componentes                 está em montagem
 procedimento de montagem             o João soldou os capacitores
 foto de referência                   a Ana ainda não testou
 escrita uma vez, lida sempre          nasce e morre com o lote
 muda raramente                       tem histórico próprio
```

Confundir as duas é o erro que mais custa caro: a lista de componentes acabaria
copiada dentro de cada unidade produzida. Cinquenta unidades num lote virariam
cinquenta cópias da mesma lista. Corrigir um componente errado exigiria corrigir
cinquenta registros — e qualquer um esquecido vira uma inconsistência silenciosa.

---

## 3. A Hierarquia

```
Cliente
  └── Produto                    ex.: "Sensor-X v2"
        ├── Placa(s)             um produto pode ter mais de uma PCB
        │     └── Receita        componentes + procedimento
        └── Lote(s)              ordens de produção, por data
              └── Unidade(s)     as peças físicas
                    └── Apontamento(s)   quem fez o quê
```

**Por que o nível "Placa" existe separado de "Produto":** porque um produto pode
ser composto por mais de uma PCB. Sem esse nível, não há onde pendurar o
apontamento quando duas pessoas trabalham em placas diferentes do mesmo produto.

**Por que "Apontamento" é um registro próprio, e não campos na Unidade:** porque
uma unidade acumula **vários** apontamentos. Se fossem campos (`montador`,
`testador`), só caberia uma pessoa em cada papel — exatamente a limitação que
motivou o projeto.

---

## 4. Fluxo de Navegação

Baseado no diagrama em [`P.I.drawio`](P.I.drawio):

```
[ Login ]  supervisora, com ID e senha
    ↓
[ Clientes ]  lista de clientes
    ↓  clica em um cliente
[ Produtos ]  os produtos daquele cliente
    ↓  escolhe um produto
[ Detalhes do Produto ]
    ├── Detalhes técnicos: componentes, quantidades, foto, montagem
    └── Lotes do produto, listados por data  +  [ Adicionar lote ]
```

**Adicionar lote** acontece na própria tela de detalhes, expandindo campos de
"nome do lote" e "data do lote" — sem navegar para outra página.

A tela de lotes precisa ser simples e bem descritiva, otimizada para consulta
rápida durante a produção.

---

## 5. Blocos do Sistema

O sistema é construído em cinco blocos, nesta ordem. Cada um entrega algo que
funciona sozinho.

| # | Bloco | Entrega | Depende de |
| :---: | :--- | :--- | :---: |
| 1 | **Clientes e Catálogo** | Cadastrar clientes e seus produtos; navegar entre eles | — |
| 2 | **Receita de Montagem** | Componentes, quantidades, foto e procedimento de cada placa | 1 |
| 3 | **Lotes e Produção** | Abrir lotes, definir N unidades, ver o andamento | 1 |
| 4 | **Apontamentos** | Registrar quem montou e quem testou cada parte | 3 |
| 5 | **Consulta por IA** | Perguntas em linguagem natural sobre os dados | 1–4 |

**A ordem não é arbitrária.** O bloco 4 é o núcleo do projeto — é ele que
resolve o problema de rastrear trabalho fragmentado. Mas ele não tem onde
existir sem lotes e unidades (bloco 3), que por sua vez não existem sem produtos
(bloco 1). O bloco 5 só faz sentido no fim: uma IA não tem o que responder sobre
um banco vazio.

**Cronograma:** Pode ser adaptado para se encaixar em diferentes semestres.
---

## 6. Decisões em Aberto

Esta é a pauta da próxima reunião do grupo, e deve ser discutico também com o cliente, entra no requisitos funcionais e não funcionais.

### 6.1 Quem escreve a receita de montagem?

**A escolha:** o procedimento de montagem é escrito pelo grupo direto no código
e no banco, ou o cliente escreve pelo próprio sistema?

**Por que importa:** muda a estrutura do banco de dados e o escopo do projeto.

- **Escrita pelo grupo** — a receita é conteúdo fixo. Sem tela de edição, sem
  login de cliente, sem controle de permissão. Menos trabalho, menos flexível.
- **Escrita pelo cliente** — exige tela de edição, autenticação de cliente,
  controle de quem pode alterar o quê, e provavelmente histórico de versões da
  receita. É um subsistema inteiro a mais.

**Sem decisão, o bloco 2 não pode começar.**

### 6.2 A receita fica no Produto ou na Placa?

**A escolha:** o diagrama mostra os detalhes técnicos na tela do Produto. Mas se
um produto tem várias placas, cada uma tem a sua própria lista de componentes e
o seu próprio procedimento.

**Por que importa:** determina se o nível "Placa" da hierarquia existe de fato
no banco ou é só uma forma de falar. Se a receita for por produto, um produto de
três placas terá as três listas misturadas num campo só.

### 6.3 Como o apontamento se liga à placa certa?

**A escolha:** quando um lote envolve várias placas do mesmo produto, o
apontamento precisa dizer **em qual placa** a pessoa trabalhou — e em qual
unidade daquele lote.

**Por que importa:** é o coração do projeto. A anotação original do diagrama já
identifica o problema: *"é necessário ter tópicos para atribuir cada funcionário
a uma placa específica do conjunto do produto"*.

**Hipótese de trabalho:** uma página única, descritiva e interativa, listando as
unidades do lote e permitindo adicionar apontamentos em cada uma. Precisa ser
validada com um desenho de tela antes de virar código.

### 6.4 Quem entra no sistema?

**A escolha:** o diagrama prevê login só da supervisora, com "login do dono"
como possibilidade futura. Mas os funcionários aparecem nos apontamentos.

**Definido assim porque:** A ideia atual é para facilitar a gestão da supervisora,
e ter como possibilidade a observação do dono de como anda a produção geral.
Adicionar funcionarios com login no sistema só faria sentido para testes assim os
técnicos deixariam registrados os erros ao invés da gestora administrar isto também.
Interessante verificar com cliente.

### 6.5 Qual é a stack?

**A escolha:** linguagem, framework e banco de dados. Nada foi definido.

**Por que importa:** trava as seções "Tecnologias", "Estrutura de Pastas" e
"Como Executar" do [README](README.md), e impede qualquer estimativa de prazo.

**Critério sugerido:** Como na faculdade ainda não houve aula direta sobre stack,
tirando banco de dados, podemos partir do banco e usar somente demonstrações
do funcionamento do código sem o código.

---

## 7. Fora de Escopo por hora

Registrado para não ser esquecido — e para não ser prometido antes da hora.

### IoT de verdade

O nome do projeto vem do fato de que as placas produzidas são de dispositivos
IoT. Não há, hoje, nenhum componente conectado no sistema. A possibilidade de
adicionar IoT de fato — uma bancada de teste que envia o resultado
automaticamente, por exemplo — fica em aberto para avaliação depois que a base
estiver de pé.

### Consulta por IA (bloco 5)

A ideia é a supervisora perguntar em português — *"quem testou tal coisa nessa
placa?"*, *"quantas unidades faltam nesse lote?"* — e receber a resposta pronta,
sem navegar por telas.

Depende de uma IA gratuita a definir, e só faz sentido com os blocos 1–4
funcionando e com dados reais no banco. Tratado como possibilidade, não como
compromisso.
