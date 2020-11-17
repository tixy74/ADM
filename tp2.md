# TP2 - Bash

## Exercice 1 - Variables d'env

### 1. Dans quels dossiers bash trouve-t-il les commandes tapées par l’utilisateur ?

**printenv PATH**   
*/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin*  

###  2. Quelle variable d’environnement permet à la commande cd tapée sans argument de vous ramener dans votre répertoire personnel ?

**printenv**  
**printenv HOME**  

### 3. Explicitez le rôle des variables LANG, PWD, OLDPWD, SHELL et _.

LANG détermine la langue que les logiciels utilisent pour communiquer avec l'user  
PWD permet de savoir le repertoire courant  
OLDPWD permet de savoir l'ancien localisation (cd -)  
SHELL chemin de l'interpreteur bash pour executer les scripts simplement (./script.sh)  
_ chemin vers l'exe de printenv  


### 4. Créez une variable locale MY_VAR (le contenu n’a pas d’importance). Vérifiez que la variable existe.

**MY_VAR=13 ; printenv MY_VAR**   
**echo $MY_VAR**   
*13*   

### 5. Tapez ensuite la commande bash. Que fait-elle ? La variable MY_VAR existe-t-elle ? Expliquez. A la fin de cette question, tapez la commande exit pour revenir dans votre session initiale.

La commande **bash** lance un nouveau sous-processus bash qui se base sur le fichier ~/.bashrc d'où le fait qu'une variable locale ne soit pas pris en compte.   
**bash -v** (mode verbose) permet de voir le script que lance bash   
exit permet de fermer le sous-processus et de retourner au processus de base   

### 6. Transformez MY_VAR en une variable d’environnement et recommencez la question précédente. Expliquez

**export MY_VAR=toto ; printenv MY_VAR**   
*toto*   
**bash**   
**echo $MY_VAR**   
*toto*    


### 7. Créer la variable d’environnement NOMS ayant pour contenu vos noms de binômes séparés par un espace. Afficher la valeur de NOMS pour vérifier que l’affectation est correcte

export NOMS="THOUVENIN" ; printenv NOMS   

### 8. Ecrivez une commande qui affiche ”Bonjour à vous deux, binôme1 binôme2 !” (où binôme1 et binôme2 sont vos deux noms) en utilisant la variable NOMS.

**echo "Bonjour à moi, $NOMS"**   
*Bonjour à moi, THOUVENIN*   

### 9. Quelle différence y a-t-il entre donner une valeur vide à une variable et l’utilisation de la commande unset ?

**unset MY_VAR ; printenv MY_VAR**   
unset supprime totalement la var alors qu'une affectation vide ne supprime pas une variable mais la conserve vide.   

### 10. Utilisez la commande echo pour écrire exactement la phrase : $HOME = chemin (où chemin est votre dossier personnel d’après bash)

**echo "\$HOME = $HOME"**   
*$HOME = /home/nico*   


## Vous enregistrerez vos scripts dans un dossier script que vous créerez dans votre répertoire personnel.
## Tous les scripts sont bien entendu à tester.
## Ajoutez le chemin vers script à votre PATH de manière permanente.

**echo PATH=\$PATH:~/script >> ~/.bashrc**   
**echo $PATH**   

## Exercice 2 - Mot de Passe

### Écrivez un script testpwd.sh qui demande de saisir un mot de passe et vérifie s’il correspond ou non au contenu d’une variable PASSWORD dont le contenu est codé en dur dans le script. Le mot de passe saisi par l’utilisateur ne doit pas s’afficher.

**sudo nano testpwd.sh"   
**sudo chmod u+x testpwd.sh**   
**sudo ./testpwd.sh**   

```
#!/bin/bash

echo "Bienvenue dans le check du password !!!!"

PASSWORD="coucou"

read -s -p 'Saisissez votre mot de passe : ' mdp
#-p pour afficher le texte avant la saisie
#-s pour cacher le texte saisi (utile pour un mdp)


if [ $mdp = $PASSWORD ]; then
        echo -e "\nVotre mdp est juste"
else
        echo -e "\nnullos"
fi
```

## Exercice 3. Expressions rationnelles

### Ecrivez un script qui prend un paramètre et utilise la fonction suivante pour vérifier que ce paramètre est un nombre réel. Il affichera un message d’erreur dans le cas contraire.

**sudo ./realnumber.sh ah**   
*ah n'est pas un nombre*   

**sudo ./realnumber.sh 2**   
*2 est un nombre*   


```
#!/bin/bash

function is_number {
        re='^[+-]?[0-9]+([.][0-9]+)?$'
        if ! [[ $1 =~ $re ]] ; then
                return 1
        else
                return 0
        fi
}

if is_number $1; then
        echo "$1 est un nombre"
else
        echo "$1 n'est pas un nombre"
fi
```

## Exercice 4. Contrôle d’utilisateur

### Écrivez un script qui vérifie l’existence d’un utilisateur dont le nom est donné en paramètre du script. Si le script est appelé sans nom d’utilisateur, il affiche le message : ”Utilisation : nom_du_script nom_utilisateur”, où nom_du_script est le nom de votre script récupéré automatiquement (si vous changez le nom de votre script, le message doit changer automatiquement)


**sudo ./usercontrol.sh oui**   
*Ton arg est : oui   
Il n'y pas de user correspondant au nom oui :-(*   

**sudo ./usercontrol.sh root**   
*Ton arg est : root   
Il y a un user correspondant au nom root !! :D*   

**sudo ./usercontrol.sh**   
*Utilisation : ./usercontrol.sh nom_utilisateur*   


```
#!/bin/bash

if [ -n "$1" ]; then
        echo "Ton arg est : $1"
        res=$(sudo cat /etc/passwd | cut -d: -f1 | uniq | grep "$1" | wc -l)
        if [ $res = "1" ]; then
                echo "Il y a un user correspondant au nom $1 !! :D "
        else
                echo "Il n'y pas de user correspondant au nom $1 :-( "
        fi
else
        echo "Utilisation : $0 nom_utilisateur"
fi
```

## Exercice 5. Factorielle

### Écrivez un programme qui calcule la factorielle d’un entier naturel passé en paramètre (on supposera que l’utilisateur saisit toujours un entier naturel).

**sudo ./factorial.sh 19**   
*121645100408832000*   

```
#!/bin/bash

counter=$1
factorial=1
while [ $counter -gt 0 ] #counter > 0
do
   factorial=$(( $factorial * $counter ))
   counter=$(( $counter - 1 ))
done
echo $factorial

# EASIER WAY USING BC :

echo $(seq -s "*" $1 | bc)
#seq -s "*" 3 -> 1*2*3
```

## Exercice 6. Le juste prix

### Écrivez un script qui génère un nombre aléatoire entre 1 et 1000 et demande à l’utilisateur de le deviner. Le programme écrira ”C’est plus !”, ”C’est moins !” ou ”Gagné !” selon les cas (vous utiliserez $RANDOM).

```
#!/bin/bash

rand=$((RANDOM%1000))
nb=-1
min=1
max=1000

while [[ $nb != $rand ]]
do
        read -p "Entrez un nombre entre $min et $max : " nb
        if [[ $nb -lt $rand ]]; then
                echo -e "C'est plus !\n"
                if [[ $min -lt $nb ]]; then
                        min=$nb
                fi
        elif [[ $nb -gt $rand ]]; then
                echo -e "C'est moins !\n"
                if [[ $max -gt $nb ]]; then
                        max=$nb
                fi
        else
                echo -e "C'est gagné !!!\n\n"
        fi
done

read -p "Souhaitez-vous refaire une partie ? [o|N]" newG
newG=$(echo $newG | cut -c1-1 | tr a-z A-Z)
#get first char of entry and toUpper it
echo -e "\n\n"
if [[ $newG = "Y" || $newG = "O" ]]; then
        exec "$0"
        #stop current script and launch current script again
fi
```

## Exercice 7. Statistiques

### 1. Écrivez un script qui prend en paramètres trois entiers (entre -100 et +100) et affiche le min, le max et la moyenne. Vous pouvez réutiliser la fonction de l’exercice 3 pour vous assurer que les paramètres sont bien des entiers.

### 2. Généralisez le programme à un nombre quelconque de paramètres (pensez à SHIFT)
```
#!/bin/bash

function is_number {
        re='^[+-]?[0-9]+([.][0-9]+)?$'
        if ! [[ $1 =~ $re ]] ; then
                return 1
        else
                return 0
        fi
}

check=0
tab=()
while (("$#")); do
        if is_number $1 ; then
                if [[ $1 -lt -100 || $1 -gt 100 ]]; then
                        check=$(( $check + 1 ))
                        echo "$1 pas entre -100 et 100"
                else
                        tab[$(($# - 1))]=$1
                fi
        else
                echo "$1 n'est pas un nombre"
                check=$(( $check + 1 ))
        fi

        shift
done


if [[ $check -eq 0 ]]; then
        min=${tab[0]}
        max=${tab[0]}
        sum=0
        counter=0
        for i in ${tab[@]}; do
                if [[ $i -lt $min ]]; then
                        min=$i
                elif [[ $i -gt $max ]]; then
                        max=$i
                fi
                counter=$(( $counter + 1 ))
                sum=$(( $sum + $i ))
                shift
        done
        moy=$(( sum / counter ))
        echo -e "Min : $min\nMax : $max\nMoy : $moy"
fi
```


### 3. Modifiez votre programme pour que les notes ne soient plus données en paramètres, mais saisies et stockées au fur et à mesure dans un tableau.

```
#!/bin/bash

function is_number {
        re='^[+-]?[0-9]+([.][0-9]+)?$'
        if ! [[ $1 =~ $re ]] ; then
                return 1
        else
                return 0
        fi
}


tab=()
counter=0
sum=0
read -p "Entrez une note ("done" to quit) : " note
max=-101
min=101
while : ; do
        if is_number $note ; then
                if [[ $note -lt -100 || $note -gt 100 ]]; then
                        echo "$note pas entre -100 et 100"
                else
                        tab[$counter]=$note
                        counter=$(( counter + 1 ))
                        if [[ $note -lt $min ]]; then
                                min=$note
                        fi
                        if [[ $note -gt $max ]]; then
                                max=$note
                        fi
                        sum=$(( $sum + $note ))
                        moy=$(( sum / counter ))
                        echo ${tab[@]}
                        echo -e "Min : $min\nMax : $max\nMoy : $moy"
                fi
        else
                if [[ "$note" = "done" ]]; then
                        break
                else
                        echo "$note n'est pas un nombre"
                fi
        fi
        read -p "Entrez une note ("done" to quit) : " note
done
```
