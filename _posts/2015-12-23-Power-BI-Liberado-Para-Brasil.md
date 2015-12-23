---
layout: post
title: "PowerBI liberado para os usuários do office 365 no Brasil"
excerpt: "Uma forma incrível de ver estatísticas sobre o VSTS"
tags: [VSTS, POWERBI]
comments: true
---

Sem muito alarde a Microsoft liberou o acesso à ferramenta [PowerBI](http://powerbi.com) aos Brasileiros. Apesar desta ferramenta já estar disponível há algum tempo para a grande maioria do mundo, por algum motivo a Microsoft deixou mercados importantes como o Brasil e a China para uma segunda leva de liberação de acesso. Para quem ainda não teve contato com esta ferramenta, imagine a funcionalidade PowerPivot do Excel melhorada, rodando na nuvem e com diversos conectores para muitos serviços, estejam eles na nuvem ou rodem localmente, entre eles:
* GitHub
* AppInsights
* Visual Studio Team Service (antigo Visual Studio Online)
* MailChamp
* Bases SQL Locais
* Analysis Services
* Arquivos
* etc...

O grande mote do PowerBI é trazer vida aos seus dados e uma das funcionalidades mais bacanas é você poder fazer perguntas sobre os dados que estão sendo exibidos para você. Mas vamos ver como tudo isso funciona na prática.

## Pré-requisitos
O Power BI é uma funcionalidade do Office 365, portanto você precisa de uma assinatura do produto para poder utilizá-lo. Por uma dezena de dólares adicionais você ganha algumas funcionalidades extras como refresh automático a cada hora, maior poder de processamento de dados, consumo de fonte de dados em tempo real e algumas ferramentas de colaboração para criar novos dashboards. Então basta entrar no endereço [](http://powerbi.com), clicar e sign in e seguir os passos para efetuar o cadastro.

## Conectando ao VSTS
Ao entrar no PowerBI, você verá uma tela de "Get Started" onde você pode selecionar a opção Service para se conectar a diversos serviços: 
![Power BI - Get Data]({{site.url}}/images/pwb.GetData.png)

Escolha o serviço Visual Studio Online e clique em Connect 
![Visual Studio Online Data Connector]({{site.url}}/images/pwb.VisualStudioOnline.png)

Digite o nome da sua conta do VSTS (o nome dela, não a URL dela) e o nome do Team Project que você quer ter maiores informações: 
![Visual Studio Connection 1]({{site.url}}/images/pwb.connection1.png)

Clique em SignIn e aceite as permissões necessárias para o PowerBI conectar-se ao VSTS: 
![Visual Studio Connection 2]({{site.url}}/images/pwb.Connection2.png)

Feito isto, basta aguardar o Dashboard inicial do PowerBI ficar pronto (pode demorar alguns bons minutos). Divirta-se vendo top commiters, bug trend, situação das sprints e muitas outras informações! 
![PowerBI Dashboard]({{site.url}}/images/pwb.Dashboard.png)

## Finalizando
Não deixe de explorar todo o potencial desta ferramenta, que faz com que um dos principais gaps entre o TFS e o VSTS seja superado com maestria! 


Até a próxima! 











