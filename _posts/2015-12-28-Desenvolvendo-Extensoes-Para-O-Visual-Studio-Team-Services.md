---
layout: post
title: "Desenvolvendo extensões para o Visual Studio Team Services - parte 1"
excerpt: "Como desenvolver novas funcionalidades para o VSTS"
tags: [VSTS, DESENVOLVIMENTO, EXTENSÕES]
comments: true
---

Há poucos meses a Microsoft liberou uma grande funcionalidade para alguns de seus produtos mais novos: A capacidade de desenvolver extensões para o Visual Studio Code e para o Visual Studio Team Services. Como integrante do programa Microsoft ALM Rangers, tive o privilégio de trabalhar em contato direto com a equipe do produto para criar uma extensão que fez parte do lançamento do Marketplace. A extensão que trabalhei se integra com o site [toggl.com](http://toggl.com) para possibilitar um "time tracker" direto do formulário dos workitems. Confere lá! [VSO TogglButton](https://marketplace.visualstudio.com/items/GersonDias.TogglButton)

Vamos começar agora uma série de posts mostrando o desenvolvimento de uma extensão para o VSTS.

## Protótipo
A idéia é bastante simples, mas creio que seja bastante útil também e sugestões serão muito bem vindas nos comentários: Fazer uma nova aba no formulário de workitem em que eu possa fazer um checklist. Imagine que eu tenha uma lista de requisitos dado pelo cliente para determinada tarefa, ou minha equipe tenha uma definição de pronto que deva ser seguida por cada workitem - neste cenário, ter um lugar para cadastrar e acompanhar estes "lembretes" dentro do próprio formulário do WorkItem pode ser bastante útil.

Seria algo deste tipo:

![Protótipo]({{site.url}}/images/Extensions/Prototipo.png)

## Criando uma extensão, step-by-step
Durante o desenvolvimento da nossa extensão, vamos executar as seguintes atividades:

1. Criar o arquivo de manifesto da extensão
1. Codificar a extensão
1. Empacotar a extensão em um arquivo .vsix
1. Publicar a extensão de forma privada
1. Compartilhar com pessoas específicas, para obter feedback
1. Opcionalmente, vamos tornar a extensão pública no [Marketplace](http://marketplace.visualstudio.com)

Durante os posts vamos entrando em maiores detalhes sobre cada um destes pontos. 

## Setup do Ambiente de Desenvolvimento
Antes de mais nada, precisamos configurar uma série de ferramentas para que possamos começar nosso desenvolvimento.

### Chocolatey
Primeiro vamos instalar o Chocolatey, um gerenciador de pacotes que irá facilitar a instalação das outras ferramentas que teremos que utilizar. Para instala-lo, basta abrir uma instância do PowerShell e digitar o seguinte comando:

```
iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
```

### Node
As extensões para o VSTS são baseadas em Node.js, o que mostra a grande mudança de mindset que esta dentro da Microsoft. Vocês já ouviram o quanto ela esta aberta para o mundo OpenSource, e isto é ótimo! 

Para instalar o Node, basta executar o comando:

```
choco install nodejs.install
```

### Pacotes NPM
Vamos instalar algumas ferramentas via NPM que utilizaremos durante nosso desenvolvimento:

* TFX - TFS Cross Platform Command Line Interface

```
npm install tfx-cli -g
```

* Grunt

```
npm install grunt-cli -g
```

### IDE de Desenvolvimento
Você pode utilizar qualquer IDE de desenvolvimento. A extensão que comentei foi toda feita no Visual Studio Code, mas, caso escolha fazer o desenvolvimento utilizando o Visual Studio, instale o template de projeto feito pelo ALM Ranger Josh Garverick, chamado VSTS Extension Project Template. Utilizaremos este template durante nosso desenvolvimento.

## Criando um Publisher
O último passo que devemos fazer para a termos nosso ambiente totalmente configurado é criar nosso profile no marketplace e podermos publicar nossa extensão. Para isso, entre no [Portal de Publicação do Visual Studio Marketplace](https://marketplace.visualstudio.com/manage), especifique um identificador e um nome para seu publisher e clique no botão Create.

![Protótipo]({{site.url}}/images/Extensions/CreatePublisher.png)

## Finalizando
Todos estes passos são necessários para que possamos desenvolver nossa extensão. No [repositório do GitHub](https://github.com/GersonDias/vso-workItemChecklist) temos a estrutura inicial de nosso projeto, que é Open Source e esta aberta a contribuições! Farei o desenvolvimento enquanto escrevo os posts, este é um trabalho em progresso! 

No próximo post iremos começar a codificar nossa extensão! 