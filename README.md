Dans le fichier C:\Users\\ <UserName>\.gitconfig se trouvent vos **alias**, des “raccourcis” persos pour des commandes (souvent) longues. En dessous de `[alias]`, rentrez les commandes qui vous intéressent / ajoutez-en ! :
Note : quelque soit votre OS, vous pouvez accédez à votre fichier de config via la commande : ```git config --global --edit```

* **c** = ```commit```
* **ca** = ```commit -a```
* **changes** = ```diff master HEAD```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;List all the differences between your master branch and your last comit
* **last** = ```diff HEAD~1 HEAD```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;What does last commit of the current branch changes (/!\ it doesn’t show the unstaged / uncommited changes)
* **tbc** = ```diff --cached```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Show the changes **T**o **B**e **C**ommited 
* **sur** = ```submodule update --recursive```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Update all the modules and their submodules
* **suri** = ```submodule update --recursive --init```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Update all the modules and their submodules and clone new ones if needed
* **surid** = ```"!git submodule update --recursive --init  && git clean -dff"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Update all the modules and their submodules, clone the new ones if needed, delete the directories of the modules that aren’t tracked anymore
* **fa** = ```fetch --all```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fetch all the remotes!
* **ck** = ```checkout```
* **purge** = ```"!git branch --merged | grep  -v '\\*\\|master\\|develop' | xargs -n 1 git branch -d"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Delete all the local branches that are merged with the master. It won’t delete branches starting with master or develop (or any other stuff you want to add)
* **ckm** = ```"!git checkout master && git pull mainRepo master && git push origin master"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Switch to local master branch, update it, push it on your fork’s master (I always keep my master clean and up to date, this might not be the case for you)
* **rhard** = ```reset --hard```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Carefull with this ! (if you *do* screw up, don’t panic, *git reflog* is there for you https://git-scm.com/docs/git-reflog)
* **dtag** = ```describe --tag```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Prints the most recent tag your branch is based on
* **cpick** = ```cherry-pick```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Multi-branch work isn’t overrated anymore !
* **tkt** = ```rebase --continue```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Continue rebase once all conflits are resolved
* **restore** = ```!git checkout $(git rev-list -n 1 HEAD -- "$1")^ -- "$1"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Restore current (local) branch to set SHA1
* **test** = ```"!git branch -D test && git checkout -b test && git pull origin $1"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Useful for PR validation, starts a fresh new “test” branch from the current branch and pulls the branch you wish to test like *git test ady_supa_dupa_feature*
* **pullc** = ```"!f() { OUTPUT="$(git rev-parse --abbrev-ref HEAD)"; git pull origin $OUTPUT; }; f"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;To pull the branch you’re using from the remote ‘origin’ (for example if you’re on the branch ‘test’ then ‘git pullc’ does ‘git pull origin test’)
* **pushc** = ```"!f() { OUTPUT="$(git rev-parse --abbrev-ref HEAD)"; git push origin $OUTPUT; }; f"```  
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;To push the branch you’re using to the remote ‘origin’ (for example if you’re on the branch ‘test’ then ‘git pushc’ does ‘git push origin test’)  
# Advanced
**/!\ This is advanced git usage and it can lead to data loss. Use it at your own risks ! /!\\**
### **1. Rebase like a pro : Chopper des nouveaux commits apparu sur la master pendant le développement, sans commit de merge / pull**
**Pros :**
+ Pas de commit de merge
+ Pas d’arborescence WTF dans git log / gitk
+ Ce qu’on ajoute est clairement visible *au-dessus* de la base initiale
**Cons :**
- Si quelqu’un a commencé une branche depuis un des commits rebasé, il va vous haïr. 
Règle du rebase : **on ne rebase jamais quelque chose qui a été poussé sur un repo**. Period.

*Example :*

* **git checkout mainRepo/master**
* **git checkout -b ma_super_feature** # on part du master pour créer une nouvelle feat
* **git commit -a -m ‘Bla bla mon commit est trop cool’**
* [...] Des commits arrivent sur la branche master et vous développez sur votre branche, 
* [...] il peut donc être utile de récupérer les changements faits sur la master de temps en temps
* **git rebase mainrepo/master** # on va repartir de la master du common et ensuite réappliquer un à un tous les commits en cours sur la branche
### **2. Rebase like a pro : Virer / modifier / grouper / reword des commits sur une branche**
**Pros :**
+ On peut régler tous les problèmes de commits, comme par exemple fusionner deux commits, renommer un commit recouvert, changer l’ordre des commits pour en fusionner deux similaires, fixer un commit comme un ninja, etc...
**Cons :**
- Si quelqu’un a commencé une branche depuis un des commits rebasé, il va vous haïr. 
- Ca peut être déroutant lorsqu’il y a beaucoup de commit
- Si la branche a plusieurs parents ça fout mal au crâne et c’est potentiellement une mauvaise idée
- On peut perdre des commits
Règle du rebase : **on ne rebase jamais quelque chose qui a été poussé sur un repo**. Period. 
La commande c’est :
**git rebase -i TARGET**

**Comment savoir quelle target utiliser ?**

On peut utiliser les SHA1 du commit "d'arrivée", mais sinon on peut utiliser les "chemins relatifs" entre les commits Git. Example : 

```
G   H   I   J
 \ /     \ /
  D   E   F
   \  |  / \
    \ | /   |
     \|/    |
      B     C
       \   /
        \ /
         A

A =      = A^0
B = A^   = A^1     = A~1
C = A^2  = A^2
D = A^^  = A^1^1   = A~2
E = B^2  = A^^2
F = B^3  = A^^3
G = A^^^ = A^1^1^1 = A~3
H = D^2  = B^^2    = A^^^2  = A~2^2
I = F^   = B^3^    = A^^3^
J = F^2  = B^3^2   = A^^3^2
```
