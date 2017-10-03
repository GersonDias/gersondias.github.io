---
layout: post
title: 'Voltando ao básico: Database Index'
excerpt: "No desenvolvimento moderno de aplicações, acabamos nos esquecendo de pensar sobre o funcionamento das bases de dados que utilizamos, já que as abstraímos em nossos ORMs e confiamos que estes componentes irão fazer um bom trabalho. Mas vamos voltar um pouquinho as bases e relembrar (ou aprender) o que são os Índices de nossos bancos de dados."
tags: [Javascript, NodeJS, C#, Development]
comments: true
---
# Database Index

Hoje em dia, é praticamente um padrão a utilização de ORMs como componente de nossas aplicações. Isto, claro, devido à complexidade de tratar dois mundos diferentes como são os modelos relacionais das bases de dados "tradicionais" e o mundo orientado à objetos das linguagens mais utilizadas no mercado. Escrever queries, procedures, functions, tables pode parecer um pouco complicado para quem não está acostumado e a profissão de DBA parece, ao meu ver, cada vez mais rara no mercado. Porém é necessário que nós que nos propomos à escrever software tenhamos um entendimento ao menos básico de um dos principais componentes que entregamos em nossas soluções.

Vamos lá, hoje falaremos sobre índices. Primeiro, deixando claro que trarei conceitos que podem ser extrapolados para qualquer banco de dados relacional (SQL Server, Oracle, MySql, etc), porém minha experiência é com o SQL Server, portanto pode ser que eu discorra sobre algo específico deste DBMS. Outra questão é que esta é uma visão de desenvolvedor para desenvolvedor, e cobrirei somente o conceito básico do tema.

## Dados e informações

Sabemos que bancos de dados tem a primordial tarefa de guardar e recuperar dados. Isto não é mistério e todos os bancos de dados do mercado fazem isso muito bem. Os dados são organizados em tabelas e estas tabelas tem suas colunas que fazem com que determinado dado possa ser visto como uma informação, com sua própria semântica. Ou seja, eu posso ter os seguintes dados que vieram da minha aplicação:

```text
Gerson
Afonso
Dias
1986-08-31
112233445
12344563665
```

Estes dados soltos não significam nada, é preciso dar semântica à estes dados. Então podemos organizar estes dados em uma forma melhor estruturada, em uma tabela com colunas que indiquem o que estes dados significam:

| Name   | MiddleName | LastName | BirthDay   | PhoneNumber | SocialNumber |
|--------|------------|----------|------------|-------------|--------------|
| Gerson | Afonso     | Dias     | 1986-08-31 | 112233445   | 12344563665  |

Assim sabemos exatamete que esta tabela guarda a informações de pessoas e quais são as partes de informações que posso utilizar. Porém, como posso recuperar os dados de uma pessoa específica dentro do meu sistema? Como vou saber se aqueles dados são do Gerson e não da Juliana? Como poderei exibir ou utilizar os dados de uma pessoa específica?

Bom, a estratégia mais comum é identificarmos um elemento que seja único de cada linha de minha tabela. Se alguém pensou que o elemento "SocialNumber" poderia ser este item que identifica unicamente uma pessoa, você pode estar errado dependendo do tipo de pessoas que fazem parte do seu sistema (por exemplo, em um consultório médico que atende crianças e adultos, pode ser que o SocialNumber seja compartilhado em mãe e a criança). Então, a prática mais comum é que tenhamos um elemento gerado automaticamente pelo nosso sistema que garanta que seja único, a famosa ```Primary Key```:

| ID | Name | Middle Name | Last Name | BirthDay | PhoneNumber | SocialNumber |
|----|------|-------------|-----------|----------|-------------:|--------:|
1 | Gerson | Afonso | Dias | 1986-08-31 | 112233445 | 12344563665
2 | Ana | | Barros | 1984-12-04 | 1123332 | 1123332323

A estratégia de gerar um ID sequencial é uma das mais utilizadas, porém trás alguns incovenientes como a possibilidade de uma pessoa má intencionada poder identificar de maneira fácil detalhes sobre a sua base de dados, como "quantos clientes este sistema possuí" ou poder tentar utilizar o ID de outra pessoa para alguma operação que ela não está autorizada a fazer. De qualquer forma, tirando este aspecto de segurança, a utilização de chaves sequenciais trás uma enorme vantagem para a recuperação dos dados: Se eu quiser o ID número 88923, sabendo que esta tabela é sequencial, o banco de dados pode utilizar um algoritmo de busca binária para achar o registro correto de forma muito eficiente. Se você utilizar os comandos específicos do SQL Server para analizar o plano de execução de sua consulta, este caso aparecia como ```Index Seek```. Porém, como eu posso definir a melhor forma de ordenação de minha tabela, para que eu possa recuperar os dados da forma mais eficiente possível? Bom, é aí que entram os índices.

Vamos imaginar que em meu sistema eu tenho em vários pontos pesquisas pelo SocialNumber (afinal é uma informação que a pessoa sabe, dificilmente ela irá se lembrar do ID dela em seu sistema), e digamos que sempre que eu procuro pelo SocialNumber eu queira recuperar também o nome da pessoa, afinal quero exibir esta informação para o usuário final. Bom, então por que não guardar estas informações específicas em algum lugar diferente para que esta informação possa ser recuperada de forma ainda mais rápida? Por que não utilizar algo deste tipo:

| ID | SocialNumber | Name   |
|----|--------------|--------|
| 1  | 12344563665  | Gerson |
| 2  | 1123332323   | Ana    | 

Sendo uma estrutura de dados menor, é mais simples para o banco de dados fazer esta busca e sempre que você fizer uma busca pelo SocialNumber ele utilizará esta estrutura de dados que é chamada de ```Non clustered Index```. Eu posso ter quantos indices deste tipo eu quiser em minhas tabelas, organizando os dados dela para que sejam recuperadas da maneira mais eficiente possível. Porém, há alguns pontos à se considerar:

1. *Storage*: Sendo uma estrutura de dados diferente da tabela original, cada índice ocupa espaço em disco
1. *Escritas ficam mais lentas*: Ao inserir um novo registro, o banco de dados irá colocar um novo registro no índice, ao atualizar ou apagar um registro, também.

E outro ponto importante é que uma pesquisa que retorne todos os dados da tabela, porém tenha na sua clausula `where` os campos do índice, irá fazer a pesquisa primeiro no índice e depois irá até a tabela "principal" e irá pegar as outras informações. Então dizer que seus `selects` ficarão mais rápidos pela utilização de índices, nem sempre é uma verdade.

Mas o que podemos fazer para não termos todas estas questões e ainda utilizar o poder dos índices? Bom, o que podemos fazer é ordenar de maneira satisfatória a nossa tabela para que ele guarde todos os dados de uma maneira que seja possível utilizar o algoritmo de busca binária para achar os dados que preciso. Por padrão o SQL Server utiliza a Chave Primária para fazer esta ordenação, o que chamamos de ```Clustered Index```. Porém, aqui cabe lembrar que estamos ordenando a nossa tabela em si, com todos os seus dados, então, claro, só podemos definir uma ```Clustered Index``` em cada uma de nossas tabelas.

Você já ouviu falar que GUIDs atrapalham o desempenho das buscas em bancos de dados? É exatamente porque a utilização de GUIDs como chave primária, sem definir uma "Clustered Index" que seja ordenável, faz com que o banco de dados não consiga utilizar o algoritmo de busca binária para achar um determinado registro e, portanto, terá que fazer um "Table Scan" para achá-lo, ou seja, terá que passar linha a linha da tabela fazendo a comparação com o valor solicitado. Uma estratégia que pode ser utilizada no caso de ser necessário a utilização de GUIDs como identificadores é a criação de uma coluna com um número sequencial que será utilizada como o ```Clustered Index```, porém estratégias como estas não são implementadas por padrão pelos ORMs, e nós precisamos saber ao menos o básico do que está acontecendo para que possamos estudar se é necessária uma configuração específica em nossos ORMs.

E você, quais conceitos básicos dos elementos que compõem nossas soluções de software acha que estão sendo esquecidas com as "modernindades" e frameworks que vemos por aí?

Abraços!