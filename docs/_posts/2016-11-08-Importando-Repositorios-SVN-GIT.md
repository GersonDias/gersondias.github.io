---
layout: post
title: 'Importando repositórios SVN para GIT'
excerpt: "Utilizando a ferramenta git svn para importar um repositório SVN para um repositório GIT com histórico"
tags: [TFS, GIT, Source Control]
comments: true
---
Após o Git ter se tornado uma das principais ferramentas para o versionamento de código fonte, muitas equipes estão procurando migrar para o git de outros sistemas de controle de versão, porém, lidar com todo o histórico de alterações, tags e branchs pode tornar-se uma dor de cabeça.

Hoje vamos falar sobre a migração de repositórios SVN para GIT.

*Este post foi inspirado em um trabalho que realizei recentemente, migrando um fonte com mais de 300Mb em SVN para o GIT. Este trabalho foi feito utilizando uma máquina rodando Windows 10, e, por vezes, utilizei o [Bash for Windows](https://msdn.microsoft.com/en-us/commandline/wsl/about). Por conta disso, você verá comandos que serão feitos utilizando o **bash** e outros feitos utilizando o **powershell***

O git possuí uma ferramenta para a importação de repositórios SVN, incluindo todo o histórico de alterações no código fonte. Se você estiver utilizando uma máquina Windows, não é necessário fazer instalações adicionais para utilizar a ferramenta git svn via linha de comando, porém, em uma máquina utilizando Ubuntu (ou utilizando o bash dentro do Windows) talvez seja necessário instalar alguns pacotes. Faça estas instalações com o comando 

```bash
sudo apt-get install git-core git-svn
```

Em meus testes, a utilização do *bash* provou-se mais rápida que o mesmo comando utilizando o powershell. Portanto, recomendo que continue no bash enquanto executa o comando de clone do repositório:

```bash
SECONDS=0 && git svn clone <caminho para o repositório> <Pasta onde será feito o clone> --stdlayout -r2000:HEAD && echo $SECONDS`
```

*O parâmetro r2000:HEAD irá limitar o histórico para a revisão 2000 até a atual. Este comando, em um repositório com 3856 commits levou cerca de 1h10m.*

No SVN, [uma tag é uma branch](http://svnbook.red-bean.com/en/1.7/svn.branchmerge.tags.html), portanto, após o clone do repositório, quando rodamos o comando ```git tag -l``` não iremos ver nenhuma TAG, porém, se rodarmos o comando ```git branch -r``` vamos observar que as branchs remotas (que são as tags do SVN) estarão lá. Para criar tags do git, de acordo com as tags do SVN, rode o seguinte comando na pasta de seu repositório (powershell): 

```powershell
git for-each-ref --format="%(refname:short) %(objectname)" refs/remotes/tags | %{$tag = $_ -split "/" -split " "; git tag -a -m 'Import tag from svn' $($tag[1]) $($tag[2])}
```
*Aqui estamos iterando em cada branch remota do nosso repositório e criando uma tag apontando para o commit que esta "branch de tag" esta apontando.*

Após isso, precisamos criar branchs locais do git que referencie as reais branchs do nosso repositório SVN. O processo para fazer isso é um pouco mais complexo:

```powershell
$todasBranchs = git for-each-ref refs/remotes;
$branchsTags = git for-each-ref refs/remotes/origin refs/remotes/tags;
$branchs = New-Object System.Collections.ArrayList;
foreach ($b in $todasBranchs) { if ($branchsTags.contains($b) -eq $false) {$branchs.add($b)}};
$branchs | %{$b = $_ -split '/'; git checkout -b $($b[2]) refs/$($b[1])/$($b[2]); git checkout master}
```

Após este comando, poderemos observar que os comandos git tag -l e git branch -v irão se comportar da forma esperada. Estes comandos irão transformar todas as branchs atuais do SVN em branchs no seu repositório git. Caso não seja isso que queira fazer, execute o comando `git checkout <nome da branch> refs/remotes/<nome da branch>` somente para as branchs que você quer manter em seu repositório git. Lembre-se sempre das boas práticas para repositórios git e não mantenha branchs que não são necessárias ou estão a muito tempo sem atualizações.

# Não se esqueça do arquivo .gitignore
Lembre-se que o git é uma ferramenta de controle de versão que, por sua natureza, não lida bem com arquivos grandes e/ou arquivos binários, portanto, não deixe de incluir o arquivo [.gitignore mais apropriado para o seu código fonte](https://github.com/github/gitignore). Isso será assunto para outro post, porém, para garantir que o arquivo .gitignore esta fazendo o seu trabalho corretamente, execute os seguintes comandos:

```powershell
git rm -r --cached .
git add .
git commit -m "Aplicando o arquivo .gitignore"
```

Com o repositório em ordem, você poderá fazer o push do seu repositório para o repositório remoto:

```powershell
git remote add origin <url do repositório remoto>
git push origin --all; git push origin --tags #por padrão o comando git push não empurra as tags para o repositório remoto.
```