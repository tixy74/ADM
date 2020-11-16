# Tp1 - THOUVENIN Nicolas 3IRC

## Exercice 2

### Manuel

#### 1.A l’aide du manuel, identifiez le rôle de la commande which

**man which**   
La commande which cherche le path de l'executable relié à la commande passée en paramètre, par exemple :   
**which sudo**   
*/usr/bin/sudo*   
Le paramètre -a de la commande permet à which de chercher la totalité des paths pour une commande, ex :   
**which -a sudo**   
*/usr/bin/sudo*   
*/bin/sudo*   

#### 2.Quand on consulte une page du manuel, comment peut-on rechercher un terme (par exemple, chercher le terme option dans la page de manuel de which ?

**man which**   
**/option**   

#### 3.Comment quitte-t-on le manuel ?

**q**   

#### 4.Chaque section du manuel a une première page, qui présente le contenu de la section. Afficher la première page de la section 6; de quoi parle cette section ?

**man man**   
**man 6 intro**   

### Navigation dans l’arborescence des fichiers

#### 1.allez dans le dossier /var/log

**cd /var/log**   

#### 2.remontez dans le dossier parent (/var) en utilisant un chemin relatif

**cd ..**   

#### 3.retournez dans le dossier personnel

**cd**   

#### 4.revenez au dossier précédent (/var) sans utiliser de chemin

**cd -**   

#### 5.essayez d’accéder au dossier /root; que se passe-t-il?

*-bash: cd: /root: Permission denied*   
Pas le droit d'accéder au cd d'admin en tant qu'user   

#### 6.essayez la commande sudo cd /root; que se passe-t-il? Expliquez

*sudo: cd: command not found*   
cd n'est pas un executable et donc ne peut pas être lancé par sudo   

#### 7.à partir de votre dossier personnel, créez l’arborescence suivante :

**mkdir Dossier1**   
**mkdir Dossier2**   
**cd Dossier2**   
**mkdir Dossier2.1**   
**mkdir Dossier2.2**      
**cd Dossier2.2**   
**touch Fichier2**   
**touch Fichier3**   
**cd**   
**cd Dossier1**   
**touch Fichier1**   

#### 8.revenez dans votre dossier personnel; à l’aide de la commande rm, essayez de supprimer Fichier1, puis Dossier1; que se passe-t-il?

**rm Dossier1/Fichier1**   
**rm Dossier1**   
*rm: cannot remove 'Dossier1': Is a directory*   

#### 9.quelle commande permet de supprimer un dossier?

**rmdir Dossier1**   

#### 10.que se passe-t-il quand on applique cette commande à Dossier2?

**rmdir Dossier2**   
*rmdir: failed to remove 'Dossier2': Directory not empty*   

#### 11.comment supprimer en une seule commande Dossier2 et son contenu?
   
**rm -r Dossier2**   

### Commandes importantes

#### 1.Quelle commande permet d’afficher l’heure? A quoi sert la commande time?

**date**   
time : sert à chronométrer ou évaluer les ressources employées par une commande passée en paramètre   

#### 2.Dans votre dossier personnel, tapez successivement les commandes ls puis la; que peut-on en déduire sur les fichiers commençant par un point?

**la**    
Les fichiers commençant par à un point sont des fichiers cachés   

#### 3.Où se situe le programme ls?
   
**which -a ls**   
*/usr/bin/ls*   
*/bin/ls*   

#### 4.Essayez la commande ll. Existe-t-il une entrée de manuel pour cette commande? Utilisez les commandes alias ou alias pour en savoir plus sur la nature de cette commande.

**ll**   
**alias ll**   
Permet d'executer la commande 'ls -alF' en moins de caractères   

#### 5.Quelle commande permet d’afficher les fichiers contenus dans le dossier/bin?

**ll /bin**   

#### 6.Que fait la commande ls ..?

**ls ..** liste le contenu du dossier parent à l'actuel   

#### 7.Quelle commande donne le chemin complet du dossier courant?

**pwd**   

#### 8.Que fait la commande echo 'yo' > plop exécutée 2 fois?

**echo 'yo' > plop** créé un fichier plop et y insert le texte "yo"   
Executer la commande une seconde fois écrase le fichier plop et écrit "yo" à l'interieur du nouveau fichier   

#### 9.Que fait la commande echo 'yo' >> plop exécutée 2 fois?

**echo 'yo' >> plop** modifie un fichier nommé "plop" ou le créé s'il n'existe pas et append à la suite du fichier une nouvelle ligne composé du texe "yo"   

#### 10. Interprétez le comportement de la commande sleep 10 | echo 'toto'?

Affiche le texte "toto" dans la console puis attends 10 secondes après l'écriture pour relancer la console   

#### 11.A quoi sert la commande file? Essayez la sur des fichiers de types différents.

**file** renvoie le type de fichier indépendamment de son extension   

#### 12.Créez un fichier toto qui contient la chaîne Hello Toto !; créer ensuite un lien titi vers ce fichier avec la commande ln toto titi. Modifiez à présent le contenu detotoet affichez le contenu de titi : qu’observe-t-on? Supprimez le fichier toto; quelle conséquence cela a-t-il sur titi?

**echo 'Hello Toto !' >> toto**   
**ln toto titi**   
**nano toto**   
Le contenu de titi a également été modifié   
**rm toto**   
titi n'a pas été supprimé ni modifié depuis la dernière modif de toto -   


#### 13.Créez à présent un lien symbolique tutu sur titi avec la commande ln -s titi tutu. Modifiez le contenu de titi; quelle conséquence pour tutu? Et inversement? Supprimez le fichier titi; quelle conséquence cela a-t-il sur tutu?

**ln -s titi tutu**   
**nano titi**   
Le contenu de tutu a également été modifié   
**nano tutu**   
Le contenu de titi a également été modifié   
**rm titi**   
Le fichier tutu passe au rouge dans ls et devient vide quand on l'ouvre   

#### 14.Affichez à l’écran le fichier /var/log/syslog. Quels raccourcis clavier permettent d’interrompre et reprendre le défilement à l’écran?

**sudo cat /var/log/syslog**   
CTRL+S : pause   
CTRL+Q : resume   

#### 15.Affichez les 5 premières lignes du fichier /var/log/syslog, puis les 15 dernières, puis seulement leslignes 10 à 20

**head -5 /var/log/syslog**   
**tail -15 /var/log/syslog**   
**sed -n 10,20p /var/log/syslog**   

#### 16.Que fait la commande dmesg | less ?

**dmesg | less** affiche ligne par ligne les messages du kernel   

#### 17.Affichez à l’écran le fichier /etc/passwd; que contient-il? Quelle commande permet d’afficher la page de manuel de ce fichier?

**sudo cat /etc/passwd**   
**man 5 passwd**   

#### 18.affichez seulement la première colonne triée par ordre alphabétique inverse

**cat /etc/passwd | cut -d: -f1 | sort -r**   
**awk -F':' '{print $1}' /etc/passwd | sort -r | uniq**   
**getent passwd | cut -d: -f1 | sort -r | uniq**   

#### 19.Quelle commande nous donne le nombre d’utilisateurs ayant un compte sur cette machine (pas seulement les utilisateurs connectés)?

**cat /etc/passwd | wc -l**   
**getent passwd | cut -d: -f1 | sort -r | uniq | wc -l**   

#### 20. Combien de pages de manuel comportent le mot-clé conversion dans leur description?

**apropos conversion**   

#### 21.A l’aide de la commande find, recherchez tous les fichiers se nommant passwd présents sur la machine

**sudo find / -name passwd**   

#### 22. Modifiez la commande précédente pour que la liste des fichiers trouvés soit enregistrée dans le fichier ~/list_passwd_files.txt et que les erreurs soient redirigées vers le fichier spécial/dev/null

**sudo find / -name passwd >> ~/list_passwd_files.txt 2> /dev/null**     
**ping google.com 1>/dev/null 2>error.log** -> renvoie stdout dans le néant et stderr dans le fichier error.log    
**grep -r power /sys/ &>/dev/null** -> renvoie stdout et stderr dans le néant   

#### 23.Dans votre dossier personnel, utilisez la commande grep pour chercher où est défini l’alias ll vu précédemment

L'alias est défini dans le fichier ~/.bashrc   

#### 24.Utilisez la commande locate pour trouver le fichier history.log

**locate history.log**   
*/var/log/apt/history.log*   

#### 25.Créer un fichier dans votre dossier personnel puis utilisez locate pour le trouver. Apparaît-il? Pourquoi?

**touch oui**   
**locate oui**   
non il n'apparait pas parce que le fichier n'est pas dans la base de données des fichiers indexés   

## Exercice 3

#### 1. Copiez le fichier /var/log/syslog dans votre dossier personnel sous le nom log.txt, puis ouvrez-le avec nano

**cp /var/log/syslog ./oui**   
**nano oui**   

#### 2. Remplacez toutes les occurrences du mot kernel par le mot noyau

**CTRL+W**   
**CTRL+R**   

#### 3. Déplacer les 10 premières lignes à la fin du fichier

**CTRL+K** * 10   
**CTRL+ArrowDown**   
**CRTL+U**   

#### 4. Annulez cette action

**ALT+U**   

#### 5. Enregistrez le fichier avant de quitter nano

**CTRL+O**   
**Entrée**   
**CTRL+X**   

## Exercice 4 

#### 1. Commencez par créer une copie de ce fichier, que vous appellerez .bashrc_bak

**cp ~/.bashrc .bashrc_bak**   

#### 2. Editez le fichier .bashrc avec nano et décommentez la ligne force_color_prompt=yes pour activer la couleur. Enregistrez le fichier et quittez nano



#### 3. Le fichier .bashrc est lu au démarrage du shell ; pour le recharger, il faudrait donc se déconnecter puis se reconnecter ; mais il existe un autre moyen : la commande source .bashrc. Testez-la, l’invite de commande devrait immédiatement passer en couleurs.

**source .bashrc**   

#### 4. Les couleurs par défauts (surtout celle du dossier courant) ne sont pas très visibles. Dans .bashrc, cherchez les lignes commençant par PS1= ; elles indiquent la mise en forme de l’invite de commande (selon que l’on est en couleurs ou non).

**CTRL+W**   

```
\[\033[1;35m\]\t\[\033[00m\] - \[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;96m\]\w\[\033[00m\]\$   
```
