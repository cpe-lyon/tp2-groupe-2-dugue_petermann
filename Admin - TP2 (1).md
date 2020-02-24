# **TP 2 - Bash**

## **Variables d'environnement**

1. ***`printenv PATH`*** pour récupérer le fichiers où bash trouve les commandes tapées par l'utilisateur.
2. Grace à la commande env on aperçoit toutes les variables d'environnement de la machine. On voit que c'est la variable d'environnement ***HOME*** qui renvoie au répertoire personnel de l'utilisateur courant.
Avec un printenv ***HOME*** on récupère le chemin de cette variable qui est `/home/serveur`.

3. Avec la commande ***`env | grep`*** + :
* ***LANG***, on voit que ***LANG*** définit le paramètre linguistique de base utilisé par les applications du système, tant qu'il n'est pas contredit par une autre variable.
vaut : **`fr_FR.UTF-8`**
* ***PWD*** définit le chemin du répertoire de travail courant de l'interpréteur de commande.
vaut : /home/serveur
* ***OLDPWD*** contient le chemin absolu vers le répertoire courant précédent (permet de savoir d'où on vient).
N'est pas définis sur notre VM
* ***SHELL*** indique l'interpréteur utilisé par défaut. La valeur habituelle sous linux est _`/bin/bash`_
*  `_` correspond au chemin de la dernière commande tapée : exemple printenv _ renvoie *`/usr/bon/printenv`*

4. Pour créer une variable locale il faut utiliser une commande de la forme :
	***[NomdeVariable] = [Commande souhaitée]*** 
	Par exemple nous avons effectué :
    `MY_VAR= nano $[Nom de la variable]` 
suite de la commande, afin de vérifier que cette dernière existe =>
	 `$MY_VAR plop`

5. 
	La commande “**Bash**” supprime les variables locales (***MY_VAR2 =toto***) est supprimé (vérification avec la commande “`echo $MY_VAR2`”)
	lorsque l’on fait exit, les variables locales sont de nouveau disponible

6. 
	Pour transformer la variable locale en variable d'environnement, il suffit de faire la commande “ **export [Variable]**” => `export MY_VAR2`
	la commande est bien présente dans le bash ( via la commande `printenv | grep MY_VAR`)
	parce que c’est devenue une variable environnement.

7. Afin de créer une variable d'environnement contenant le noms de nos binômes nous lançons la commande : 
`NOMS=PETERMANN\ DUGUE`
Nous pouvons vérifier cette dernière création par la commande :
 `echo $NOMS`
 Qui nous renvoie bien le contenu désiré

8. Commande écrivant *"Bonjour à vous deux, binôme1 binôme2 !"* avec la variable défini précédemment **Noms**
	`echo “Bonjour à vous deux $NOMS”`

9. La différence entre unset et variable vide:
	-	unset permet de supprimer la variable ( vous ne pourrez plus l’appeler dans vos commandes)  
	-	Laisser une variable avec une valeur vide, la variable existe toujours mais ne renvoie rien.

10. écrire la phrase *$HOME = chemin* (renvoyé par la variable **HOME** :
	`echo -e \$HOME = $HOME`

### **Programmation Bash**
Pour ajouter le chemin vers notre dossier script (présent dans notre dossier personnel) à la variable ***PATH*** de manière permanente, on ajoute à la suite de ***PATH*** notre chemin puis on l'écrit dans notre *.bashrc* à la suite pour le définir définitivement, avant de relire le fichier *.bashrc* qui est normalement lu qu'au démarrage de la machine.
Pour cela on lance la commande complète qui fait les 3 commandes expliquées précédemment :
***`echo PATH=$PATH:~/script/ >> ~/.bashrc && source ~/.bashrc`***

## **Contrôle de mot de passe**

chmod permet d'autoriser les droits d’exécution d'un script.sh

condition de vérification si ce qui a été tapé par l'utilisateur par la variable mot de passe définis :
* test "..." = "..."
* [ 2 = 2 ]
* Attention en shell (bin bash) on ne met pas de if avec des = , on utilise les valeurs eq, lt etc...)
> Les opérateurs de tests disponibles sont, pour les chaînes :
> -   `c1 = c2`, vrai si c1 et c2 sont égaux ;
> -   `c1 != c2`, vrai si c1 et c2 sont différents ;
> -   `-z c`, vrai si c est une chaîne vide ;
> -   `-n c`, vrai si c n'est pas une chaîne vide.
> Pour les nombres :
> -   `n1 -eq n2`, vrai si n1 et n2 sont égaux (equal) ;
> -   `n1 -ne n2`, vrai si n1 et n2 sont différents (non equal);
> -   `n1 -lt n2`, vrai si n1 est strictement inférieur à n2 (lower than);
> -   `n1 -le n2`, vrai si n1 est inférieur ou égal à n2 (lower or equal);
> -   `n1 -gt n2`, vrai si n1 est strictement supérieur à n2 (greater than) ;
> -   `n1 -ge n2`, vrai si n1 est supérieur ou égal à n2 (greater or equal).
> Pour les expressions :
> -   `! e`, vrai si e est faux ;
> -   `e1 -a e2`, vrai si e1 et e2 sont vrais ;
> -   `e1 -o e2`, vrai si e1 ou e2 est vrai.

Le script : 

    #/bin/bash
    PASSWORD='motdepasse'
    echo -n -e "Votre mot de passe :\n"
    read -s
    mdp="$REPLY"
    if test "$PASSWORD" = "$mdp"
    then echo -n -e "Mot de passe bon\n"
    else echo -n -e "Mot de passe faux\n"
    fi
    exit 0
    #
## **Expressions rationnelles**

Script prenant en paramètre qui utilise la fonction donné pour vérifier que ce paramètre est un nombre réel (affiche un message d'erreur dans le cas contraire) :

    #!/bin/bash
    function is_number()
    {
    re='^[+-]?[0-9]+([.][0-9]+)?$'
    if ! [[ $1 =~ $re ]] ; then
	    return 1
    else
	    return 0
    fi
    }
    
    is_number $1
    if [ $? -eq 0 ] ; then
	    echo "nombre reel"
    else
	    echo "pas un nombre reel. ERREUR"
    fi
    #

## **Contrôle d'utilisateur**

Script permettant de vérifier l'existence d'un utilisateur dont le nom est donné en paramètre du script. Si ce dernier est appelé sans nom d'utilisateur, il affiche le message d'erreur : "Utilisation : nom_du_script nom_utilisateur"
Avec nom_du_script le nom que recherche automatiquement le script, ainsi se modifie tout seul si on renomme le script.

    #/bin/bash
    if [ $# -ne 1 ]; then
	    encho -n -e “Merci de mettre un utilisateur à la suite de la commande\n”
	    exit
    fi
    toto=$(cat /etc/passwd | cut -d: -f1 |grep $1)
    if [ “$1” = “$toto”]; then
	    echo -e “utilisateur $1 est présent dans le fichier /etc/passwd \n”
    else
	    echo -e “L’utilisateur $1 ne se trouve pas dans le fichier /etc/passwd \n”
    fi
    #

# **Factorielle**

Script permettant le calcul factorielle d'un entier naturel passé en paramètre (en supposant que l'utilisateur saisit toujours un entier naturel) :

    #/bin/bash
    fact ()
    {
    n=$1
    if [ $n -eq 0 ]
    then
	    echo 1
    else
	    echo $((n *`fact $((n-1))`))
    fi
    }
    echo `fact $1`
    #

## **Le juste prix**
Script qui génère un nombre aléatoire entre 1 et 1000. Puis demande à l'utilisateur de devinez ce dernier avec les indices "C'est plus !" , "C'est moins !" ou encore "Gagné !" quand il finit par trouver.

    #/bin/bash
    NBalea=$RANDOM
    let "NBalea%=1001"
    echo $NBalea
    echo -e "Devinez le nombre choisi :"
    read i
    while : ; do
    	if test $i -lt $NBalea 
    	then echo -e "C'est plus !\n"
    	elif test $i -gt $NBalea
    	then exho -e "C'est moins !\n"
    	else echo -e "Gagné !\n" ; break
    	fi
    	read i
    done
    exit 0
    #
## **Statistiques**

Script prennant en paramètres 3 entiers et affiche le min le max et la moyenne.

    #/bin/bash
    if [ “$# -ne 3 ]; then
	    echo -e “Merci de mettre 3 nombres séparés par des espaces \n”
	    exit
    fi
    for i in $1 $2 $3
    do
	    if [  $i -le -100 -o $i -ge 100 ]; then
		    echo -e -n “Merci de mettre des nombres entre \”-100\” et \”100\” pour l’argument $i \n”
		    exit
	    fi
    done
    #Moyenne
    Moy=$((($1+$2+$3)/3))
    echo -e “La moyenne est de $Moy \n”
    #Minimum
    if [ $1 -le $2 -a $1 -le $3 ]; then
	    echo -e “La valeur minimale est $1 \n“
	    if [ $2 -le $1 -a $2 -le $3 ]; then
		    echo -e “La valeur minimale est $2 \n“
	    else
		    echo -e “La valeur minimale est $1 \n“
	    fi
	fi
    #Maximum
    if [ $1 -ge $2 -a $1 -ge $3 ]; then
	    echo -e “La valeur maximale est $1 \n“
	    if [ $2 -ge $1 -a $2 -ge $3 ]; then
	    echo -e “La valeur maximale est $2 \n“
	    else
	    echo -e “La valeur maximale est $1 \n“
	    fi
	fi
    #

## **Les combinaisons possibles de couleurs**

Afficher un tableau des combinaisons de couleur limité au borne 40 à 47 pour les couleurs de background et 0 à 7 plus 30 à 37 pour le style de la police.

> * 0 : normal
> * 1 : gras
> * 4 : souligné
> * 5 : italique
> * 7 : surligné

    #/bin/bash
    echo -e 'FG\BG \t 40 \t 41 \t 42 \t 43 \t 44 \t 45 \t 46 \t 47'
    for attr in 0 1 4 5 7 30 31 32 33 34 35 36 37; do
            printf $attr
            for back in 40 41 42 43 44 45 46 47; do
                    printf '\t\033[%s;%smBash\033[0m' $attr $back
            done
            printf '\n'
    done
    exit 0
    #

