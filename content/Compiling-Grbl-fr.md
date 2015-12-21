_Ce wiki est destiné à fournir diverses instructions sur la façon de compiler grbl. Une fois compilé, vous devriez avoir un nouveau fichier .hex qui sera utilisé pour flasher votre Arduino. S'il vous plaît, vous êtes libre de contribuer avec en mettant à jour les informations ci-dessous ou avec d'autres méthodes._

# Via l'IDE Arduino (toutes plateformes):
_Dernière mise à jour: 15/06/2015 par @chamnit. (Testé sur OS X 10.10 avec Arduino IDE v1.65)_

Merci aux personnes travaillant sur l'IDE [Arduino](/lien/ 'Arduino'){: .duino}, il a tout ce dont vous avez besoin pour compiler grbl. Tout est inclus dans le package [logiciels](http://arduino.cc/en/Main/Software).
NOTE: Cette méthode compile le code source dans un nouveau code hex et l'envoi ensuite automatiquement à l'Arduino. Vous ne pouvez pas flasher directement un fichier pré-compilé .hex via l'interface IDE Arduino. Voir notre page Wiki [Flasher Grbl à un Arduino](flasher_grbl.html) pour savoir comment faire si vous avez seulement un fichier .hex.

_NOTE: Avant de commencer, assurez-vous que toute installation plus ancienne de Grbl a été complètement retiré de l'IDE Arduino._

_NOTE: Il y a eu des rapports de problèmes de téléchargement sur certaines ancienne cartes Arduino. Typiquement, re-flasher le bloc de démarrage Arduino résout ce problème. Ceci peut être réalisé avec un Arduino séparé assez facilement, et il y a de nombreuses ressources en ligne pour vous montrer comment faire._

1. Télécharger le code source Grbl (à ce jour v0.9j).
 * Cliquez sur le bouton [Download ZIP](https://github.com/grbl/grbl/archive/master.zip){: .btn} sur le côté supérieur droit de la page d'accueil.
 * Une fois téléchargé, décompressez l'archive et vous obtiendrez un dossier appeler ```grbl-master``` ou quelque chose de semblable.
2. Assurez-vous que vous utilisez la version la plus récente de l'IDE Arduino (la dernière version testée étant la v1.61).
3. Chargez Grbl dans l'IDE Arduino comme une bibliothèque.
 * Lancez l'IDE Arduino.
 * Cliquez sur le menu déroulant ```Sketch```, puis naviguez jusqu'à ```Inclure Bibliothèque```, et cliquez sur ```Ajouter Bibliothèque .ZIP```, notez que cela fonctionne même avec un simple dossier (avant la version 1.6.2 ce sera ```Importer une bibliothèque...```, et cliquez sur ```Ajouter une bibliothèque...```).
 * Sélectionnez le dossier ```Grbl``` à l'intérieur du dossier ```grbl-master``` lorsqu'il vous sera demandé de sélectionner un dossier de la bibliothèque que vous souhaitez ajouter. Le dossier correct contient uniquement les fichiers source et un répertoire d'exemple.
 * Cela peut prendre quelques secondes pour que l'IDE Arduino importe la nouvelle bibliothèque.
 *_NOTE: Pour les utilisateurs d'une version Arduino IDE pré-v1.05, vous devrez ajouter manuellement Grbl dans vos bibliothèques Arduino, de sorte qu'elle apparaisse dans le menu ```Ajouter une bibliothèque...```. Faites une simple recherche sur Internet pour savoir comment installer, puis passez à l'étape 4._
4. Ouvrez l'exemple GrblUpload.
 * Cliquez sur ```Fichier``` dans le menu déroulant en bas, accédez à ```Examples->Grbl```, et sélectionnez ```GrblUpload```.
5. Compiler et télécharger Grbl dans votre Arduino.
 * Connectez votre Arduino Uno à votre ordinateur.
 * Assurez-vous que votre modèle est fixé sur l'Arduino Uno dans le menu ```Outil->Type de carte``` et que le port série est correctement sélectionné dans ```Outil-> Port Serie```.
 * Cliquez sur ```Envoyer```, et Grbl devrait compiler et être envoyé sur votre Arduino! (Flasher avec un programmateur fonctionne également en utilisant la commande de menu ```Envoyer à l'aide du programmateur```.)

Une fois que vous avez votre bibliothèque ```Grbl``` mis en place dans l'IDE Arduino, vous pouvez mettre à jour, remplacer ou modifier le code source Grbl dans le dossier de la bibliothèque. Sur un Mac, il est situé dans ```~/Documents/Arduino/bibliothèques/```. Sur Windows, il est dans ```Mes Documents\Arduino\bibliothèques```. Vous devrez peut-être redémarrer l'IDE Arduino pour que cette modification prenne effet.

Pas de problèmes! Aucune problèmes!


# Pour * *{: .fa .fa-apple} Mac OS X :
_Dernière mise à jour: 2012-01-29 par @chamnit. (Testé sur OS X 10.7, 10.6, 10.4 et les Arduino IDE r22, v1.0)_

Cette méthode de compilation Grbl utilise le terminal et la ligne de commande Mac OSX pour accéder aux compilateurs de l'IDE Arduino sans avoir à utiliser l'IDE Arduino. Cela produit le même firmware que la méthode avec l'IDE Arduino ci-dessus.

D'abord, vous devez vous assurer que vous avez la version la plus récente de l'IDE Arduino installé sur votre Mac. La partie la plus délicate est de mettre en place le chemin de l'environnement pour les compilateurs inclus dans le logiciel Arduino. Pour ce faire, vous aurez besoin d'abord de localiser où ils sont. Selon l'endroit où vous placez votre logiciel Arduino.app, ce sera généralement situé dans _/Applications/Arduino.app_ pour la plupart des gens. Le chemin complet est alors: ```/Applications/Arduino.app/Contents/Resources/Java/hardware/tools/avr/bin```

**Pour ajouter le chemin du compilateur:** Ouvrez le Terminal dans /Applications/Utilitaires.

Puis tapez:

    nano ~/.bashrc

afin de pouvoir modifier votre fichier de configuration shell.

Maintenant, ajoutez cette ligne à la fin du fichier:

    export PATH=$PATH:/Applications/Arduino.app/Contents/Resources/Java/hardware/tools/avr/bin

ou quelque soit ou votre chemin se trouve être.

Appuyez sur _CTRL-X_ pour quitter et sélectionnez _Oui_ pour enregistrer le fichier. Maintenant que vous avez ajouté le chemin du compilateur. Vous aurez besoin de fermer la fenêtre de travail actuelle et en ré-ouvrir une nouvelle pour que le chemin soit charger correctement.

NOTE: Si vous rencontrez des problèmes, vous pouvez avoir besoin d'ajouter ce même chemin de votre fichier .bashprofile. Le processus est exactement le même, il suffit d'inverser les noms.

**Pour compiler:** Une fois que vos chemins sont configurés, tout ce que vous aurez besoin de faire est d'aller dans votre répertoire grbl et tapez `make`. (Pour effacer tous les anciens fichiers de compilation à partir d'une version précédente, tapez `make clean` en premier.) Cette fonction devrait appeler _avr-gcc_, commencer à compiler grbl, et créer un tout nouveau firmware appelé _grbl.hex_ qui pourra ensuite être flashé dans votre Arduino.



# Pour * *{: .fa .fa-windows} Windows:
_Dernière mise à jour: 2012-01-28 par @txjammer. (Testé sous Windows XP et l'IDE Arduino r23)_

Vous pouvez utiliser l'IDE Arduino ainsi car il est livré avec "win-avr" avrgcc.


**Vous devez ajouter les chemins des exécutables** comme make.exe et avrdude.exe aux variables de l'environnement windows.  Faites un clic droit sur ```mon ordinateur``` dans le menu Démarrer et cliquez sur Propriétés. Aller à l'onglet Avancé et sur le fond, il y aura un bouton qui dit variables d'environnement. Sous variables système, il y aura une variable avec le nom ```Chemin```. Cliquez éditer et ajouter les chemins de l'exécutable, exemple : _C:\arduino-00xx\hardware\tools\avr\bin;C:\arduino-00xx\hardware\tools\avr\avr\bin;C:\arduino-00xx\hardware\tools\avr\utils\bin_. Ne pas effacer vos chemins précédents, il suffit d'ajouter les nouveaux. Une fois ceci fait, vous pouvez compiler le code source.

###Pour Windows 7 et Arduino 1.5.7

*Ajouter les chemins suivants à votre variable PATH* - veillez à inclure ; après chaque entrée, sauf le dernier dans votre PATH entrée variable.

`C:\Program Files (x86)\Arduino\Hardware\tools\avr\avr\bin\`

`C:\Program Files (x86)\Arduino\Hardware\tools\avr\bin\`

`C:\Program Files (x86)\Arduino\HARDWARE\Arduino\sam\system\CMSIS\Exemples\cmsisexample\gccatmel`

`C:\Program Files (x86)\Arduino`

Vous aurez très probablement besoin de redémarrer votre ordinateur afin que Windows puisse prendre en compte et reconnaître les chemins nouvellement ajoutés.

***

Une fois que vos chemins ont étés mis à jour, vous pouvez ouvrir une invite de commande et faites cela:

Cliquez sur Démarrer, dans la boîte de dialogue Exécuter, tapez `cmd` ou trouver l'invite de commande dans votre menu de démarrage, généralement dans Démarrer -> Programmes -> Accessoires.

Changez votre répertoire de travail dans le répertoire qui contient le code source de grbl:

    cd C:\gbrlpath

taper

    make clean

Vous devriez obtenir ceci:

    rm -f grbl.hex main.elf main.o motion_control.o gcode.o spindle_control
    t_control.o serial.o protocol.o stepper.o eeprom.o settings.o planner.o
    ts.o limits.o print.o probe.o report.o system.o main.d motion_control.d
    spindle_control.d coolant_control.d serial.d protocol.d stepper.d eepro
    ngs.d planner.d nuts_bolts.d limits.d print.d probe.d report.d system.d`

Tapez:

    make grbl.hex

ou simplement:

    make

Si tout va bien grbl.hex devrait être créé et vous pourrez le télécharger sur votre atmega328p en utilisant avrdude. Pour obtenir des instructions sur la façon de flasher votre Arduino avec le fichier grbl.hex nouvellement créer, voir [cette entrée wiki](flasher_grbl.html)

**Ruby est facultatif, mais si vous ne modifiez pas le fichier Makefile vous devrez télécharger Ruby** et ajouter dans les paramètres d'installation le chemin des variables d'environnement. Ensuite, vous pourrez compilé la source complète avec le calcul du flash. Si vous ne souhaitez pas installer Ruby, éditez le Makefile (enlever) tout ce qui est après Ruby (sur la ligne 84 seulement).

**Une alternative consiste à utiliser Atmel Studio**, une version customisée de Visual Studio.

_Dernière mise à jour: 18/07/2014 par @gerritv (testé sous Windows 8.1, 64bits)_

* Installer Atmel studio
* Installer l'extension ```Créer à partir de Makefile``` (Outils/Extension Manager)
* Démarrer Outils/Créer projets de Makefile
* Sélectionner le Makefile à partir de votre répertoire de code grbl
* Sélectionner un périphérique, utilisez ATmega328p pour l'Arduino Uno
* Dans Projets/Propriétés, décochez la case ```Utilisez Makefile externe```
* Ajouter -DFCPU=16000000 -mmcu=atmega328p de Projet/Propriétés/Toolchain/AVR compilateur Gnu/Divers Autres drapeaux

Les 2 dernières étapes doivent être faites pour les deux configurations de débogage et de sortie

Profitez des avantages de Visual Studio pour Atmel/AVR

# Pour * *{: .fa .fa-linux} Linux:
_Dernière mise à jour: 2012-03-02 par @speters. (Testé sur ???)_

Assurez-vous que vous avez les bibliothèques requises installées:  _avr-gcc_ et _arduino_ (_sudo aptitude install arduino_)

Dans le terminal, remplacez les répertoires où se trouve le code source _grbl_.

Ensuite, tapez la commande suivante pour compiler et construire le firmware:

    make clean
    make grbl.hex

# Pour Ubuntu:
_Dernière mise à jour: 20/01/2014 par @EliteEng._

Les instructions ci-dessous ont étés testé sous Ubuntu 11.10 avec une carte Arduino Uno.  Cela vous permettra de compiler grbl à partir du code source et le flasher sur votre Arduino.  Ces instructions devraient, en théorie, fonctionner avec d'autres version de Debian.

Sur une nouvelle installation de ubuntu, le processus d'installation va se dérouler comme ceci:

1) Installez l'outil de construction AVR en exécutant:

    sudo apt-get install arduino-core make unzip

2) Compiler le code source GRBL et créer le fichier du firmware:

    cd /home ou l'emplacement ou vous souhaitez télécharger le code source.
    wget https://github.com/grbl/grbl/archive/master.zip
    unzip master.zip
    cd grbl-master
    sudo make grbl.hex

3) Pour flasher le firmware sur votre Arduino Uno, branchez l'Arduino en utilisant le câble USB (Vérifiez que l'appareil se trouve bien dans /dev/ttyACM0 et exécutez la commande suivante:

    sudo PROGRAMMER="- c arduino -P /dev /ttyACM0" make flash

Ça y est, le firmware devrait maintenant être installé sur votre Arduino.


Autres références:

* **[DANK](http://dank.bengler.no/-/page/show/5471gettinggrbl)** _(Dernière mise à jour 2/2011)_