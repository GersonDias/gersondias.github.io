---
layout: post
title: "Vulnerabilidade encontrada no Git"
excerpt: "Uma grave vulnerabilidade foi descoberta nos clientes Git"
tags: [TFS, Git, Vulnerabilidades, Segurança]
comments: true
---

Hoje foi divulgada a descoberta de uma vulnerabilidade nas entranhas do código fonte do Git que permite que pessoas mal intencionadas tomem o controle de uma máquina que baixe um repositório "infectado". 

Para entender a causa do problema, precisamos lembrar que o Git guarda nos repositórios locais um arquivo chamado 'config', dentro da pasta .git. Esse arquivo contém uma série de configurações referentes aquele repositório em específico e geralmente é alterado com o comando 

```
git config --local
```
Bom, a questão é que geralmente este arquivo de configuração local não é sobrescrito nos clients que baixam o repositório. Porém, este filtro que esta presente nos clients Git ignoram somente a pasta .git, não a .Git ou a .gIt, ou qualquer outra variação deste nome.

Inicialmente este problema pode não parecer tão ruim, mas, se lembrarmos da possibilidade de renomearmos qualquer comando git com um alias (que ficam guardados no arquivo de configuração do repositório) e pode executar qualquer comando, um simples git push poderia formatar um computador ou ser a porta de entrada de malwares e tantos outros riscos possíveis. 

Claro que também há a questão de que este tipo de ataque deve vir de uma pessoa que tenha a permissão de push no repositório. Mas não é por isso que não devemos nos precaver não é mesmo?

De qualquer forma, esta é uma vulnerabilidade nas aplicações clients do Git, então, caso esteja utilizando o Windows, baixe a nova versão do [msysgit](http://msysgit.github.io/) e/ou o o [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=523729) (é necessário ter o Update 4 desta versão já instalado).

Apesar de, como já dito, esta ser uma vulnerabilidade dos clients, a Microsoft também lançou um path para o [TFS 2013 Update 4] (http://go.microsoft.com/fwlink/?LinkId=523727)

Para maiores informações:
http://blogs.msdn.com/b/bharry/archive/2014/12/18/git-vulnerability-with-git-config.aspx
http://msysgit.github.io/
https://github.com/blog

Abraços!