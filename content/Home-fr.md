
_Bienvenue sur le wiki de grbl! Vous êtes libre de modifier ces pages pour aider à garder grbl à jour!_

### A propos de Grbl

Grbl est un logiciel gratuit, open source, et de haute performance. Il sert à contrôler le mouvement des machines CNC à l'aide d'un Arduino.

La plupart des imprimantes 3D open source utilise Grbl dans leurs cœurs. Il a été adapté pour être utilisé dans des centaines de projets, y compris la découpe au laser, l'écriture automatiques, les perceuses de précision, les graffitis... Grâce à sa performance, la simplicité et les exigences matérielles frugal, Grbl a grandi dans un petit phénomène open source.

En 2009, [Simen Svale Skogsrud](http://bengler.no/grbl) a honoré la communauté open-source en écrivant et en libérant les premières versions de Grbl pour tout le monde ([inspiré par l'Interpréteur G-Code Arduino créer par Mike Ellery](http://www.contraptor.org/grbl-gcode-interpreter)). Depuis 2011, Grbl va de l'avant mené par la communauté open-source sous la direction pragmatique de "Ph.D. Sungeun K. Jeon" (@chamnit).

-----

### Supporter officiel du projet Grbl CNC

[![Inventables](img/inventables-logo.svg)](https://www.inventables.com){: .supLogo .inventables}
[![Panucatt Devices](img/panucatt2-logo.svg)](http://www.panucatt.com/){: .supLogo .panucatt}
[![OpenBuilds](img/openbuilds-logo.svg)](http://www.openbuilds.com){: .supLogo .openbuilds}
[![Carbide](img/carbide-logo.svg)](http://www.carbide3d.com){: .supLogo .carbide}


_Grbl est un projet open-source et alimentée par le temps libre de nos administrateurs et utilisateurs intrépides altruistes. Si vous souhaitez faire un don, tous les profits seront utilisés pour aider à garder ce projet durable et pour financer l'équipement nécessaire. Merci!_

[![Faire un don](img/contribute-bt.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=s-xclick&hostedbuttonid=EBQWAWQAAT878){: .btTip}

-----

### Qui peut utiliser Grbl

Les Bricoleurs qui font du fraisage et ont besoin de bon résultat avec une simple contrôleur pour leur système comme l'omniprésent Arduino Uno. Les gens qui détestent encombrer leur espace des grosse tour PC juste pour le port parallèle. Les Bricoleurs qui ont besoin d'un contrôleur écrit proprement comme base pour leur projet.

### Super caractéristiques

Grbl est prêt pour la production de produit final. Nous l'utilisons pour tous nos fraisage, en l'exécutant à partir de nos ordinateurs portables utilisant des interfaces graphiques écrites par l'utilisateur ou avec un simple script console (inclus) pour écouter le G-code. Il est écrit en C optimisé en utilisant toutes les fonctionnalités intelligentes des puces ATMega328P de l'Arduino pour atteindre un timing précis et un fonctionnement asynchrone. Il est capable d'atteindre un taux de pas de plus de 30kHz et cela proprement, un flux ??? des impulsions de controles.

Grbl est conçu pour des machines à trois axes. Aucun des axes en rotation (pour le moment) - simplement X, Y et Z.

L'interpréteur G-Code implémente un sous-ensemble de standard NIST rs274/ngc et il est testé avec la sortie d'un certain nombre d'outils CAM sans aucun problème. Les mouvements linéaire, circulaire et hélicoïdale sont entièrement pris en charge.

* G-codes pris en charge dans la version **v0.9i** :
 * **G38.3, G38.4, G38.5:** _Probing_
 * **G40:** _Cutter Radius Compensation Modes_
 * **G61:** _Path Control Modes_
 * **G91.1:** _Arc IJK Distance Modes_
* G-Codes pris en charge dans la version **v0.9h** :
 * **G38.2:** _Probing_
 * **G43.1, G49:** _Dynamic Tool Length Offsets_
* G-Codes pris en charge dans les versions **v0.8** (et **v0.9**)
 * **G0, G1:** _Linear Motions_
 * **G2, G3:** _Arc and Helical Motions_
 * **G4:** _Dwell_
 * **G10 L2, G10 L20:** _Set Work Coordinate Offsets_
 * **G17, G18, G19:** _Plane Selection_
 * **G20, G21:** _Units_
 * **G28, G30:** _Go to Pre-Defined Position_
 * **G28.1, G30.1:** _Set Pre-Defined Position_
 * **G53:** _Move in Absolute Coordinates_
 * **G54, G55, G56, G57, G58, G59:** _Work Coordinate Systems_
 * **G80:** _Motion Mode Cancel_
 * **G90, G91:** _Distance Modes_
 * **G92:** _Coordinate Offset_
 * **G92.1:** _Clear Coordinate System Offsets_
 * **G93, G94:** _Feedrate Modes_
 * **M0, M2, M30:** _Program Pause and End_
 * **M3, M4, M5:** _Spindle Control_
 * **M8, M9:** _Coolant Control_

La plupart des options de configuration peuvent être définies lors de l'exécution et sont sauvegardées dans l'EEPROM entre les sessions et même conservé entre les différentes versions de Grbl que vous mettez à niveau. Pour une description de ces G-Codes, nous suivons les [directives G-Codes NIST](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.15.7813) et LinuxCNC.org fournit également une grande documentation pour ces derniers. [(G-code)](http://www.linuxcnc.org/docs/2.5/html/gcode/gcode.html), [(M-codes)](http://www.linuxcnc.org/docs/2.5/html/gcode/m-code.html), [(autres codes)](http://www.linuxcnc.org/docs/2.5/html/gcode/other-code.html) et le [Wiki Shapeoko]( http://www.shapeoko.com/wiki/index.php/G-Code) tente de lister tous les codes pris en charge par Grbl avec les commentaires approprié.

### La gestion de l'accélération

Dans les premiers jours, les contrôleurs CNC basés sur Arduino ne disposaient pas de planification de l'accélération et ne pouvaient pas bouger à pleine vitesse sans une certaine forme d'assouplissement. La gestion Grbl constante de l'accélération avec le regard avant planificateur résolu cette question et a été reproduit partout dans le micro-contrôleur CNC monde, de Marlin à TinyG. Grbl utilise intentionnellement un modèle simple d'accélération constante, ce qui est plus que suffisant pour l'utilisation d'une CNC maison. Pour cette raison, nous avons concentrer notre temps à optimiser nos algorithmes de planification et assurant que les mouvements sont solides et fiables. Lorsque l'installation de tous les ensembles de fonctionnalités nous jugeons critique sont complets et ne nous oblige à modifier notre planificateur pour les accueillir, nous avons l'intention de rechercher et mettre en œuvre plus avancés algorithmes de commande de mouvement, qui sont habituellement réservés pour les machines avec de très haute taux d'alimentation (c.-à-pick-and-place) ou dans des environnements de production. Enfin, voici un [lien](http://onehossshay.wordpress.com/2011/09/24/improvinggrblcorneringalgorithm/) décrivant la base de notre algorithme de coins à grande vitesse afin de faciliter les mouvements dans les taux d'alimentation les plus rapides et les freins avant des coins pointus pour rapide mais branler fonctionnement.

### Limitations de la conception

Nous avons limité G-Code-support par la conception. Cela permet de maintenir le code source Grbl simple, léger et flexible, alors que nous continuons à développer, améliorer et maintenir la stabilité de chaque nouvelle fonctionnalité. Grbl supporte toutes les opérations courantes rencontrées dans la production des Outils-CAM, mais laisse quelques G-codeurs frustrés. Aucune variables, aucune base de données d'outils, pas de fonctions, pas de cycles fixes, pas de l'arithmétique et pas de structures de contrôle. Juste les opérations et les capacités de base de la machine. Rien de très complexe, nous pensons que les interfaces peuvent manipuler cela assez facilement et les traduire pour Grbl.

### Nouvelles fonctionnalités de la version 0.9 !
C'est-il vraiment passé une année et demi depuis la dernière version? Oh que le temps passe vite .. Quoi qu'il en soit, cette mise à jour est **énorme**! La stabilité et la performance a été perfectionné et aiguisés pour faire que Grbl est des productions de qualité. Voici un résumé des nouvelles modifications:

* **IMPORTANT:**
 * **Le débit sériel par défaut est maintenant de 115200! (Ancienement de 9600)**
 * **L'entrée du limite Axe-Z sur D11 est échangé avec une broche D12 permettra de soutenir la sortie de la broche PWM variable.**
 * **Aucun "Etat de file d'attente": la file d'attente a été retirée parce qu'elle est redondante. Dès maintenant, suspendre Grbl et seules les commandes en temps réel sont autorisées. Démarrer un cycle, reprendre un cycle, réinitialiser, et sortir.**
* **NOUVEAU Algorithme de mouvement des moteurs super doux:** refonte complète de la manutention du pilote pas à pas pour simplifier et réduire le temps de travail par ISR tick. Opération beaucoup plus lisse avec la nouvelle "Adaptive Multi-Axis Step Smoothing" (AMASS) algorithme qui fait ce que son nom l'indique (voir la source stepper.c pour plus de détails). Les utilisateurs doivent immédiatement voir des améliorations significatives dans la façon dont leurs machines se déplacent et dans la performance globale!
* **Stabilité et robustesse mises à jour:** la stabilité globale de Grbl a été le point principal de cette version. L'interface de planificateur et l'exécution de pas a été complètement ré-écrit pour la robustesse et l'incorruptibilité par l'introduction d'un tampon de segment étape intermédiaire que des mesures "checks-out" à partir de la mémoire tampon de planification en temps réel. Cela signifie que nous pouvons maintenant sans crainte piloter Grbl à des limites plus haute. Combiné avec le nouvel algorithme pas à pas et planificateur d'optimisations, cela se traduit par "5x à 10x" d'augmentations globales des performance dans notre test! En outre, des tests de stabilité et de robustesse ont été rapportés à prendre facilement 1,4 millions (oui, "Million") de ligne de programmes G-code comme un champion!
* **(x4) + Plannificateur plus rapide:** calculs de planification amélioré de quatre fois ou plus par l'optimisation des opérations de bout en bout, qui comprenait la rationalisation des calculs et de l'introduction d'un pointeur de planificateur pour localiser les parties non améliorables de la mémoire tampon et ne pas gaspiller des cycles pour les recalculer.
* **Vitesse variable d'outil rotatif:** Permet une sortie matériel PWM pour les commandes G-code `S`. "NOTE:" Cette fonction nécessite d'échanger le `PIN` Z-Limit **D11** avec le `PIN` Spindle enable **D12** pour accéder à la PWM marériel sur le `PIN` **D12**. Le PIN Z-limite, maintenant sur D12, devrait fonctionner comme avant.
* **Compiler/Compilable via l'IDE Arduino !:** le code source de Grbl peut maintenant être téléchargé et modifié, et ensuite être compilé et flashé directement via l'IDE Arduino, qui devrait fonctionner sur toutes les plateformes. Voir le Wiki pour plus de détails sur la marche à suivre.
* **G-Code Analyseur révision:** complètement ré-écrit à partir de zéro et pour être à 100% en conformité avec les standard G-Code ("Partie de la norme NIST sont un peu désuètes et arbitraire, donc nous avons modifié certaines petites choses pour leurs donner plus de sens. Les différences sont décrites dans le code source.) Nous avons également pris des mesures pour nous permettre de briser l'analyseur G-Code en tâches séparées et distinctes, ce qui est essentiel pour certaines idées et améliorations futures de développement.
* **Réglages d'accélération et de vitesse des indépendants:** Chaque axes peut être définies avec des paramètres d'accélération et de vitesse uniques et Grbl pourra automatiquement calculer l'accélération et la vitesse maximale à travers un chemin en dépendant de la direction.
* **Limites soft:** Vérifie si une commande de mouvement dépasse les limites de l'espace de travail avant de l'exécuter, et les alarmes eteintes, si elle est détectée.
* **Sondage | Probing:** Les commandes G-Code G38.2, G38.3, G38.4, & G38.5 sonde droite sont maintenant supportées et reliées par le PIN A5.
* **Décalages de longueur d'outil:** Le sondage (probing) n'a pas de sens sans compensation de longueur d'outil (TLO), donc nous l'avons ajouté! Les commandes d'annulations G43.1 dynamique TLO (décrit par linuxcnc.org) et G49 TLO sont désormais pris en charge. G43.1 dynamique TLO fonctionne comme le G43 TLO normale (NON SUPPORTER) mais nécessite un mot d'axe supplémentaire avec la valeur de décalage ci-joint. Nous avons fait cela de manière que Grbl n'est pas à suivre et maintenir une base de données de correction d'outil dans sa mémoire. Peut-être dans l'avenir, nous allons ajouter une base de données d'outil, mais pas pour cette version.
* **Amélioration de la performance Arc:** Plus le rayon de l'arc sera grand, plus rapidement Grbl le tracera! Nous sommes en train de définir des arcs en termes de tolérance corde à l'arc, plutôt que d'une longueur de segment fixe. Cela adapte automatiquement la longueur de segment d'arc de telle sorte que l'erreur radiale maximale du segment de la véritable arc est jamais plus que la valeur de la tolérance à la corde d'un défaut de 0,002 mm super précis.
* **Nouveau simulateur Grbl! (par @jgeisler et @ashelly):** Une enveloppe complètement indépendante du code source principale Grbl qui peut être compilé comme un exécutable sur un ordinateur. Aucun Arduino nécessaire. Simule simplement les réponses de Grbl comme si elle était sur un Arduino.
* **CPU Pin Mapping:** Dans un effort pour que Grbl soit compatible avec d'autres architectures AVR, tels que 1280 ou 2560, un nouveau fichier de configuration des broches de cpumap.h a été créé pour permettre à Grbl d'être compilé pour eux. Ceci est actuellement pris en charge par l'utilisateur, de sorte que votre kilométrage peut varier. Si vous rencontrez un bug, s'il vous plaît laissez-nous savoir, ou mieux, envoyez-nous un correctif! Merci d'avance!
* **Rapport d'état en temps réel configurable:** Les utilisateurs peuvent désormais personnaliser le type de données en temps réel que Grbl enverra quand ils émettent un '?' rapport de situation (status report). Cela comprend des données telles que: position de la machine, la position de travail, l'utilisation de la mémoire tampon de planificateur, l'utilisation du tampon série RX.
* **Routine Homing Mise à jour:** Définit le volume de l'espace de travail dans tout l'espace négatif indépendamment de la position de fin de course. Communs sur les machines CNC Pro. Mais, le comportement peut être modifié par une option de compilation temps si. Maintenant lié directement dans les principaux modules de planificateur et pas à pas pour réduire l'espace de flash et permettre des vitesses maximales lors de la recherche.
* **CoreXY Support:** Grbl prend désormais en charge la cinématique CoreXY sur un niveau d'introduction. La plupart des fonctions ont été vérifiées à travailler, mais il peut y avoir des bugs ici ou là. S'il vous plaît signaler les problèmes que vous trouverez!
* **Soutenir porte de sécurité:** interrupteurs pour portes de sécurité sont maintenant supportés. Grbl va forcer un flux attente, arrêt de la broche et du liquide de refroidissement, et attendez que l'interrupteur de la porte est fermée et que l'utilisateur a émis un curriculum vitae. À la reprise, la broche et arrosage seront re-dynamiser après un délai configurable et continuer. Utile pour les OEM qui exigent cette fonctionnalité.
* **Limite complet et contrôle Pin Configurabilité:** Limites et le fonctionnement des broches de commande peuvent maintenant être interprétés par Grbl comme vous le souhaitez, avec les résistances de pull-up internes activées ou désactivées, ou la lecture d'un haut ou bas comme un déclencheur. Cela devrait couvrir tout le câblage et NO ou NC scénarios de commutation.
* **Option Partage Pin Limite:** Fin de course peut être combiné à partager les mêmes broches pour libérer broches O à d'autres fins précieuse I /. Lorsqu'ils sont combinés, les utilisateurs doivent ajuster le masque de cycle de ralliement dans config.h pas à la maison les axes sur un axe commun dans le même temps. Ne vous inquiétez pas; limites dures et le cycle de prise d'origine fonctionnent toujours comme ils le faisaient avant.
* **La compilation caractéristique supplémentaire Options:** Limiter / contrôle de rapports d'état de la broche, numéro de ligne suivi, reporting en temps réel du taux d'alimentation, et plus encore.


### Nouvelles fonctionnalitées de la v0.8:
Beaucoup de choses se sont passé depuis la v0.7. Nous travaillons très dur pour créer un contrôleur CNC simple, mais puissant pour le vénérable Arduino. Voici une liste des nouvelles choses qui sont venus avec la v0.8.

* **Commandes "Run-times" multi-tâches:** Mise en pause avec décélération contrôlée pour aucune perte de l'emplacement, reprendre après la mise en pause du flux, Reset, et le rapport d'état.
* **Avancée Homing Cycle:** Beaucoup d'options de configuration pour différents types de machines dont les axes se déplace quand ils recherchent leurs directions. Les interrupteurs de fins de course peuvent également être utilisés comme des limites strictes.
* **Persistent Coordonner données du système:** Coordonnées du système de travail (G54-G59) et les positions prédéfinies (G28, G30) sont maintenues dans l'EEPROM, donc ils vont toujours être réglé comme vous les avez défini la dernière fois.
* **Mode de vérification G-Code:** Permet de configurer Grbl de lancer votre programme sans bouger quoi que ce soit, de sorte que vous puissiez vérifier si oui ou non vous avez des erreurs que Grbl n'appreciera peut-etre pas!
* **Rapports améliorer:** Rapports de position en temps réel, ce qui Grbl fait, l'état de l'analyseur G-code, et coordonnées de valeurs de décalage stockés.
* **Blocs de démarrage:** exécute un G-Code utilisateur auto-magiquement utilisateur au démarrage ou réinitialiser. Peut être utilisé pour définir vos paramètres par défaut.
* **Pin-outs:** début de cycle, pause et abandonner sont maintenant épinglé sur les broches A0, A1, et A2. Il suffit de connecter un commutateur "normalement ouvert" à la broche et la masse. C'est tout!
* Analyseur G-Code plus robuste avec contrôle de vérification d'erreur.
* Et bien plus!


### Plan pour le développement futur de la v1.0
Grbl est constamment en cours de développement et de nouvelles fonctionnalités sont ajoutés régulierement. Voici une courte liste des fonctionnalités à venir prévues pour la prochaine version, mais cliquez sur ce [lien](https://github.com/grbl/grbl/wiki/Development-Path-and-Future-Needs) pour lire les détails.

* **Remplacements:** l'ajustement en temps réel des avances, vitesse de broche, et liquide de refroidissement pendant un cycle. Nous avons résolu et juste besoin de comprendre quelques petits détails avant de l'installer.
* **Mouvement:** Mettre en place une interface facile pour les utilisateurs pour faire des mouvement appropriée.
* **Compensation de jeu:** Pas sûr que ça va faire dans la version Uno, mais certainement lorsque nous soutenons une plus grande contrôleur.

***
_Grbl v0.8 (et avant) est distribué sous la licence MIT et a été développé par Simen Svale Skogsrud, Sungeun K. Jeon Ph.D., et Jens Geisler._

_Grbl v0.9 est distribué sous la licence GPLv3 et est développé par Sungeun K.Jeon Ph.D .. Voir [Licence](https://github.com/grbl/grbl/wiki/Licensing) pour plus de détails._
***

## Aide
* **[FAQ](https://github.com/grbl/grbl/wiki/Frequently-Asked-Questions)**

* **[License](https://github.com/grbl/grbl/wiki/Licensing)**

* **[Compiler Grbl](https://github.com/grbl/grbl/wiki/Compiling-Grbl)**

* **[Flasher Grbl sur un Arduino](https://github.com/grbl/grbl/wiki/Flashing-Grbl-to-an-Arduino)**

* **[Connecter Grbl](https://github.com/grbl/grbl/wiki/Connecting-Grbl)**

* **[Configurer Grbl v0.9](https://github.com/grbl/grbl/wiki/Configuring-Grbl-v0.9)**

* **[Configurer Grbl v0.8](https://github.com/grbl/grbl/wiki/Configuring-Grbl-v0.8)**

* **[Configurer Grbl v0.7](https://github.com/grbl/grbl/wiki/Configuring-Grbl-v0.7)**

* **[Utilisation de Grbl](https://github.com/grbl/grbl/wiki/Using-Grbl)**

* **[L'interfaçage avec Grbl](https://github.com/grbl/grbl/wiki/Interfacing-with-Grbl)**

* **[Exemples G-code](https://github.com/grbl/grbl/wiki/G-Code-Examples)**

* **[Bugs connus](https://github.com/grbl/grbl/wiki/Known-Bugs)**