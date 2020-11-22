# TP4

## Exercice 1

### 1. Utilisez la commande groupadd pour créer deux groupes dev et infra
**sudo groupadd dev**   
**sudo groupadd infra**   

### 2. Créez ensuite 4 utilisateurs alice, bob, charlie, dave avec la commande useradd, en demandant la création de leur dossier personnel et avec bash pour shell
Afficher les options par défauts de useradd : **useradd -D**   
**sudo useradd -m -s /bin/bash alice**   
**sudo useradd -m -s /bin/bash bob**     
**sudo useradd -m -s /bin/bash charlie**     
**sudo useradd -m -s /bin/bash dave**   
Pour afficher le group auquel a été affecté un user : **groups alice**

### 3. Ajoutez les utilisateurs dans les groupes créés : - alice, bob, dave dans dev - bob, charlie, dave dans infra
Pour ajouter un user existant à un groupe (secondaire) : 
**sudo usermod -a -G dev alice**
**sudo usermod -a -G dev bob   
sudo usermod -a -G dev dave   
sudo usermod -a -G infra bob   
sudo usermod -a -G infra charlie   
sudo usermod -a -G infra dave   ***

### 4. Donnez deux moyens d’afficher les membres de infra
**cat /etc/group | grep "infra"**   
**sudo cat /etc/gshadow | grep "infra"**   

### 5. Faites de dev le groupe propriétaire des répertoires /home/alice et /home/bob et de infra le groupe propriétaire de /home/charlie et /home/dave
**sudo chgrp -R dev /home/alice**   
**sudo chgrp -R dev /home/bob**   
**sudo chgrp -R infra /home/charlie**   
**sudo chgrp -R infra /home/dave**   

### 6. Remplacez le groupe primaire des utilisateurs : - dev pour alice et bob - infra pour charlie et dave
**sudo usermod alice -g dev**   
**sudo usermod bob -g dev**    
**sudo usermod charlie -g infra**   
**sudo usermod dave -g infra**   

### 7. Créez deux répertoires /home/dev et /home/infra pour le contenu commun aux membres de chaque groupe, et mettez en place les permissions leur permettant d’écrire dans ces dossiers.
**sudo mkdir /home/dev**   
**sudo mkdir /home/infra**   
**sudo chmod 755 /home/infra**   
**sudo chmod 755 /home/dev**   

### 8. Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer ou supprimer ce fichier ?
**cd /home/dev**
**umask 0022**   
**cd /home/infra**
**umask 0022**  

### 9. Pouvez-vous ouvrir une session en tant que alice ? Pourquoi ?
Non parce que son compte est inactif parce qu'elle n'a pas de mdp

### 10. Activez le compte de l’utilisateur alice et vérifiez que vous pouvez désormais vous connecter avec son compte
**sudo passwd alice**   

### 11. Comment obtenir l’uid et le gid de alice ?
**id alice**   

### 12. Quelle commande permet de retrouver l’utilisateur dont l’uid est 1003 ?
**cat /etc/passwd | grep "1003"**
*charlie:x:1003:1002::/home/charlie:/bin/bash*

### 13. Quel est l’id du groupe dev ?
**cat /etc/group | grep "dev"**   
**dev:x:1001:alice,bob,dave**   

### 14. Quel groupe a pour gid 1002 ? ( Rien n’empêche d’avoir un groupe dont le nom serait 1002...)
**cat /etc/group | grep "1002"**   
*infra:x:1002:bob,charlie,dave* 

### 15. Retirez l’utilisateur charlie du groupe infra. Que se passe-t-il ? Expliquez.
**sudo gpasswd -d charlie infra**   


### 16. Modifiez le compte de dave de sorte que : — il expire au 1er juin 2021 — il faut changer de mot de passe avant 90 jours — il faut attendre 5 jours pour modifier un mot de passe — l’utilisateur est averti 14 jours avant l’expiration de son mot de passe — le compte sera bloqué 30 jours après expiration du mot de passe
**sudo chage --expiredate "Jun 1, 2021" dave**   
**sudo chage --maxdays 90 dave**   
**sudo chage --mindays 5 dave**   
**sudo chage --warndays 14 dave**  
**sudo chage --inactive 30 dave**   
**sudo chage -l dave**   
*Last password change : Nov 16, 2020    
Password expires : Feb 14, 2021    
Password inactive : Mar 16, 2021   
Account expires : Jun 01, 2021    
Minimum number of days between password change : 5    
Maximum number of days between password change : 90   
Number of days of warning before password expires : 14*


### 17. Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?
**cat /etc/passwd | grep "root"**
*root:x:0:0:root:/root:/bin/bash*

### 18. Si vous regardez la liste des comptes présents sur la machine, vous verrez qu’il en existe un nommé *nobody*. A quoi correspond-il ?
Il correspond à l'utilisateur avec le moins de droit sur la machine

### 19. Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ? Quelle commande permet de forcer sudo à oublier votre mot de passe ?
**man sudo**   
Par défaut, sudo conserve notre mdp pendant 15 minutes    
**sudo -k**   

## Exercice 2

### 1. Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques lignes de texte. Quels sont les droits sur test et fichier ?
**mkdir test**   
**ls -l test**   
*drwxrwxr-x 2 nico nico   4096 Nov 16 15:10 test*   
**cd test**   
**touch file**   
**ls -l file**
*-rw-rw-r-- 1 nico nico 64 Nov 16 15:10 file*

### 2. Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en tant que root. Conclusion ?
**chmod ugo-rwx file**   
**ls -l file**    
*---------- 1 nico nico 64 Nov 16 15:10 file*   
Avec sudo, il est quand même possible de modifier un fichier

### 3. Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un fichier s’il existe déjà. Que peut-on dire au sujet des droits ?
La commande **echo Hello > file** fonctionne, on peut donc dire que les droits d'écriture sont nécessaires pour cette commande

### 4. Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez
Cela ne fonctionne pas car on essaye d'execute le fichier (avec bash ou sh) mais que Hello n'est pas reconnue comme commande

### 5. Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ? Rétablissez le droit en lecture sur test.
**chmod u-r .**   
**ls**   
*ls: cannot open directory '.': Permission denied*   
**cat file**   
*Hello*
Si on a pas le droit de lecture sur le dossier, on peut quand même lire le fichier donc les droits pour un dossier ne sont pas récursifs par défauts et n'affectent pas les fichiers enfants.

### 6. Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvezvous déduire de toutes ces manipulations ?
**touch new    
mkdir sstest    
ls**    
*file  new  sstest*    
**chmod u-w new    
chmod u-w .   
nano new**   
*denied*   
**chmod u+w .   
nano new**    
*denied*   
**rm new**     
*rm: remove write-protected regular empty file 'new'? y*
Avec les droits en écriture sur un dossier, on peut supprimer ses fichiers même si on a pas les droits d'écriture sur lesdits fichiers

### 7. Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test. Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?
Pour les répertoires, le droit d'execution correspond à la possibilité d'effectuer des actions dans ce répertoire

### 8. Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd ..” ? Pouvez-vous donner une explication ?
Si l'on a plus le droit d'execution sur le répertoire courant, alors on a plus la possibilité de rien faire sur ce dossier et son contenu hormis le quitter 

### 9. Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.
**chmod g+r file**   

### 10. Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture, ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.
**umask 0077**   
**umask -S**     
*u=rwx,g=,o=*
### 11. Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires, mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire
**umask 0022**   
**umask -S**   
*u=rwx,g=rx,o=rx*   

### 12. Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.
**umask 0037**    
*u=rwx,g=r,o=*   

### 13. Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous pourrez vous aider de la commande stat pour valider vos réponses) :
*chmod u=rx,g=wx,o=r fic*    
**chmod 534 fic**
*chmod uo+w,g-rx fic* en sachant que les droits initiaux de fic sont r--r-x---    
**chmod 602 fic**
*chmod 653 fic en sachant que les droits initiaux de fic sont 711*    
**chmod u-x,g+r,o+w fic**   
*chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---*   
**chmod 520 fic**    

### 14. Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier /etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?
**which passwd**   
*/usr/bin/passwd*
**stat -c %A /usr/bin/passwd**    
*-rwsr-xr-x*
**stat -c %A /etc/passwd**    
*-rw-r--r--*
Ce "s" indique que ce programme est exécuté avec les droits de son propriétaire pour permettre aux utilisateurs d’exécuter ce programme comme s’ils étaient root : c’est le rôle du droit setuid (Set User ID)


