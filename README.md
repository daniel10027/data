# data

Git : cloner un projet, travailler à plusieurs et créer des branches

      Cloner un projet (par ex. pour travailler à plusieurs ou pour sauvegarder)...
      et synchroniser les différentes copies
      git clone pour dupliquer un dépôt
      La commande "git clone </path/to/existing/git/repository> 
      </path/where/it/should/be/cloned>" permet de cloner un dépôt existant 
      (en local ou à travers un réseau si le chemin est une URL) à un autre endroit.
      La métaphore du clonage doit être comprise ici comme une copie exacte : 
      après "git clone" les deux dépôts sont les images parfaites l'une de l'autre :
      ils possèdent le même historique, 
      et il n'y en a pas un qui est plus légitime que l'autre (en fait, on verra dans 
      la section sur "git pull" que suite au clonage la branche master du clone fait 
      référence à la branche master du cloné). Si plusieurs personnes travaillent sur 
      un projet, elles peuvent ainsi en obtenir chacune une copie.
      Dans un premier temps, nous allons synchroniser les dépôts, 
      puis nous verrons dans un second temps comment détecter et gérer les conflits 
      qui peuvent survenir lors de modifications concurrentes. Nous supposerons que le 
      projet initial était dans le répertoire repoA, et qu'on le clone dans un autre 
      répertoire repoB situé n'importe où sauf sous l'arborescence de repoA évidemment. 
      Pour simplifier nous prendrons repoA et repoB au même niveau, 
      même si en pratique ça n'a pas grand intérêt.
      
```js
$ git status
On branch master
nothing to commit, working directory clean
$ cd ..
$ mkdir repoB
$ git clone repoA repoB
Cloning into 'repoB'...
done.
$ ls -a repoA
. .. doc firstFile.txt firstFile.txt.bak .git .gitignore licence.txt log README.txt
$ ls -a repoB
. .. doc firstFile.txt .git .gitignore licence.txt README.txt
````

      Au passage, vous remarquerez que le fichier .gitignore 
      (c'est le même fichier qu'au premier article) dans repoA a bien été pris en 
      compte et que le fichier firstFile.txt.bak ainsi que le répertoire log ont 
      bien été ignorés lors du clonage dans repoB.
      git pull pour maintenir à jour une copie locale d'un dépôt de référence
      Nous allons commencer par une configuration simple où repoA est le dépôt de
      référence d'un projet où se font toutes les modifications, et vous souhaitez 
      uniquement maintenir à jour votre copie locale repoB sans que vous y apportiez
      la moindre modification.

      Maintenant que le dépôt d'origine repoA a été cloné, nous allons commencer par
      y faire des modifications puis un nouveau commit.
      
      
 ```js
 $ cd repoA
$ git status
On branch master
nothing to commit, working directory clean
$ echo "modification from repoA" >> firstFile.txt
$ git add firstFile.txt
$ git commit -m "some improvement from repoA"
$ git status
On branch master
nothing to commit, working directory clean
````

      Maintenant, un "git status" dans repoB montre que celui-ci est à jour 
      (par rapport à lui-même), mais que les modifications que nous venons de faire dans 
      repoA n'ont pas été reportées dans repoB.

```js
$ cd ../repoB
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
$ diff ../repoA/firstFile.txt ./firstFile.txt
8d7
< modification from repoA
````

      Pour synchroniser son dépôt avec celui que nous avions cloné (ou un autre), il faut faire un appel explicite à "git pull". Ce dernier ne se fait pas automatiquement (heureusement, imaginez un peu la pagaille). Ci-dessous, la ligne 19 lorsque l'on regarde le contenu du fichier firstFile.txt après le "git pull" montre que la modification que nous avions faite dans repoA a bien été répercutée dans repoB.

      Remarquez que dans repoB, nous faisons simplement "git pull" sans lui dire explicitement qu'il faut aller chercher dans repoA. En fait, le "git clone repoA repoB", git a bien indiqué dans repoB que ce dernier avait été généré à partir de repoA en le désignant comme "origin" (comparez donc les fichiers repoA/.git/config et repoB/.git/config). Consultez la documentation sur les dépôts distants pour plus de précisions.
