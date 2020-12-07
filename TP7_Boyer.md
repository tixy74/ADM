# TP7 : Boot, services et processus / Tâches d'administration (2)

## BOYER Charles - 3IRC

---

Ce TP a été effectué sur une machine physique, sous Ubuntu Desktop.

---

### Exercice 1 : Personnalisation de GRUB

1. Préparation

    ```bash
    > sudo cp /etc/default/grub.d/50-curtin-settings.cfg # Si il n'est pas présent, on s'en fout
    ```

2. Modification du délai du menu GRUB

    ```bash
    > sudo nano /etc/default/grub
    #Modifier le fichier:
        GRUB_TIMEOUT=10 # Pour mettre un délai de 10sec
    ```

3. Mise à jour de la configuration

    ```bash
    > sudo update-grub
    ```

4. Vérification de la configuration

    Redemarrer la VM. Si le menu GRUB s'affiche avec un délai de 10sec, c'est bon

5. Modification de la résolution

    ```bash
    > sudo nano /etc/default/grub
    #Modifier le fichier:
        GRUB_GFXMODE=1920x1080
        GRUB_GFXPAYLOAD_LINUX=1920x1080 # Pour la VM
    > sudo update-grub
    ```

6. Ajout d'un fond d'écran

    ```bash
    > sudo apt install grub2-splashimages
    > sudo cp /usr/share/images/grub/Plasma-lamp.tga /boot/grub
    > sudo nano /etc/default/grub
    #Ajouter dans le fichier:
        GRUB_BACKGROUND="/boot/grub/Plasma-lamp.tga"
    > sudo update-grub
    ```

7. Ajout d'un thème

    ```bash
    > A FAIRE
    ```

8. Ajout d'entrées

    ```bash
    > sudo nano /etc/grub.d/custom_40
    # Ajouter dans le fichier:
        menuentry "Reboot" {
            reboot
        }

        menuentry "Eteindre" {
            halt
        }
    > sudo update-grub
    ```

9. Changement de langue du clavier dans GRUB

    ```bash
    > sudo mkdir /boot/grub/layouts
    > sudo grub-kbdcomp -o /boot/grub/layouts/fr.gkb fr
    > sudo nano /etc/default/grub
    # Ajouter dans le fichier:
        GRUB_TERMINAL_INPUT=at_keyboard
    > sudo nano /etc/grub.d/40_custom
    # Ajouter dans le fichier:
        insmod keylayouts
        keymap fr
    ```

---

### Exercice 2 : Noyau

1. Installation des paquets de compilation C

    ```bash
    > sudo apt isntall build-essential
    ```

2. Création du module

    ```bash
    > touch hello.c
    # Ajouter dans le fichier
    ```

    ```c
    #include <linux/module.h>
    #include <linux/kernel.h>

    MODULE_LICENSE("GPL");
    MODULE_AUTHOR("John Doe");
    MODULE_DESCRIPTION("Module hello world");
    MODULE_VERSION("Version 1.00");

    int init_module(void)
    {
        printk(KERN_INFO "[Hello world] - La fonction init_module() est appelée.\n");
        return 0;
    }
        
    void cleanup_module(void)
    {
        printk(KERN_INFO "[Hello world] - La fonction cleanup_module() est appelée.\n");
    }
    ```

3. Création du MakeFile

    ```bash
    > touch Makefile
    # Ajouter dans le fichier
    ```

    ```c
    obj-m += hello.o
    
    all:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
    
    clean:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
    
    install:
        cp ./hello.ko /lib/modules/$(shell uname -r)/kernel/drivers/misc
    ```

4. Lancement Makefile

    ```bash
    > make
    > sudo make install
    ```

5. Chargement du module

    ```bash
    > sudo insmod /lib/modules/5.4.0-53-generic/kernel/drivers/misc/hello.ko
    > lsmod | grep hello # Pour vérifier qu'il est activer
    ```

6. Description du module

    ```bash
    > modinfo hello.ko
    ```

7. Dechargement du module

    ```bash
    > sudo rmmod /lib/modules/5.4.0-53-generic/kernel/drivers/misc/hello.ko
    ```

8. Lancement au démarrage

    ```bash
    > sudo nano /etc/modules
    # Ajouter dans le fichier:
        hello
    > reboot
    > lsmod
    ```

---

### Exercice 3 : Interception de signaux

1. Création du script

    ```bash
    > touch recopie
    > chmod +x recopie
    > nano recopie
    # Mettre dans le fichier:
        #!/bin/bash
        while :
        do 
            read a 
            echo $a > tmp.txt
        done
    ```

2. Test du script avec CTRL+Z

    ```bash
    > ./recopie
    # Appuie sur CTRL+Z
    ```
    Quand on appuie sur CTRL+Z, le script se stoppe.

3. Test du script avec CTRL+C

    ```bash
    > ./recopie
    # Appuie sur CTRL+C
    ```
    Quand on appuie sur CTRL+C, le script s'interront.

4. Prise en charge des signaux

    ```bash
    > nano recopie
    # Rajouter dans le fichier:
        handlerSIGINT()
        {
            echo OK, je fais un peu de ménage avant
            rm tmp.txt
            exit
        }

        handlerSIGTSTP()
        {
            echo Impossible de me placer en arrière plan
        }

        trap handlerSIGINT SIGINT;
        trap handlerSIGTSTP SIGTSTP;
    > ./recopie #Pour tester
    ```

5. La commande ```kill```

    ```bash
    > ./recopie
    > ps -u # Pour avoir le pid de recopie
    > kill -9 1345
    #Le programme affiche Killed
    ```

6. Utilisation de la commande ```test```

    ```bash
    > nano recopie
    # Rajouter dans le fichier (dans la fonction handlerSIGINT):
        if test -f "tmp.txt"; then
            rm tmp.txt
        fi
    > ./recopie #Pour tester
    ```

---

### Exercice 4 : Surveillance de l'activité du système

1. La commande ```htop```

    Quand on lance cette commande dans un terminal, puis qu'on en ouvre un autre, on peux voir qu'une nouvelle entrée apparait. La commande affiche donc tout les processus en cours sur la machine. ```w``` permet de lister les users connecté et de voir ce qu'ils font.

2. Les dernières connexions à la machine

    Pour afficher les dernières connexion à la machine, nous pouvons utiliser la commande ```last```

3. La version du noyau

    Pour afficher la version du noyau, nous pouvons utiliser la commande ```uname -r```.

4. Informations du processeur en JSON
   
   Pour obtenir afficher toutes les informatiions du processeur, au format JSON, nous pouvons utiliser la commande:

   ```bash
   > sed -n '/./s/ *\(\( *[^:[:blank:]]\)*\)[^:]*\(:*\)/"\1"\3/gp' /proc/cpuinfo
   ```


