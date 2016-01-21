---
layout: post
title: "VSTS Extensions Parte 4 - Integrando com o VSTS"
excerpt: "Analizando o fluxo de desenvolvimento de extensões para VSTS"
tags: [VSTS, DESENVOLVIMENTO, EXTENSÕES]
comments: true
---

Finalizamos o último artigo com nossa aplicação totalmente funcional, porém nem um pouco útil. Como nossa extensão ainda não sabe "onde ela vive", ela não é lá muito útil, afinal o mesmo checklist aparece em todos os workitems e são gravados "no navegador" do usuário (via a API de localstorage). Neste post vamos fazer nossa extensão saber mais informações sobre o workitem que ela esta sendo executado e vamos adaptar o código para termos 1 checklist por workitem.

##Serviços do VSSSDK
Existem diversos serviços que você pode executar a depender do que sua extensão irá fazer. Hoje, precisamos apenas saber qual é o ID do workitem que esta sendo exibido na tela do usuário. Para tal, temos que utilizar o seguinte serviço: `TFS/WorkItemTracking/Services`. Para utilizá-lo, basta utilizar o método `require` do SDK, da seguinte forma:

{% highlight js %}
{% raw %}

VSS.require(['TFS/WorkItemTracking/Services'], function (workItemService) {
                workItemService.WorkItemFormService.getService().then(function (workItem) {
                    workItem.getId().then(function (workItemId) {
                        var todos = todoStorage.get(workItemId);
                        self.todos = todos;

                        self.workItemId = workItemId; 

                        $scope.newTodo = '';
                        $scope.editedTodo = null;

                        $scope.vm = self;
                       
                        $scope.todos = todos;                         
                        
                        $scope.$apply();
                    });
                })
            });          

{% endraw %}
{% endhighlight %}

O método require retorna o serviço que passamos como parâmetro em uma função `callback`. Devido a natureza assíncrona e o desejo que a UI não congele enquanto esperamos o retorno de nossos serviços, a API do VSTS utiliza em larga escala as `promisses`. Neste código, repare como fizemos para obter o ID do Workitem (linha 3) e, além disto, modificamos o método `get` do serviço `todoStorage` para receber este ID como parâmetro.

O código do serviço ficou da seguinte forma:

{% highlight js %}
{% raw %}

module todos {
    'use strict';

    export class todoStorage implements ITodoStorage {
        STORAGE_ID = 'todos-angularjs-typescript';

        workItemStorage(workItemId: number): string {
            return this.STORAGE_ID + workItemId;
        }

        get(workItemId: number): todoItem[] {
            return JSON.parse(localStorage.getItem(this.workItemStorage(workItemId)) || '[]');
        }

        put(todos: todoItem[], workItemId: number) {
            localStorage.setItem(this.workItemStorage(workItemId), JSON.stringify(todos));
        }
    } 
}

{% endraw %}
{% endhighlight %}

Ainda temos o mesmo problema de utilizar o `localStorage` para salvar os dados, mas ao menos temos um checklist por workitem!

No próximo artigo iremos estudar as possibilidades de integração para salvarmos estes dados na própria plataforma do VSTS.

####Posts anteriores:
* [Desenvolvendo Extensões para o Visual Studio Team Services - Parte 1](http://blog.gersondias.net/Desenvolvendo-Extensoes-Para-O-Visual-Studio-Team-Services/) 
* [VSTS Extensions Parte 2 - O arquivo de Manifesto](http://blog.gersondias.net/VSSExtensions-O-Manifesto/)
* [VSTS Extensions Parte 3 - Desenvolvendo a Aplicação](http://blog.gersondias.net/Desenvolvendo-A-Aplicacao/)