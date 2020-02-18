# data

### Git : cloner un projet, travailler à plusieurs et créer des branches
#### synchroniser les différentes copies
##### git clone pour dupliquer un dépôt

```js

La commande "git clone </path/to/existing/git/repository> </path/where/it/should/be/cloned>"
permet de cloner un dépôt existant (en local ou à travers un réseau si le chemin est une URL) 
à un autre endroit. La métaphore du clonage doit être comprise ici comme une copie exacte :
après "git clone" les deux dépôts sont les images parfaites l'une de l'autre : ils possèdent
le même historique, et il n'y en a pas un qui est plus légitime que l'autre (en fait,
on verra dans la section sur "git pull" que suite au clonage la branche master du clone fait
référence à la branche master du cloné). Si plusieurs personnes travaillent sur un projet,
elles peuvent ainsi en obtenir chacune une copie.
Dans un premier temps, nous allons synchroniser les dépôts, puis nous verrons dans un second
temps comment détecter et gérer les conflits qui peuvent survenir lors de modifications 
concurrentes. Nous supposerons que le projet initial était dans le répertoire repoA, et qu'on
le clone dans un autre répertoire repoB situé n'importe où sauf sous l'arborescence de repoA 
évidemment.
Pour simplifier nous prendrons repoA et repoB au même niveau,
même si en pratique ça n'a pas grand intérêt.
```
      
      
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

      Pour synchroniser son dépôt avec celui que nous avions cloné (ou un autre),
      il faut faire un appel explicite à "git pull". 
      Ce dernier ne se fait pas automatiquement (heureusement, imaginez un peu la pagaille).
      Ci-dessous, la ligne 19 lorsque l'on regarde le contenu du fichier firstFile.txt après 
      le "git pull" montre que la modification que nous avions faite dans repoA a bien 
      été répercutée dans repoB.
      
      
      
      Remarquez que dans repoB, nous faisons simplement "git pull" sans lui dire explicitement 
      qu'il faut aller chercher dans repoA. En fait, le "git clone repoA repoB", git a bien 
      indiqué dans repoB que ce dernier avait été généré à partir de repoA en le désignant comme
      "origin" (comparez donc les fichiers repoA/.git/config et repoB/.git/config). Consultez la 
      documentation sur les dépôts distants pour plus de précisions.
      
   
 ```js

$ git pull 
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From /home/olivier/tmp/jnk/repoA
 c939f1f..b88eea6 master -> origin/master
Updating c939f1f..b88eea6
Fast-forward
 firstFile.txt | 1 +
 1 file changed, 1 insertion(+)
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
````
 
 
                  - Pour récapituler :

                  Le dépôt de référence fait donc un cycle classique :
                  • modifications,
                  • git add,
                  • git commit.
                  • Le dépôt clone se maintient à jour en faisant "git pull".
                  
              
# git push pour propager sur le dépôt d'origine les modifications que vous avez faites en local


                  Alors que dans la section précédente les modifications se faisaient toujours
                  dans le même sens, nous allons maintenant voir que git vous permet de propager
                  sur le dépôt d'origine des modifications que vous avez faites sur son clone.
                  Puisque les modifications se font en sens inverse de celles du "git pull",
                  il faut évidemment utiliser "git push" depuis repoB.
                  Afin d'éviter les conflits potentiels lorsque plusieurs utilisateurs font des
                  modifications sur leur clone, nous allons créer une branche spécifique dans 
                  laquelle nous allons faire nos modifications, puis nous allons envoyer cette 
                  branche dans repoA avec un "git push", et enfin fusionner cette branche avec
                  la branche en cours de repoA avec un "git merge". 
                  La section suivante revient plus en détail sur les branches.
                  

                  Dans l'exemple suivant, nous nous plaçons dans repoB, nous créons une nouvelle
                  branche myBranchFromB (ligne 6) dans laquelle nous faisons des modifications sur 
                  le fichier firstFile.txt (lignes 8 à 10). Remarquez que lors du "git commit" 
                  lignes 11 puis 14, ces opérations se font sur la nouvelle branche myBranchFromB.
                  Enfin, lors du "git push" les lignes 22 et 23 montrent que la nouvelle branche 
                  est transmise à repoA.
 
 
 
 ```js
 $ git pull
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
$ git checkout -b myBranchFromB
Switched to a new branch 'myBranchFromB'
$ echo "modifications from repoB" >> firstFile.txt
$ git add firstFile.txt
$ git commit -m "improvements from repoB"
[myBranchFromB 098fc69] improvements from repoB
 1 file changed, 1 insertion(+)
$ git status
On branch myBranchFromB
nothing to commit, working directory clean
$ git push origin myBranchFromB
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 328 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
To /home/olivier/tmp/jnk/repoA/
 * [new branch] myBranchFromB -> myBranchFromB
 ````
 
                  En revenant à repoA, nous pouvons vérifier que tant que l'on est sur la branche master,
                  les modifications ne sont pas prises en compte (lignes 3 et 6 à 12), mais qu'elles sont
                  bien dans la branche myBranchFromB que nous venons d'envoyer avec le "git push" 
                  (lignes 14, 16 et 19 à 26). Nous revenons donc à la branche master (ligne 27) pour 
                  incorporer les changements de la branche myBranchFromB avec un "git merge" (ligne 29).
                  
               
 ```js
 $ cd ../repoA
$ git status
On branch master
nothing to commit, working directory clean
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
$ git checkout myBranchFromB
Switched to branch 'myBranchFromB'
$ git status
On branch myBranchFromB
nothing to commit, working directory clean
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
modifications from repoB
$ git checkout master
Switched to branch 'master'
$ git merge myBranchFromB
Updating 842cc68..09805d8
Fast-forward
 firstFile.txt | 1 +
 1 file changed, 1 insertion(+)
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
modifications from repoB
````

                À ce point, nous avons effectué des modifications dans la branche myBranchFromB de repoB,
                envoyé cette branche dans repoA et nous l'y avons fusionné avec la branche principale 
                (appelée master) de repoA. Néanmoins, la branche master de repoB n'a pas encore été mise à jour.
                Il nous faut donc enfin faire un "git pull" depuis repoB.
                
```js
$ cd ../repoB
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
$ git pull
From /home/olivier/tmp/jnk/repoA
 842cc68..09805d8 master -> origin/master
Updating 842cc68..09805d8
Fast-forward
 firstFile.txt | 1 +
 1 file changed, 1 insertion(+)
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
modifications from repoB
````
               Pour récapituler :

                 - depuis le dépôt clone
                  
                  •n'oubliez pas de commencer par un "git pull" pour être certain de partir
                  de la dernière version de référence
                  •créez une nouvelle branche avec git checkout -b nomNouvelleBranche
                  •faites un ou plusieurs cycles classiques
                  modifications,
                        •git add
                        •git commit
                  •faites un "git push" (comme nous l'avons vu précédemment, il est possible
                  de faire plusieurs cycles de "commit" atomiques puis un "git push" lorsque l'on est satisfait)
                  
                - depuis le dépôt origine
                
                  •assurez-vous d'être sur la bonne branche (par exemple master) avec
                  un "git checkout nomBonneBranche"
                  •faites un "git merge nomNouvelleBranche" de la branche que vous venez
                  de pousser depuis le dépôt clone
                  •résolvez les conflits éventuels (cf. section suivante)
                  •faites un "git branch -d nomNouvelleBranche" pour supprimer la  branche 
                  qui est devenue inutile maintenant que vous l'avez intégrée
                  
                - depuis le dépôt clone
                
                  • repassez dans la bonne branche (par exemple master)
                  • faites un "git pull" pour récupérer la dernière version à jour avec les modifications que 
                  vous venez de pousser, les modifications éventuelles d'autres utilisateurs et la résolution 
                  des conflits éventuels.
                  •faites un "git branch -d nomNouvelleBranche" sur la nouvelle branche pour la supprimer également.
                  
           
### Détection et résolution de conflits en cas de modifications concurrentes sur deux instances

            Évidemment, la situation que nous venons de voir peut se révéler problématique si pendant que vous
            êtes occupé à faire vos modifications entre votre "git pull" et votre "git push" quelqu'un fait 
            d'autres modifications sur le dépôt d'origine (ou fait un autre "git push" avant le vôtre).

            Une fois de plus, git est là pour vous sauver la vie :

            • si les modifications concurrentes portent sur des fichiers différents ou même des endroits 
            différents d'un même fichier, git se débrouille pour tout intégrer (alors qu'avec de simples 
            copies de fichiers, les modifications du dernier écraseraient les modifications antérieures)
            
            • si les modifications concurrentes portent sur les mêmes endroits dans un fichier,
                  - git vous signale qu'il y a un conflit (et vous dit où)
                  - git vous prépare la zone en délimitant les deux modifications concurrentes-
                  - git vous laisse gérer la résolution
                  
                  
            Là encore, notez bien l'intérêt de ne travailler sur des changements les plus petits possibles.
            Il est bien plus facile de fusionner deux branches ayant peu de modifications que deux branches
            ayant fortement divergé et comportant des différences aux mêmes endroits.


```js
$ cd repoB
$ git pull
Already up-to-date.
$ git checkout -b myBranchFromB
Switched to a new branch 'myBranchFromB'
$ echo "conflict from repoB" >> firstFile.txt
$ git add firstFile.txt
$ git commit -m "improvement from repoB likely to cause conflict"
[myBranchFromB db36634] improvement from repoB likely to cause conflict
 1 file changed, 1 insertion(+)
$ cd ../repoA
$ echo "conflict from repoA" >> firstFile.txt
$ git add firstFile.txt
$ git commit -m "improvement from repoA likely to cause conflict"
[master 348ff6b] improvement from repoA likely to cause conflict
 1 file changed, 1 insertion(+)
$ cd ../repoB
$ git push origin myBranchFromB
Counting objects: 26, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (26/26), 2.12 KiB | 0 bytes/s, done.
Total 26 (delta 11), reused 0 (delta 0)
To /home/olivier/tmp/jnk/repoA/
 * [new branch] myBranchFromB -> myBranchFromB
 ````

            À ce point, nous avons ajouté une ligne dans le fichier firstFile.txt dans repoB
            et une autre ligne au même endroit du fichier firstFile.txt dans repoA. Nous procèdons
            ensuite comme à la section précédente pour envoyer sur repoA la branche myBranchFromB de repoB.

            Il ne reste plus qu'à fusionner la branche myBranchFromB avec la branche 
            principale de repoA (et les ennuis commencent).
            
 ```js
 $ cd ../repoA
$ git merge myBranchFromB
Auto-merging firstFile.txt
CONFLICT (content): Merge conflict in firstFile.txt
Automatic merge failed; fix conflicts and then commit the result.
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
modifications from repoB
<<<<<<< HEAD
conflict from repoA
=======
conflict from repoB
>>>>>>> myBranchFromB
````

            Lors du "git merge", git détecte bien le conflit (ligne 4) et nous dit dans quel fichier 
            cela s'est produit. Dans le fichier, git a ajouté les deux portions qui posent problème 
            en les délimitant par des lignes de chevrons et en les séparant par une ligne de '=' (lignes 15 à 19).
            C'est alors à l'utilisateur d'éditer la zone à la main et de supprimer les délimitations.
            
```js
$ vi firstFile.txt
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
modifications from repoB
Manually-resolved conflict from repoA and repoB
$ git add firstFile.txt
$ git commit -m "Fixed conflicts from repoA and repoB"
[master ab5a47f] Fixed conflicts from repoA and repoB
$ git status
On branch master
nothing to commit, working directory clean
$ git branch -d myBranchFromB
Deleted branch myBranchFromB (was db36634).
````
            Enfin, il ne reste plus qu'à mettre à jour repoB.
            
            
           
         
```js


$ cd ../repoB
$ git checkout master
Switched to branch 'master'
Your branch is behind 'origin/master' by 3 commits, and can be fast-forwarded.
 (use "git pull" to update your local branch)
$ git pull
Updating 09805d8..ab5a47f
Fast-forward
 firstFile.txt | 1 +
 1 file changed, 1 insertion(+)
$ cat firstFile.txt
This is the first line of my first file...
... and this is the second line
Now we add another line
 
Adding a fourth line
Adding a fifth line
modification from repoA
modifications from repoB
Manually-resolved conflict from repoA and repoB
$ git branch
* master
 myBranchFromB
$ git branch -d myBranchFromB
Deleted branch myBranchFromB (was db36634)
````

