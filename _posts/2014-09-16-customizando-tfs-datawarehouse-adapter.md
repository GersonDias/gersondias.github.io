---
layout: post
title: "Customizando o TFS Datarehouse Adapter"
excerpt: "Exemplo de customização do TFS Datawarehouse (TFS Datawarehouse Custom Adapter)."
tags: [TFS, Datawarehouse, Customização TFS]
comments: true
---

Um fato em qualquer empresa é a necessidade de informações. Todos os níveis hierárquicos podem se beneficiar das informações certas, providas no momento certo e a venda de uma ferramenta como o Team Foundation Server muitas vezes se pauta nesta necessidade: “E se vocês soubessem exatamente o requisito que deu origem a determinada linha de código?”, “E se você tivesse uma ferramenta que te provesse dados sobre o teu ambiente de desenvolvimento de software onde você pode combiná-los para conseguir informações diversas?”. Estas são frases que certamente as empresas que optaram pelo TFS já ouviram durante o processo de implantação da ferramenta. Porém, e no caso onde preciso cruzar dados provindos de outros sistemas com os dados do TFS para me dar uma visão completa de meu ambiente? E no caso onde preciso misturar dados de uma maneira diferente da que o TFS me entrega inicialmente? Nestas situações uma solução a ser avaliada é a customização do TFS Data Warehouse. 
<br/><br/>
O TFS Data Warehouse basicamente abrange uma base de dados relacional e uma base de dados analítica que agregam dados de todas os seus Team Project Collections e proveêm os dados para todos os relatórios que temos. Estas bases de dados podem ser atualizadas via plugins (ou Adapters) que rodam sob o processo TfsJobAgent do TFS. Você pode ter maiores detalhes desta arquitetura no MSDN e, se você for um bom administrador de TFS Server, não deixe de checar periodicamente o status destes adapters com a ajuda do webservice de administração do data warehouse:
<br/>
```
http://localhost:8080/tfs/TeamFoundation/Administration/v3.0/WarehouseControlService.asmx
```
<br/>
e sempre de um Oi para seu TFS
<br/>
```
http://your-server:8080/tfs/_oi/
```

### O problema que queremos resolver 
Agora, vamos ao problema que queremos resolver para este exemplo: um cliente relata ter problemas para gerenciar corretamente as licenças do Visual Studio, pois alguns usuários instalaram versões do Visual Studio diferentes da que eles deveriam em suas máquinas. Além disto, perguntas como “Quem está utilizando a versão Ultimate? De qual Visual Studio?” “Quantos estão utilizando a Premium?” eram perguntas costumeiras. Sabendo que em algum momento do fluxo de trabalho as pessoas teriam que acessar o TFS, uma das soluções mais rápidas para responder a esta pergunta seria verificar na tabela tbl_command da Collection utilizada quais eram os usuários e qual User Agent que ele estava utilizando para se conectar ao TFS. Porém, o problema é que, por padrão, os dados desta tabela são expurgados a cada 14 dias e aumentar este tempo, geralmente, não é uma boa idéia devido ao número de registros que esta tabela carrega, especialmente em um ambiente com mais de 300 usuários ativos por dia no TFS. Assim, a solução adotada foi: fazer um Adapter do Datawarehouse do TFS que verifica de tempos em tempos quais usuários fizeram alguma consulta ao TFS, bem como guardar o software que ele utilizou para fazer esta consulta; os dados desta tabela não podem ser expurgados e nem sofrer perdas de dados em caso de um rebuild do Datawarehouse. Por conta desta última característica, decidiu-se criar uma nova base de dados no mesmo servidor SQL do TFS e esta base que seria a responsável por carregar a tabela que teria os dados dos usuários que acessaram o TFS ao longo do tempo. Para este exemplo, não será necessária customizações no cubo gerado pelo Analysis Server, apenas mudanças de bases de dados relacionais. Ao final, o relatório será gerado e disponibilizado pelo Excel, utilizando a ferramenta PowerPivot

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
<br/>
_Alguns pontos importantes sobre a estruturação da solution:_

* Apesar de particularmente gostar de versionar estas Dlls externas junto com o projeto, estas Dlls são proprietárias e não podem ser distribuidas publicamente. Por este motivo, você não vai encontrar um pacote Nuget com estas dependencias, e algumas delas não podem ser encontradas nem mesmo instalando o TFS 2013 Object Model. Assim, existem duas formas de obter estas Dlls: Instalar o TFS Express em sua máquina de desenvolvimento ou obter estes binários de um servidor do TFS.
* O projeto 2012 e 2013 tem exatamente o mesmo código. A única diferença entre as duas são as versões das Dlls do TFS que são referenciadas. Portanto, resolvi usar um pequeno truque dentro da solution: Criei uma “solution folder” chamada Shared e dentro dela coloquei os arquivos .cs. Dentro de cada projeto, adiciono estes arquivos como um “link” para o arquivo físico. Assim, não preciso me preocupar em fazer alguma mudança na versão 2012 que tenha que ser refeita na versão 2013, ao mesmo tempo que cada projeto em sua pasta bin terá os compilados da mesma maneira e as referências para as versões corretas das Dlls Microsoft.TeamFoundation.*. Este tipo de solução é comum quando temos que trabalhar com diversas versões de Dlls e o mesmo código fonte, tanto que os novos Shared Projects funcionam basicamente da mesma forma, porém deixando tudo “visualmente mais elegante”.

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
Pelo cmd executar a aplicação SetupWarehouseAdapter.exe e verificar como ela é utilizada. Um exemplo do comando de instalação deste adapter seria (chame o executável sem nenhum parâmetro para entender mais sobre os parâmetros utilizados):

```
    .\SetupWarehouseAdapter.exe -c -t=3 -JobName=”TFS Usage Log” -SyncJobExtension=”TFS.Warehouse.Adapter.TFSUsersAdapterJobExtension” -Collection=http://vsalm:8080/tfs/DefaultCollection
```

3. Reiniciar o serviço TfsJobAgent

```
    net start TfsJobAgent
```

###No mais 
Creio que estes passos são suficientes para que você possa começar a estudar o código e perceber as possibilidades de customização do data warehouse do TFS. Em outro artigo vamos abordar um pouco mais os detalhes de implementação e algumas práticas que devem ser seguidas quando estamos trabalhando neste tipo de solução. Até logo!
