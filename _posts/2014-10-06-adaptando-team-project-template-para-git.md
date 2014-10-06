---
layout: post
title: "Adaptando o Team Project Template para criação de projetos com GIT"
excerpt: "O que fazer quando você recebe um erro ao criar um projeto utilizando o Git no TFS"
tags: [TFS, Team Foundation Server, GIT, Process Template]
comments: true
---

Quando trabalhamos com customizações diversas nos Team Project Templates, devemos tomar alguns cuidados e esperar por algumas surpresas quando queremos utilizar recursos novos do TFS. No caso de hoje, temos o exemplo um Team Project Process Template com diversas customizações feitas na versão 2012 do Team Foundation Server. Ao atualizarmos para a versão 2013 a primeira coisa que queriamos testar era a criação de Team Projects que utilizassem o GIT como Source Control. Eis que encontramos a seguinte surpresa:
<br/>
![The process template that you are using does not support Git]({{site.url}}/images/Create-Project-Git-Error.png)
<br/>
Para corrigir este erro, precisamos baixar o process template para a máquina local para que possamos editá-lo. No Visual Studio, vá no menu Team -> Team Project Collection Settings -> Process Template Manager. Escolha o template que você quer modificar e clique em Download. Aponte um caminho em sua máquina onde os arquivos serão salvos. Abra o arquivo .\Version Control\VersionControl.xml e coloque as seguintes linhas entre as tags `<taskXml>`:

{% highlight xml %}
<git>
        <permission allow="GenericRead, GenericContribute, ForcePush, Administer, CreateBranch, CreateTag, ManageNote" identity="[$$PROJECTNAME$$]\$$PROJECTADMINGROUP$$" />
        <permission allow="GenericRead, GenericContribute, CreateBranch, CreateTag, ManageNote" identity="[$$PROJECTNAME$$]\Contributors" />
        <permission allow="GenericRead, GenericContribute, CreateBranch, CreateTag, ManageNote" identity="[$$PROJECTNAME$$]\Build Administrators" />
        <permission allow="GenericRead" identity="[$$PROJECTNAME$$]\Readers" />        
</git>
{% endhighlight %}

Salve o arquivo e volte para o Visual Studio. No Process Template Manager, faça o Upload do Process Template que acabamos de editar. Confirme que queremos fazer o *override* do template atual e tente criar o Team Project novamente. Problema resolvido!

### Lembre-se
Quaisquer customização que fazemos no Team Foundation Server pode apresentar algum problema durante o upgrade de versão. Então esteja atento a isto e faça algo parecido com um *inventário* sobre todas as customizações que você tem no seu TFS. Leve isto em conta no planejamento do upgrade (seja de versões - 2012 para 2013 - ou de updates - 2013 update 2 para update 3).





