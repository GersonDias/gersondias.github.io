---
layout: post
title: "Valores e princípios ágeis (ou porque o Scrum não é importante)"
excerpt: "Não importa a metodologia utilizada, siga os Princípios Ágeis"
tags: [Agilidade]
comments: true
---

Há pouco mais de dois anos, quando entrei na Lambda3, assumi a tarefa de integrar o time de Consultores ALM. Basicamente o que fazemos é ir até empresas e indicar e guiar aquela equipe em práticas e ferramentas que os levem a ter uma maior produtividade e assertividade no desenvolvimento de software. Desde então, conheci diversas empresas, em diversos locais do Brasil e vi que elas têm muito em comum:

1. Querem seguir metodologias ágeis;
1. Não querem seguir o Scrum.

Tudo bem, seguimos adiante. Grande parte das vezes também guiei estas empresas para a utilização do Team Foundation Server/Visual Studio Team Services. Em determinados momentos, quando eu iria explicar sobre as ferramentas de [Planejamento Ágil](https://www.visualstudio.com/en-us/features/agiletools-vs.aspx), ouvia coisas do tipo:

"Olha, melhor nem falar em Sprints, pois o pessoal aqui tem até calafrios quando ouvem esta palavra".

Com o tempo meu espanto em ouvir este tipo de coisas foi diminuindo. De qualquer forma, estas mesmas pessoas falavam coisas do tipo:

"Olha, aqui criamos a metodologia <Nome da Empresa>Agile. Isto porque somos diferentes, em nosso ambiente agimos assim e assado, mas queremos adotar as metodologias ágeis!".

Antes de continuar, vou abrir um adendo da ferramenta que geralmente puxa esta palavra (apenas lembre-se que o foco aqui não é a ferramenta em si). No VSTS, quando criamos um projeto, podemos escolher qual o Template que ele irá utilizar. Este Template trará diferenças principalmente no trato com os Itens de Trabalho, que terão informações diferentes que devem ser preenchidos pelas pessoas que trabalham naquele projeto. Por padrão, temos três diferentes templates: CMMI, Agile, Scrum.

Bom, é hora de escolher o Template de Projeto para a ferramenta. De cara, o CMMI é descartado, afinal "queremos ser ágeis!". O Scrum também é descartado, afinal "xii, Scrum não funciona aqui!" e ficamos com a escolha do template Agile, afinal, "queremos ser ágeis"! Neste momento, o cliente sente que está "Instalando a Agilidade na empresa" e tenho que ser o primeiro a dar a notícia: *Não é a escolha do Template de Projeto da ferramenta que irá te tornar Ágil!".

##O Scrum não importa
O Scrum não importa, o Kanban não importa, o Crystal não importa... nenhum Framework importa, porque você será Ágil se você _for_ ágil.

Ops, mas o que isso significa?

Veja esta imagem:

![Logical Levels]({{site.url}}/images/LogicalLevels.png)

Este modelo foi proposto por Robert Dilts e Todd Epstein, e mostra como a psique humana se comporta. Seguindo alguns padrões como:

1. Estamos em um ambiente, e temos diferentes comportamentos em diferentes ambientes: Em nossa casa nos comportamos de uma forma, em nossa empresa de outra e assim por diante. Ou seja, o ambiente determina nosso comportamento
1. Nosso comportamento tem como base nossas capacidades. Diferentes capacidades trazem diferentes comportamentos
1. Estas capacidades que temos são desenvolvidas tendo por bases nossas crenças (se sou uma pessoa que não acredita na violência, não há motivos para eu aprender a atirar com uma arma, por exemplo)
1. Estas crenças que temos estão baseadas em nossa visão de mundo
1. E nossa visão de mundo vem de nosso local mais profundo, vem de nossos ideais (em algumas referências, este nível é chamado de Espírito)

Como exemplo, imagine que você está em um baile de dança de salão. Você vê os casais dançando e sente vontade de dançar também (o ambiente agindo sobre você). Porém você não tem esta habilidade ou capacidade e nem acredita ser um dançarino, então você não irá dançar.
Agora imagine que você esta neste mesmo baile e sentiu uma conexão e viu aqueles rostos sorridentes, aqueles corpos dançando em harmonia com a música e toda aquela magia e viu que aquilo tem tudo a ver com o que você acredita. Neste momento houve uma conexão com aquela atividade (aquilo foi em conformidade com os seus Ideais). Você então passou a acreditar que aquilo é possível e você quer fazer isso, então você se vê e É um dançarino. Então procurará uma escola de dança para aprimorar suas capacidades e o seu comportamento em um baile será dançar.

Ok, mas que relação isso tem com a Agilidade e desenvolvimento de software? Bom, se você quer ser Ágil, não é um Framework que te fará Ágil e sim os princípios ágeis. Estes princípios estariam no nível das Crenças no modelo proposto e você só será Ágil se (e somente se) elas estiverem de acordo com a Visão e Ideais da sua empresa. 

Aqui temos mais um ponto de atenção: Quando falo dos princípios ágeis, não estou falando _somente_ destes:

Indivíduos e interações mais que processos e ferramentas
Software em funcionamento mais que documentação abrangente
Colaboração com o cliente mais que negociação de contratos
Responder a mudanças mais que seguir um plano

Estou sim falando dos 12 Princípios Ágeis:

* Nossa maior prioridade é satisfazer o cliente através da entrega contínua e adiantada de software com valor agregado. 
* Mudanças nos requisitos são bem-vindas, mesmo tardiamente no desenvolvimento. 
* Processos ágeis tiram vantagem das mudanças visando vantagem competitiva para o cliente. 
* Entregar frequentemente software funcionando, de poucas semanas a poucos meses, com preferência à menor escala de tempo. 
* Pessoas de negócio e desenvolvedores devem trabalhar diariamente em conjunto por todo o projeto. 
* Construa projetos em torno de indivíduos motivados. 
* Dê a eles o ambiente e o suporte necessário e confie neles para fazer o trabalho. 
* O método mais eficiente e eficaz de transmitir informações para e entre uma equipe de desenvolvimento é através de conversa face a face. 
* Software funcionando é a medida primária de progresso. 
* Os processos ágeis promovem desenvolvimento sustentável. Os patrocinadores, desenvolvedores e usuários devem ser capazes de manter um ritmo constante indefinidamente. 
* Contínua atenção à excelência técnica e bom design aumenta a agilidade. 
* Simplicidade--a arte de maximizar a quantidade de trabalho não realizado--é essencial. 
* As melhores arquiteturas, requisitos e designs emergem de equipes auto-organizáveis. 
* Em intervalos regulares, a equipe reflete sobre como se tornar mais eficaz e então refina e ajusta seu comportamento de acordo.

_Alguns Fremoworks colocam outros valores nesta lista. [Recentemente o Scrum fez um update de seus valores](http://blog.lambda3.com.br/tag/scrumguide-update/)_

Você acredita nestes valores? Você os entende? Você sabe quais são as capacidades necessárias para alcançar cada um destes princípios? E, principalmente, você *É* Ágil?

