---
layout: post
title: "Customizando o TFS Datawarehouse - Parte 1"
excerpt: "Exemplo de customização do TFS Datawarehouse (TFS Datawarehouse Custom Adapter)."
tags: [TFS, Datawarehouse, Customização TFS, Team Foundation Server]
comments: true
---

Informações são necessárias em qualquer empresa. Os benefícios de informações corretas, providas no momento necessário, muitas vezes são utilizados como um dos principais argumentos de vendas do Team Foundation Server: _"E se vocês soubessem exatamente o requisito que deu origem à determinada linha de código?"_, _"E se vocês tivessem uma ferramenta que lhe provesse os dados sobre o teu ambiente de desenvolvimento de software, com a possibilidade de combinar estes dados para conseguir a informação que você precisa naquele momento?"_. Estas frases estão entre as que serão ouvidas durante o processo de venda ou de implementação de uma solução como o TFS.
<br/>
Porém, seria possível o cruzamento de dados de outros sistemas de gerenciamento, já existentes em minha empresa, com os dados que são coletados pelo TFS? Seria possível "misturar" dados de uma maneira diferente da que o TFS me entrega por padrão? A resposta para estas perguntas é **sim** e, para alcançar estes objetivos, uma das soluções é a customização do TFS Data Warehouse, por meio de um Adapter Customizado.
<br/>
O TFS Data Warehouse basicamente abrange uma base de dados relacional e uma base de dados analítica que agregam dados de todos os seus Team Project Collections e proveêm dados para todos os relatórios que temos. Estas bases de dados podem ser atualizadas via plugins (ou Adapters) que rodam sob o processo TfsJobAgent do TFS. Você pode ter maiores detalhes desta arquitetura no [MSDN](http://msdn.microsoft.com/en-us/library/ms244687.aspx) e, se você for um [bom administrador do Team Foundation Server](http://pt.slideshare.net/ViniciusMoura4/como-ser-um-bom-administrador-de-team-foundation-server-vinicius-moura), não deixe de checar periodicamente o status destes adapters com a ajuda do webservice de administração do data warehouse:

```
http://localhost:8080/tfs/TeamFoundation/Administration/v3.0/WarehouseControlService.asmx
```
<br/>
e sempre de um Oi para seu TFS

```
http://your-server:8080/tfs/_oi/
```

### O problema que queremos resolver 
Agora, vamos ao problema que queremos resolver para este exemplo: um cliente relata ter problemas para gerenciar corretamente as licenças do Visual Studio, pois alguns usuários instalaram versões do Visual Studio diferentes da que eles deveriam em suas máquinas. Além disto, perguntas como “Quem está utilizando a versão Ultimate? De qual Visual Studio (2010, 2012)?” “Quantos estão utilizando a Premium?” eram costumeiras. Sabendo que em algum momento do fluxo de trabalho as pessoas teriam que acessar o TFS, uma das soluções para responder a esta pergunta seria verificar na tabela **tbl_command** da Collection utilizada quais eram os usuários e qual User Agent que ele estava utilizando para se conectar ao TFS. Porém, o problema é que, por padrão, os dados desta tabela são expurgados a cada 14 dias e aumentar este tempo, geralmente, não é uma boa idéia devido ao número de registros que esta tabela carrega, especialmente em um ambiente com mais de 500 usuários ativos por dia no TFS. Assim, a solução adotada foi: fazer um Adapter do Datawarehouse do TFS que verifica de tempos em tempos quais usuários fizeram alguma consulta ao TFS, guardando o software que ele utilizou para fazer esta consulta. Como regra, os dados desta nova tabela não seriam expurgados e nem poderiam sofrer perda de dados por conta de comandos como o *rebuild* do *datawarehouse*. Por conta desta última característica, decidiu-se pela criação de uma nova base de dados no mesmo servidor SQL do TFS e esta base que seria a responsável por carregar a tabela que teria os dados dos usuários que acessaram o TFS ao longo do tempo. Para este exemplo, não será necessária customizações no cubo gerado pelo Analysis Server, apenas mudanças de bases de dados relacionais. Ao final, o relatório será gerado e disponibilizado pelo Excel, utilizando a ferramenta PowerPivot, podendo-se a publicação ocorrer no Sharepoint, via *Excel Services*

### A Solução 
Primeiro e antes de mais nada, clone o repositório que está no GitHub: 
<br/>
```
git clone https://github.com/GersonDias/TFSDatawarehouse.git
```
<br/>
Vamos dar uma olhada em como esta solução está estruturada:

* **WarehouseAdapterApplication:** São códigos utilizados pelo adapter que não estão vinculados a nenhuma versão do TFS, ou seja, este projeto não tem nenhuma referência às DLLs Microsoft.TeamFoundation.*

* **WarehouseAdapterTFS2012:** Projeto que contém as referência para os Assemblies Microsoft.TeamFoundation.* na versão 11.0 do (TFS 2012)

* **WarehouseAdapterTFS2013:** Projeto que contém as referências para os Aseemblies Microsoft.TeamFoundation.* na versão 12.0 (TFS 2013)

* **SetupWarehouseAdapter:** Projeto que contém os códigos necessários para a instalação e configuração do Adapter

* **WarehouseAdapterTestes:** Alguns pequenos testes do projeto

_Alguns pontos importantes sobre a estruturação da solution:_

* Apesar de particularmente gostar de versionar estas Dlls externas junto com o projeto, as Dlls do TFS são proprietárias e não podem ser distribuidas publicamente. Por este motivo, você não vai encontrar um pacote Nuget com estas dependencias, e algumas delas não podem ser encontradas nem mesmo instalando o TFS 2013 Object Model. Assim, existem duas formas de obter estas Dlls: Instalar o TFS Express em sua máquina de desenvolvimento ou obter estes binários de um servidor do TFS.
* Os projetos *WarehouseAdapterTFS2012* e *WarehouseAdapterTFS2013* tem exatamente o mesmo código. A única diferença entre as duas são as versões das Dlls do TFS que são referenciadas. Portanto, resolvi usar um pequeno truque dentro da solution: Criei uma “solution folder” chamada Shared e dentro dela coloquei os arquivos .cs. Dentro de cada projeto, adiciono estes arquivos como um “link” para o arquivo físico. Assim, não preciso me preocupar em fazer alguma mudança na versão 2012 que tenha que ser refeita na versão 2013, ao mesmo tempo que cada projeto em sua pasta bin terá os compilados da mesma maneira e as referências para as versões corretas das Dlls Microsoft.TeamFoundation.*. Este tipo de solução é comum quando temos que trabalhar com diversas versões de Dlls e o mesmo código fonte, tanto que os novos Shared Projects funcionam basicamente da mesma forma, porém deixando tudo “visualmente mais elegante”.

As seguintes referências são utilizadas neste projeto. Os caminhos abaixo são para os que tem o TFS 2013 instalado em sua máquina. Para o TFS 2012 você deverá procurar estas mesmas DLLs nos caminhos de instalação desta versão do TFS:

* **Microsoft.TeamFoundation.Client.dll:** C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\ReferenceAssemblies\v2.0\Microsoft.TeamFoundation.Client.dll
* **Microsoft.TeamFoundation.dll:** Encontrado no GAC da máquina
* **Microsoft.TeamFoundation.Adapter.dll:** C:\Program Files\Microsoft Team Foundation Server 12.0\Application Tier\TFSJobAgent\Plugins
* **Microsoft.TeamFoundation.Common.dll:** C:\Program Files\Microsoft Team Foundation Server 12.0\Application Tier\TFSJobAgent\Plugins
* **Microsoft.TeamFoundation.Framework.Server.dll:** C:\Program Files\Microsoft Team Foundation Server 12.0\Application Tier\TFSJobAgent
* **Microsoft.VisualStudio.Services.Common:** C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\ReferenceAssemblies\v4.5\Microsoft.VisualStudio.Services.Common.dll
* **Microsoft.VisualStudio.Services.WebApi:** C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\ReferenceAssemblies\v4.5\Microsoft.VisualStudio.Services.WebApi.dll (somente para a versão TFS2013)

Após ter refeito todas as referências nos projetos, tente compilá-lo. Em minha máquina tenho uma pasta Lib dentro dos projetos que citamos e aponto esta pasta no Reference Paths do projeto, porém, pelos motivos ditos anteriormente, não posso disponibilizar esta pasta no Github.

###Instalando e configurando o Adapter
Para efetuar a instalação do Adapter, devemos seguir os seguintes passos:

1. Parar o serviço TfsJobAgent 

```
    net stop TfsJobAgent
```

2. Copiar as Dlls geradas no projeto WarehouseAdapterTFS201x para a pasta de plugins do TFSJobAgent (<caminho instalação do tfs>\application tier\TfsJobAgent\plugins) em cada máquina que faça parte do AppTier da sua instalação do TFS
Pelo cmd executar a aplicação SetupWarehouseAdapter.exe. Um exemplo do comando de instalação deste adapter seria (chame o executável sem nenhum parâmetro para entender mais sobre os parâmetros utilizados):

```
    .\SetupWarehouseAdapter.exe -c -t=3 -JobName=”TFS Usage Log” -SyncJobExtension=”TFS.Warehouse.Adapter.TFSUsersAdapterJobExtension” -Collection=http://vsalm:8080/tfs/DefaultCollection
```

3. Reiniciar o serviço TfsJobAgent

```
    net start TfsJobAgent
```

###No mais 
Creio que estes passos são suficientes para que você possa começar a estudar o código e perceber as possibilidades de customização do data warehouse do TFS. Em outro artigo vamos abordar um pouco mais os detalhes de implementação e algumas práticas que devem ser seguidas quando estamos trabalhando neste tipo de solução. Até logo!
