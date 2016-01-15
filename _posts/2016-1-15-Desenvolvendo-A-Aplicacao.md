---
layout: post
title: "VSTS Extensions Parte 3 - Desenvolvendo a aplicação"
excerpt: "Analizando o fluxo de desenvolvimento de extensões para VSTS"
tags: [VSTS, DESENVOLVIMENTO, EXTENSÕES]
comments: true
---

Com o arquivo de manifesto pronto, podemos partir para o desenvolvimento de nossa aplicação. Vamos lá!

##Inicializando a aplicação
Você pode desenvolver a sua aplicação da maneira que achar melhor, com os componentes que quiser e seguindo a arquitetura que achar melhor. Não há nenhum tipo de restrições impostas pelo VSTS para suas aplicações. A única questão a tomar atenção na página de sua aplicação (que foi definida no arquivo de manifesto), é inicializar o SDK do VSTS, com o seguinte código:

``` html
<body>
    <script type="text/javascript">
        // Initialize the VSS sdk
        VSS.init({
            setupModuleLoader: true,
            moduleLoaderConfig: {
                paths: {
                    "Scripts": "scripts"
                }
            }
        });
        // Wait for the SDK to be initialized
        VSS.ready(function () {
            require(["Scripts/app"], function (app) { });
        });
    </script>
    ...
``` 


Como a intensão desta série de artigos não é focar no desenvolvimento da parte web da extensão e sim nos pontos de integração com a plataforma do VSTS, como prova de conceito, iremos utilizar o código de exemplo contido no site [todomvc](http://todomvc.com/examples/typescript-angular/), mostrando um todo/checklist com as funcionalidades básicas utilizando Angular + Typescript. Veja o código no [repositório do projeto de exemplo](https://github.com/tastejs/todomvc/tree/gh-pages/examples/typescript-angular) e continue acompanhando nosso [repositório](https://github.com/GersonDias/vso-workItemChecklist).

Como resultado de nossa codificação, temos a seguinte imagem:

![Exemplo Checklist]({{site.url}}\images\Extensions\Checklist-Sample.png)
 
Neste ponto você verá que apesar de funcional, nossa extensão ainda não é utilizável, afinal o mesmo checklist é exibido em todos os workitems. No próximo artigo iremos explorar os pontos de integração disponíveis para as extensões e os serviços de armazenamento de dados e busca de informações sobre o contexto da ação do usuário dentro do VSTS.

Até a próxima!!

####Posts anteriores:
* [Desenvolvendo Extensões para o Visual Studio Team Services - Parte 1](http://blog.gersondias.net/Desenvolvendo-Extensoes-Para-O-Visual-Studio-Team-Services/) 
* [VSTS Extensions Parte 2 - O arquivo de Manifesto](http://blog.gersondias.net/VSSExtensions-O-Manifesto/)
