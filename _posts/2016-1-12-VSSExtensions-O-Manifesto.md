---
layout: post
title: "VSTS Extensions Parte 2 - O arquivo de Manifesto"
excerpt: "Como descrever uma extensão para o VSTS"
tags: [VSTS, DESENVOLVIMENTO, EXTENSÕES]
comments: true
---

Hoje iremos criar a estrutura inicial de nosso projeto e iremos estudar um pouco do arquivo de manifesto. Vamos lá!

##Estrutura inicial
A estrutura de nosso projeto não é nenhum segredo. Por tratar-se de um projeto Javascript, você tem bastante liberdade para estruturá-lo da forma como achar melhor. De qualquer forma, alguns pontos são importantes:

###Arquivo vss-extension.json
Em teu projeto você deverá ter um arquivo chamamdo vss-extension.json. Este arquivo é o "manifesto" de sua extensão, ela a descreve, informa seu nome, versão, permissões necessárias (sua extensão irá mexer com workitems, com código?) onde ela estará localizada no Marketplace e muitas outras informações que serão utilizadas para criar a página de nossa extensão dentro do Marketplace. Todas as informações que você vê [nesta página](https://marketplace.visualstudio.com/items/GersonDias.TogglButton) foram extraídas do arquivo de manifesto. Vamos falar mais deste arquivo em breve.

###VSS.SDK
Outro arquivo obrigatório em seu projeto é o SDK de extensibilidade do VSTS. Faça o [download](https://github.com/Microsoft/vss-sdk) do arquivo javascript e coloque em uma pasta chamada scripts, ele será mais que necessário!

###Template de projeto
Em nossa extensão utilizaremos um Template de projeto preparado pelo ALM Ranger Josh Garverik. Esta extensão já trás os arquivos que falamos com exemplos de preenchimento e também uma estrutura bem organizada para fazermos nosso desenvolvimento utilizando TypeScript. Para instalar este template, no Visual Studio, vá em `Tools -> Extensions and Updates` e procure pelo template `VSTS Extension Project Template`. Instale-o, reinicie o Visual Studio e você verá este projeto quando clicar em `File -> New Project -> Other Languages -> Type Script -> Visual Studio Team Services Extension`

![New Project]({{site.url}}\images\Extensions\VSSExtension-New Project.png)

##Preenchendo o arquivo vss-extension.json
Vamos fazer o preenchimento inicial de nosso arquivo de manifesto. Consulte a [documentação](https://www.visualstudio.com/en-us/integrate/extensions/develop/manifest) para detalhes de como preencher este arquivo. Em nosso arquivo preencheremos as seguintes propriedades:

* __manifestVersion__: Deve ser preenchido com `1`
* __id__: Um identificador único por _publisher_ em nosso caso, `WICheckList`
* __version__: A versão de nossa extension, Deve ser no formato `major.minor.patch`, então o nosso ficará `0.1.0`
* __name__: O nome "comercial" de nossa extension (até 200 caracteres). `WorkItem CheckList`
* __publisher__: O identificador de nosso publisher, que criamos no post anterior. Em meu caso, `GersonDias`. Para maiores informações [Criando e administrando um publisher](https://www.visualstudio.com/integrate/extensions/publish/overview) 
* __targets__: São os produtos ou serviços suportados por sua integração ou extensão. Hoje são suportados os seguintes valores:  
`Microsoft.VisualStudio.Services`  
`Microsoft.VisualStudio.Services.Integration`

Este arquivo contém diversas outras propriedades que serão necessárias adiante, conforme formos preenchendo estas propriedades entraremos em detalhe sobre elas. Neste momento, o melhor é que acompanhe em tempo real a evolução deste arquivo no nosso [repositório do GitHub](https://github.com/GersonDias/vso-workItemChecklist/blob/master/vso-WIChecklists/vss-extension.json)

##Contribution Point
Uma propriedade fundamental presente no arquivo _vss.extension.json_ é a __contributions__. Esta propriedade define __onde__ nossa extensão será carregada dentro do VSTS. Em nossa extension, queremos uma nova tab no formulário do _workitem_, então nossa __contribution__ ficará da seguinte forma:

```
"contributions": [
    {  
        "id": "work-item-checklist",
        "type": "ms.vss-work-web.work-item-form-page",
        "description": "WorkItem Checklist",
        "targets": [
            "ms.vss-work-web.work-item-form"
        ],
        "properties": {
            "name": "CheckList",
            "uri": "workItemChecklist.html"
        } 
    }
]
```

##Testando a extensão
Uma das propriedades existentes no arquivo _vss.extension.json_ é a __baseUri__ é justamente ela quem fala qual o endereço base que a o VSTS irá chamar quando for carregar a sua extensão. A única regra aqui é que o endereço seja https! Então, iremos executar nosso projeto utilizando o IIS Express para fins de desenvolvimento, então mude esta propriedade para `https://localhost:44300/` e vamos fazer nossa primeira publicação para testes. Não se esqueça de manter a propriedade __"public": false__ para evitar que outras pessoas vejam nossa extensão.

Para fazermos nossa publicação, precisamos instalar a ferramenta _TFS Cross Platform Command Line Interface_. Para instalá-la, na linha de comando, digite:  

```
npm install -g tfx-cli
```

###Empacotando a extensão

Após a instalação ou update da ferramenta, digite a seguinte linha de comando (lembre-se, você deve estar na pasta do arquivo vss-extension.json):

```
tfx extension create --manifest-globs vss-extension.json
```

Agora você verá um arquivo com o seguinte formato em seu diretório __<publisher>.<extensionId>-<version>.vsix__ este é o pacote de nossa extensão.

###Publicando a extensão
Antes de publicar nossa extensão, precisamos obter nosso token de acesso para que a ferramenta _tfx_ possa fazer a publicação por nós. Entre em na página `https://<your_account_url>/_details/security/tokens` e siga os passos do _wizard_ para criar seu _personal token_. 

Após conseguir o token, volte a linha de comando e digite

```
 tfx extension publish --share-with gersondias --token <seu token> --save
```

Aqui estamos pedindo para efetuar a publicação de nossa extension, e compartilhando ela com determinada conta do VSTS (_gersondias_ em meu caso) e salvando estas informações em um arquivo chamado settings.vset.json para que não precisemos digitar o token novamente quando efetuarmos uma nova publicação desta extensão (não versione este arquivo em locais públicos!).

###Instalando a extension
Depois de todos estes passos, estamos prontos para instalar a nossa extension para que possamos testá-la. Vá até a página de administração das extensões ` `https://<your_account_url>/DefaultCollection/_admin/_apps/hub/ms.vss-extmgmt-web.manageExtensions-collection#tab=Manage&status=Active` e veja que nossa extensão esta lá, disponível para ser instalada:

![Extension Available]({{site.url}}\images\Extensions\VSSExtension-ExtensionAvailable.png)

![Extension Page]({{site.url}}\images\Extensions\VSSExtension-ExtensionPage.png)

Siga os passos para a instalação da Extension. 

###Validando a instalação
Volte ao Visual Studio e rode a aplicação. Ele irá subir o IIS Express e você verá a página inicial da aplicação em seu browser:

![Extension Home]({{site.url}}\images\Extensions\VSSExtension-Home.png)

Agora, em sua conta do VSTS, vá até algum workitem e veja que uma nova aba aparecerá no formulário do WorkItem

![WI Form]({{site.url}}\images\Extensions\VSSExtension-WIForm.png)

###Finalizando
E assim finalizamos este post, com nossa extension publicada e funcionando dentro do formulário do workitem. No próximo post vamos entrar em detalhes sobre a página que é exibida pela nossa extensão e vamos desenvolver nosso Checklist.

Até a próxima! :)

####Posts anteriores:
[Desenvolvendo Extensões para o Visual Studio Team Services - Parte 1](http://blog.gersondias.net/Desenvolvendo-Extensoes-Para-O-Visual-Studio-Team-Services/)