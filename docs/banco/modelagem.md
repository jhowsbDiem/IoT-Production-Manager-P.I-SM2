# Modelagem do Banco de Dados — IoT Production Manager

Projeto conceitual do banco de dados do IoT Production Manager: o negócio
que ele atende, as entidades, os atributos, os relacionamentos e as regras
que valem sobre eles.

---

## 1. Mini mundo

Uma equipe de produção monta e testa placas de circuito impresso (PCB) de
dispositivos IoT, encomendadas por empresas clientes. A equipe é administrada
por uma supervisora, que precisa acompanhar a produção sem depender de
planilhas nem da memória das pessoas.

Cada **cliente** encomenda um ou mais **produtos**. Um produto é composto por
uma ou mais **placas**: um sensor, por exemplo, pode ter uma placa principal e
uma placa de alimentação. Cada placa tem a sua receita de montagem, escrita e
mantida pela supervisora. A receita reúne a foto de referência da placa, os
**componentes** que ela leva, cada um registrado como um **item da receita**,
com a quantidade e a posição na placa (por exemplo, "C1 a C12"), e os
**procedimentos** de montagem. Os componentes formam um catálogo da equipe: o
mesmo capacitor pode ser usado em várias placas.

Uma placa pode ser montada de mais de um jeito, e por isso tem um ou mais
procedimentos: o padrão e as variações que a supervisora cria, como "com
verniz" ou "retrabalho". Cada procedimento é formado por **etapas**,
numeradas na ordem recomendada de execução. Um procedimento que já foi usado
na produção não é alterado: para mudar o jeito de montar, a supervisora cria
um procedimento novo, e o histórico continua apontando para o antigo.

A produção acontece em **lotes**. Um lote é uma ordem de produção de um
produto, aberta numa data, com um nome escolhido pela supervisora e um número
de **unidades**. Ao abrir o lote, a supervisora define qual procedimento cada
placa do produto vai seguir, o padrão ou uma variação. Essa escolha fica
registrada como o **procedimento do lote**, e assim dois lotes do mesmo
produto podem seguir procedimentos diferentes ao mesmo tempo. Cada unidade é
uma peça física do produto, numerada dentro do lote, e é montada aos poucos,
por várias pessoas.

O trabalho feito em cada unidade é registrado em **apontamentos**. Um
apontamento diz qual **funcionário** fez qual etapa, de qual placa, em qual
unidade, e quando. O apontamento é de montagem ou de teste. No teste, ele
registra também o resultado, aprovado ou reprovado, e uma observação. Quando
uma unidade é reprovada, ela volta para a bancada, e o retrabalho é
registrado com apontamentos nas etapas de outro procedimento da placa, como o
de retrabalho, até um novo teste aprovado. Assim, quando um defeito aparece
semanas depois, é possível saber quem montou exatamente aquela parte da placa
e quem a corrigiu.

Os funcionários montam e testam, mas não acessam o sistema: quem registra os
apontamentos é a supervisora. O acesso é feito por **usuários**, cada um com
um perfil: a supervisora administra tudo, e o dono só consulta a produção. O
cadastro de funcionários e o de usuários são separados, para que um
funcionário possa ganhar acesso no futuro sem mudar o modelo.

### Objetivos do negócio

O banco de dados existe para a supervisora responder, a qualquer momento:

1. **Quanto falta?** Quantas unidades de um lote ainda não estão prontas, e
   quais etapas do procedimento do lote faltam em cada uma.
2. **Quem fez?** Qual funcionário montou, testou e corrigiu cada etapa de cada
   unidade, e com qual resultado.
3. **Como se faz?** Quais componentes, em que quantidade, e quais etapas de
   cada procedimento compõem a montagem de cada placa.
4. **Quanto material?** Quantos componentes de cada tipo um lote vai
   consumir, segundo a receita das suas placas.

---

## 2. Entidades

Cada entidade guarda um tipo de informação de que o negócio precisa. A tabela
lista as treze, na ordem em que aparecem no mini mundo, com o motivo de cada
uma existir.

| Entidade | Por que existe |
| :--- | :--- |
| **Cliente** | A empresa que encomenda os produtos. Existe para agrupar os produtos por quem os encomenda e responder "o que produzimos para este cliente?". |
| **Produto** | O item que um cliente encomenda, como "Sensor-X v2". É o que os lotes produzem: cada lote é de um produto só. |
| **Placa** | Uma PCB que compõe um produto. Existe separada do Produto porque um produto pode ter mais de uma placa, cada uma com os seus componentes e procedimentos. É na placa que fica a receita de montagem. |
| **Componente** | Um componente eletrônico do catálogo da equipe, como um capacitor de 10 µF. Existe uma vez só, mesmo quando é usado em várias placas, para responder "em quais placas este componente é usado?" e calcular o consumo previsto de um lote. |
| **Item da receita** | Um componente que uma placa leva, com a quantidade e a posição na placa ("C1 a C12"). É uma entidade associativa entre Placa e Componente: a quantidade e a posição não pertencem nem à placa nem ao componente, mas aos dois juntos. |
| **Procedimento** | Um jeito de montar uma placa: o padrão ou uma variação, como "Com botão", "Com sensor de chama" ou "retrabalho". Existe porque uma placa pode ser montada de mais de um jeito, e porque um procedimento já usado não é alterado: mudar o jeito de montar cria um procedimento novo, e o histórico continua apontando para o antigo. |
| **Etapa** | Um passo numerado de um procedimento, como "soldar os capacitores". É a "parte" que o apontamento registra: a mesma etapa ensina quem monta e rastreia quem montou. |
| **Lote** | Uma ordem de produção de um produto, com nome, data e número de unidades. Existe para acompanhar a produção pedido a pedido e responder "quanto falta neste lote?". |
| **Unidade** | Uma peça física produzida dentro de um lote, numerada. Existe porque cada unidade tem a sua própria história: quem montou, quem testou, se foi reprovada e quem a corrigiu. |
| **Procedimento do lote** | O procedimento que cada placa do produto segue num lote. É uma entidade associativa entre Lote e Procedimento: permite que dois lotes do mesmo produto sigam procedimentos diferentes ao mesmo tempo, e define as etapas que contam para "quanto falta". |
| **Apontamento** | O registro de que um funcionário fez uma etapa numa unidade: se foi montagem ou teste, quando, e no teste o resultado. É uma entidade própria, e não campos na Unidade, porque uma unidade acumula vários apontamentos: várias pessoas, várias etapas, testes e retrabalho. Campos como "montador" e "testador" só guardariam uma pessoa em cada papel. |
| **Funcionário** | Quem monta e testa as placas. Existe para que cada apontamento diga quem fez o trabalho. Não acessa o sistema. |
| **Usuário** | Quem entra no sistema, com um perfil: supervisora ou dono. Existe separado do Funcionário porque quem administra o sistema não é, necessariamente, quem aparece nos apontamentos. Um funcionário pode ganhar acesso no futuro, ligado a um usuário, sem mudar o modelo. |

### Por que a receita não é uma entidade

A receita de uma placa (a foto, os componentes com as suas quantidades e os
procedimentos de montagem) já está distribuída entre Placa, Item da receita,
Procedimento e Etapa. Uma entidade Receita teria exatamente um registro por
placa e nenhum dado próprio, porque a foto pertence à placa. Por isso
"receita" é um conceito do negócio, usado no texto, e não uma entidade do
modelo.