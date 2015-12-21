#### _Liens rapides:_
* [Démarrage](#start)
* [Paramètres Grbl](#settingsGrbl)
* [Les Paramètres Grbl et leurs explications](#xva)
* [Les autres commandes '$' de Grbl](#grbls-other--commands)
* [Commandes en temps réel](#real-time)

***

<a href="">test</a>


***

## Démarrage

Tout d'abord, onnectez vous à Grbl à l'aide du terminal série de votre choix. 

Réglez la vitesse de transmission à "115200" comme 8-N-1 (8-bits, pas de parité et 1 bit d'arrêt.) 

Une fois connecté vous devriez obtenir une réponse Grbl qui ressemble à ceci:

    Grbl 0.9i ['$' for help]

Tapez $ et appuyez sur Entrée pour que Grbl imprime le message d'aide. Vous ne devriez pas voir toute l'écho local de la commande $ et entrez. Grbl devrait répondre par:

    $$ (view Grbl settings | afficher les réglages Grbl)
    $ (view parameters | voir paramètres)
    $G (view parser state | voir l'état de l'analyseur)
    $I (view build info | voir les infos de la version)
    $N (view startup blocks | voir les blocs de démarrage)
    $x=value (save Grbl setting | sauvegarder les paramètres Grbl)
    $Nx=line (save startup block | sauvegarder le bloc de démarrage)
    $C (check gcode mode | vérifier le mode gcode)
    $X (kill alarm lock | éteindre le blocage alarme)
    $H (run homing cycle | démarrer le cycle homing)
    ~ (cycle start | début du cycle)
    ! (feed hold | flux en attente)
    ? (current status | état actuel)
    ctrl-x (reset Grbl | réinitialiser Grbl)

Le commandes `$` sont des commandes système Grbl utilisées pour peaufiner les réglages, afficher ou modifier les états de Grbl et ses modes de fonctionnement, et commencer un cycle de homing/ralliement. Le quatre dernières commandes **non $** sont des commandes de contrôle en temps réel qui peuvent être envoyés à tout moment, quel que soit l'état de Grbl. Ceux-ci vont immédiatement changer le comportement de fonctionnement de Grbl ou immédiatement imprimer un rapport des données importantes en temps réel, comme la position actuelle (aka DRO).

***

## Réglages Grbl

#### $$ - View Grbl settings | Afficher les réglages Grbl
Pour afficher les réglages, tapez `$$` et appuyez sur Entrée une fois connecter à Grbl. Grbl doit répondre avec la liste des paramètres actuels du système, comme le montre l'exemple ci-dessous. Tous ces paramètres sont persistant et conservés dans la mémoire EEPROM, donc si vous éteignez, ceux-ci seront chargé à nouveau jusqu'à la prochaine fois que vous allumerez votre Arduino.


    $0=10 (step pulse, usec | impulsion de pas en micro-secondes)
    $1=25 (step idle delay, msec | délai de retard de pas en msec)
    $2=0 (step port invert mask:00000000)
    $3=6 (dir port invert mask:00000110)
    $4=0 (step enable invert, bool | inverser l'activation de pas, bool)
    $5=0 (limit pins invert, bool | inverser les broches de fin de course, bool)
    $6=0 (probe pin invert, bool | inverser la broche de sonde, bool)
    $10=3 (status report mask:00000011)
    $11=0.020 (junction deviation, mm)
    $12=0.002 (arc tolerance, mm)
    $13=0 (report inches, bool)
    $20=0 (soft limits, bool)
    $21=0 (hard limits, bool)
    $22=0 (homing cycle, bool)
    $23=1 (homing dir invert mask:00000001)
    $24=50.000 (homing feed, mm/min)
    $25=635.000 (homing seek, mm/min)
    $26=250 (homing debounce, msec)
    $27=1.000 (homing pull-off, mm)
    $100=314.961 (x, step/mm)
    $101=314.961 (y, step/mm)
    $102=314.961 (z, step/mm)
    $110=635.000 (x max rate, mm/min)
    $111=635.000 (y max rate, mm/min)
    $112=635.000 (z max rate, mm/min)
    $120=50.000 (x accel, mm/sec^2)
    $121=50.000 (y accel, mm/sec^2)
    $122=50.000 (z accel, mm/sec^2)
    $130=225.000 (x max travel, mm)
    $131=125.000 (y max travel, mm)
    $132=170.000 (z max travel, mm)


#### $x=val - Save Grbl setting

La commande `$x=val` enregistre ou modifie un paramètre Grbl, qui peut être fait manuellement en envoyant cette commande lorsque vous êtes connecté à Grbl grâce à un programme de terminal série, mais la plupart des GUI Grbl vont le faire pour vous comme une caractéristique conviviale. 

Pour modifier manuellement l'option d'impulsions de pas en 10 microsecondes par exemple, vous pouvez taper ceci , suivie d'entrer:

    $0=10

Si tout va bien, Grbl répondra par un `ok` et ce paramètre sera stockée dans l'EEPROM et sera conservé indéfiniment ou jusqu'à ce que vous les modifiez une nouvelle fois. Vous pouvez vérifier si Grbl a bien reçu et stocké correctement vos réglages en tapant `$$` pour afficher à nouveau les paramètres du système.


****

## Les réglages `$x=val` de Grbl et ce qu'ils signifient.

**NOTE: La numérotation des réglages a changé depuis la version 0.8c et ceci à des fins d'évolutivité.**

#### $0 – Step pulse, microseconds | Impulsion de pas, microsecondes

Les pilotes pas-à-pas sont conçus pour une certaine longueur minimale d'impulsions de pas. Consultez la fiche technique ou essayer tout simplement quelques numéros. Vous voulez les impulsions les plus courtes que vos pilotes pas-à-pas puissent reconnaître de manière fiable. Si les impulsions sont trop longues, vous pourriez rencontrer des problèmes lors de l'exécution du système à taux de fourrage et des taux d'impulsions très élevés, parce que les impulsions de pas peuvent commencer à se chevaucher. Nous vous recommandons quelque chose autour de 10 microsecondes, ce qui est la valeur par défaut.

#### $1 - Step idle delay, msec | Délais d'attente de pas, msec

Chaque fois que vos moteurs pas-à-pas complètent un mouvement et viennent à un point d'arrêt, Grbl va retarder la désactivation des moteurs pas-à-pas par cette valeur. "OU", vous pouvez toujours garder vos axes activés (alimenter de manière à maintenir la position) en définissant cette valeur au maximum de 255 millisecondes. Encore une fois, juste pour répéter, vous pouvez garder tous les axes activé avec cela `$1=255`.

Le délai d'attente de verrouillage des moteurs pas à pas est le temps que Grbl gardera les moteurs verrouillées avant de les désactiver. Selon le système, vous pouvez définir cette valeur à zéro et ainsi la désactiver. Sur d'autres, vous devrez peut-être régler 25-50 millisecondes pour vous assurer que vos axes sont complètement stopper avant de le désactiver. Ceci est à prendre en compte pour les moteurs de vos machines qui n'aiment pas beaucoup fonctionner sur de longues périodes sans faire quelque chose. Aussi, gardez à l'esprit que certains pilotes pas à pas ne se souviennent pas à quel micro-pas ils se sont arrêtés, alors quand vous les réactivez, vous pouvez pour cette raison être confronter à une perte de pas. Dans ce cas, il suffit de garder vos moteurs toujours activés via `$1=255`.

#### $2 – Step port invert mask:binary

Ce paramètre inverse le signal d'impulsions de pas. Par défaut, un signal de pas commence à normalement-bas et va vers le haut lors d'un événement d'impulsions de pas. Après un temps d'impulsions de pas fixé par `$0`, la réinitialisation de la broche pour bas, jusqu'au prochain événement d'impulsion de pas. Quand il est inversé, le comportement de l'impulsion de pas passe de normalement-haut, à normalement-bas pendant l'impulsion, et retourne à normalement-haut. La plupart des utilisateurs n'auront pas besoin d'utiliser ce paramètre, mais cela peut être utile pour certains pilotes pas à pas CNC qui ont des exigences particulières. Par exemple, un retard artificiel entre la broche de direction et l'impulsion de pas peut être créée en inversant la broche de pas.

Ce paramètre d'inversion de masque est une valeur qui stocke les axes pour inverser comme indicateurs binaires. Vous n'avez vraiment pas besoin de comprendre complètement comment cela fonctionne. Il vous suffit de saisir la valeur des paramètres pour les axes que vous souhaitez inverser. Par exemple, si vous voulez inverser les axes X et Z, vous enverriez `$2=5` à Grbl et le réglage devrait maintenant lire `$2=5 (step port invert mask: 00000101)`.

| Valeur de réglage |  Masque  | Inverser X | Inverser Y | Inverser Z |
|:-----------------:|:--------:|:----------:|:----------:|:----------:|
|         0         | 00000000 |      N     |      N     |      N     |
|         1         | 00000001 |      Y     |      N     |      N     |
|         2         | 00000010 |      N     |      Y     |      N     |
|         3         | 00000011 |      Y     |      Y     |      N     |
|         4         | 00000100 |      N     |      N     |      Y     |
|         5         | 00000101 |      Y     |      N     |      Y     |
|         6         | 00000110 |      N     |      Y     |      Y     |
|         7         | 00000111 |      Y     |      Y     |      Y     |

#### $3 – Direction port invert mask:binary

Ce paramètre inverse le signal de direction pour chaque axe. Par défaut, Grbl suppose que les axes se déplacent dans une direction positive lorsque le signal de broche de direction est bas, et une direction négative lorsque la broche est haut. Souvent, les axes ne se déplacent pas de cette façon avec certaines machines. Ce paramètre va inverser le signal de broche de direction des axes qui se déplacent dans le sens opposé.

Ce paramètre de masque interverti fonctionne exactement comme le port de masque étape inverti et magasins qui axes pour inverser comme indicateurs binaires. Pour configurer ce paramètre, il vous suffit d'envoyer la valeur pour les axes que vous souhaitez inverser. Utilisez le tableau ci-dessus. Par exemple, si vous voulez inverser la direction de l'axe Y uniquement, vous enverrez `$3=2` à Grbl et le réglage devrait maintenant lire `$3=2 (dir port invert mask: 00000010)`

#### $4 - Step enable invert, bool

Par défaut, la broche d'activation stepper est haut pour désactiver et bas pour l'activée. Si votre installation a besoin du contraire, inverser simplement la broche d'activation stepper en tapant `$4=1`. Désactiver avec `$4=0`. (Peut nécessiter une réinitialisation pour charger les modifications.)

#### $5 -  Limit pins invert, bool

Par défaut, les broches de fin de course sont maintenue normalement-haut avec une résistance pull-up interne de l'Arduino. Quand une broche de fin de course est bas, Grbl interprète cela comme déclenchée. Pour le comportement opposé, juste inverser les broches de fin de course en tapant `$5=1`. Désactiver avec `$5=0`. (Peut nécessiter une réinitialisation pour charger les modifications.) 

**NOTE: Si vous inversez les broches de vos fin de course, vous aurez besoin d'une résistance pull-down reliée à toutes les broches de fin de course pour éviter de surcharger les broches avec le courant et ainsi les faire griller.**

#### $6 -  Probe pin invert, bool

Par défaut, la broche de sonde est maintenue normalement-haute avec une résistance de pull-up interne à l'Arduino. Lorsque la broche de sonde est bas, Grbl interprète cela comme déclenché. Pour le comportement opposé, juste inverser la broche de la sonde en tapant `$6=1`. Désactiver avec `$6=0`. (Peut nécessiter une réinitialisation pour charger les modifications.) 

**NOTE: Si vous inversez votre broche de sonde, vous aurez besoin d'une résistance externe relié à la broche de la sonde pour éviter la surcharge avec le courant et la faire griller.**


#### $10 - Status report mask:binary

Ce paramètre détermine qu'elles données Grbl enverra en temps réel à l'utilisateur quand un `?` status report is sent. Par défaut, Grbl renverra son état de marche (ne peut pas être désactivé), la position de la machine, et la position de travail (position de la machine avec les décalages de coordonnées et d'autres compensations appliquée). Trois fonctions de reporting supplémentaires sont disponibles qui seront utiles pour les interfaces ou pour les utilisateurs qui mettent en place leurs machines, qui incluent le tampon série RX, l'utilisation du tampon de bloc du planificateur, et les états des broches de fin de course (bas ou haut, affichés dans l'ordre ZYX). 

Pour les définir, utilisez le tableau ci-dessous afin de déterminer quelles sont les données que vous souhaitez que Grbl vous renvoi. Sélectionnez les types de rapports que vous aimeriez voir dans les rapports de situation et ajouter leurs valeurs. Ceci est la valeur que vous utilisez pour envoyer à Grbl. Par exemple, si vous avez besoin des positions de la machine et de travail, ajoutez les valeurs 1 et 2 et envoyer à Grbl `$10=3` pour le définir. Ou, si vous avez besoin des position de la machine seule et de l'état des broches de fin de course, ajoutez les valeurs 1 et 16 et envoyer à Grbl `$10=17`. 

En général, conserver ces rapports d'état en temps réel à un minimum, car il faut beaucoup de ressources pour imprimer et envoyer ces données à un taux de retour élevé. Par exemple, les rapports de broches de fin de course est généralement nécessaire uniquement lorsque les utilisateurs mettent en place leur machine. Ensuite, il est recommandé de le désactiver, car il est pas très utile une fois que vous avez tout compris.

|  Type de rapport | Valeur |
|:----------------:|:------:|
| Machine Position |    1   |
|   Work Position  |    2   |
|  Planner Buffer  |    4   |
|     RX Buffer    |    8   |
|    Limit Pins    |   16   |

#### $11 - Junction deviation, mm

L'écart de jonction est utilisée par le gestionnaire d'accélération pour déterminer à quelle vitesse il peut se déplacer à travers les jonctions de segments de ligne d'un chemin de programme G-code. Par exemple, si le chemin G-code a une tour de 10 degrés forte à venir et la machine se déplace à pleine vitesse, ce paramètre permet de déterminer combien la machine doit ralentir pour aller en toute sécurité à travers le coin sans perdre étapes.

Comment nous calculons il est un peu compliqué, mais, en général, des valeurs plus élevées donne un mouvement plus rapide dans les virages, tout en augmentant le risque de pertes de pas et de positionnement. Des valeurs inférieures rendent le gestionnaire d'accélération plus prudent et mèneront à un virage plus prudent et plus lent. Donc, si vous avez des problèmes quand votre machine essaie de prendre un virage trop vite, "diminuer" cette valeur pour faire ralentir votre machine à l'entrée des virage. Si vous voulez que votre machine se déplace plus rapidement à travers les jonctions, "augmenter" cette valeur pour l'accélérer. Pour les curieux, cliquez sur ce [lien](http://t.co/KQ5BvueY) pour en apprendre plus à propos de l'algorithme de virages Grbl, qui représente à la fois la vitesse et l'angle de jonction avec une méthode très simple, efficace, et robuste.

#### $12 – Arc tolerance, mm

Grbl construit les cercles G2/G3, les arcs et les hélices en les subdivisant en petites lignes minuscules, tels que la précision de l'arc de traçage ne soit jamais en dessous de cette valeur. Vous n'aurez probablement jamais besoin de modifier ce paramètre, puisque "0.002" est bien en dessous de la précision de la plupart des machines CNC. Mais si vous trouvez que vos cercles sont trop brut ou que le traçage de l'arc s'effectue trop lentement, modifier ce paramètre. Des valeurs inférieures donnent une plus grande précision, mais pourrons conduire à des problèmes de performance en surchargeant Grbl avec trop de lignes minuscules. Alternativement, des valeurs plus élevées retrace avec une précision inférieure, mais peuvent accélérer les performances de l'arc depuis Grbl avec moins de lignes à traiter.

Pour les curieux, la tolérance de l'arc est définie comme la distance perpendiculaire maximale d'un segment de ligne avec les points d'extrémité se trouvant sur l'arc, aka une corde. Avec une géométrie basique, on résout pour la longueur des segments de ligne à tracer l'arc qui répond à ce paramètre. Modéliser des arcs avec cette technique est très bien, parce que les segments d'arc ajustent et redimensionnent automatiquement la longueur pour assurer un tracage d'arc avec des performances optimale, tout en ne perdant jamais de précision.

#### $13 - Report inches, bool

Grbl possède la fonctionnalité de rapport de positionnement en temps réel pour fournir à l'utilisateur une retroaction sur la position précise de la machine, ainsi que les coordonnées des paramètres de compensation de longueur d'outil et de sondage. Par défaut, il est régler pour signaler cela en mm, mais en envoyant la commande `$13=1`, vous marqué ce drapeau booléen sur 'vrai' et les fonctions de reporting s'afficheront à présent en pouces. Faites à nouveau `$13=0` pour revenir en mm.

#### $20 - Soft limits, bool

Le **_Soft Limit_** est un dispositif de sécurité pour aider à empêcher votre machine de se déplacer trop loin et au-delà des limites de mouvement, et d'écraser ou casser quelque chose de cher. Il fonctionne en connaissant les limites maximales de mouvement pour chaque axe et où Grbl est en coordonnées machine. Chaque fois qu'une nouvelle commande de mouvement G-code" est envoyé à Grbl, il vérifie si oui ou non vous allez accidentellement dépassé les limites d'espace de la machine. Si vous le faites, Grbl s'arrêtera immédiatemment à l'endroit ou il est, stoppera l'outil rotatif et le système de refroidissement, puis lancera l'alarme du système indiquant le problème. La position de la machine sera conservé, car il n'est pas du à un arrêt immédiat forcé comme avec les limites strictes.

**NOTE: Le **_Soft Limit_** nécessite que le 'homing' soit activé et que les paramètres de mouvement d'axe maximales précises, parce Grbl à besoin de savoir où il est. `$20=1` pour activer, et `$20=0` pour désactiver.**

#### $21 - Hard limits, bool

Les llimite stricte fonctionne de la même façon que **_Soft Limit_**, mais utilise des commutateurs physiques en lieu et place. Fondamentalement, vous câbler quelques interrupteurs (mécaniques, magnétiques ou optiques) proche de la fin de mouvement de chaque axe, ou si vous sentez qu'il pourrait y avoir des problèmes si votre programme se déplace trop vers où elle ne devrait pas. Lorsque l'interrupteur se déclenche, il va immédiatement arrêter tout mouvement, l'arrêt du système de refroidissement et de l'outil rotatif (si connecté), et passera directement en mode d'alarme, ce qui vous obligera à vérifier votre machine et à tout réinitialiser.

Pour utiliser les limites stricte avec Grbl, les broches de fin de course sont maintenue élevé avec une résistance pull-up interne, de façon que tous ce que vous ayez à faire est de cablé un interrupteur normalement ouvert avec la broche et la masse et activer les limites strictes avec `$21=1`. (Désactiver avec `$21=0`.) Nous vous conseillons fortement de prendre des mesures de prévention d'interférence électrique. Si vous voulez une limite pour les deux extrémités de mouvement d'un des axes, connecter juste deux interrupteurs en parallèle avec la broche et la masse, si l'un d'eux s'enclenche, il activera la limite stricte.

Gardez à l'esprit, qu'un événement de fin de course matérielle est considérée comme un événement critique, où les moteurs vont s'arrêter immédiatement et auront probablement perdu les pas. Grbl n'a pas de retour sur la situation, de sorte qu'il ne peut garantir de l'endroit ou il se trouve. Donc, si une limite stricte est déclenchée, Grbl entrera en mode boucle d'alarme infinie, vous donnant une chance de vérifier votre machine et vous obligeant à réinitialiser Grbl. Rappelez-vous que c'est une fonction purement sécuritaire.

#### $22 - Homing cycle, bool

Ahh, homing. Pour ceux qui viennent de débuter avec leur CNC, le 'homing' cycle est utilisée pour localiser avec exactitude et précision une position connue et cohérente sur une machine à chaque fois que vous démarrez Grbl entre les sessions. En d'autres termes, vous savez exactement où vous êtes, à tout moment et à chaque fois. Disons que vous commencez l'usinage de quelque chose ou que vous êtes sur le point de commencer la prochaine étape d'un travail et qu'une panne de courant survient, vous redémarrer Grbl et Grbl n'a aucune idée où il est. Vous êtes de gauche avec la tâche de déterminer où vous êtes. Si vous avez 'homing', vous aurez toujours un point de référence zéro pour localiser à partir, donc tout ce que vous avez à faire est de lancer le 'homing' cycle et de reprendre là où vous vous étiez arrêté.

Pour mettre en place le 'homing' cycle pour Grbl, vous devez disposer d'interrupteurs de fin de course dans une position fixe qui ne sera pas endommager ou déplacés, ou bien votre point de référence dérégler. Habituellement, ils sont configurés dans le point le plus éloigné de X Y et Z de chaque axe. Câbler vos interrupteurs de fin de course avec les broches de fin de course et la terre, tout comme avec les limites strictes, et activer le 'homing'. Si vous êtes curieux, vous pouvez utiliser vos interrupteurs de fin de course pour les deux limites strictes et pour le 'homing'. Ils marche bien avec chacun des deux. 

Par défaut, le 'homing' cycle de Grbl déplace l'axe-Z dans le sens positif en premier pour dégager l'espace de travail, puis déplace à la fois les axes X et Y en même temps dans le sens positif. Pour configurer la façon dont votre 'homing' cycle se comporte, il y a des réglages Grbl plus bas dans la page décrivant ce qu'ils font (et la compilation des options avec.)

Encore une chose à noter, lorsque le 'homing' est activé. Grbl verrouille toutes les commandes G-Code jusqu'à ce que vous ayez effectuez le 'homing' cycle. Cela signifie aucun mouvements d'axes, à moins que le verrouillage soit désactivé ($X) mais ne expliquerons cela plus tard. La plupart, sinon tous les contrôleurs CNC, font quelque chose de semblable, car il possède la plupart du temps un dispositif de sécurité pour empêcher les utilisateurs de faire une erreur de positionnement, qui est très facile à faire et être en colère quand une erreur ruine un élément. Si vous trouvez cela gênant ou trouvez des bugs étranges, s'il vous plaît laissez-nous savoir et nous allons essayer de travailler sur cela pour que tout le monde soit heureux. :)

**NOTE: Consultez le fichier config.h pour plus d'options de 'homing' pour les utilisateurs avancés.** Vous pouvez désactiver le blocage du 'homing' au démarrage, configurer les axes qui se déplacent d'abord pendant un 'homing' cycle et dans quel ordre, et bien plus encore.


#### $23 - Homing dir invert mask, int:binary

Par défaut, Grbl suppose que vos fins de course 'homing' sont dans le sens positif, le premier déplacement dans le sens positif de l'axe-z, puis dans le sens positif des axes X-Y avant d'essayer de localiser précisément le point zéro de la machine en allant d'avant en arrière lentement autour de l'interrupteur. Si votre machine dispose d'un interrupteur de fin de course dans le sens négatif, le masque de direction 'homing' peut inverser la direction des axes. Il fonctionne exactement comme le 'step port invert' et come le 'direction port invert maks', où tout ce que vous avez à faire est d'envoyer la valeur dans le tableau pour indiquer ce que vous voulez inverser et rechercher pour la direction opposée.

#### $24 - Homing feed, mm/min

Le 'homing' cycle recherche pour les fins de course à un taux plus de recherche élevé, et après les avoir trouvés, il se déplacera à une vitesse plus lente à la recherche de l'emplacement précis du zéro machine. Homing feed rate is that slower feed rate. Set this to whatever rate value that provides repeatable and precise machine zero locating. 

#### $25 - Homing seek, mm/min

Le taux de recherche 'Homing' est le taux de recherche sur du 'homing' cycle, ou la vitesse à laquelle il tente d'abord de trouver les interrupteurs de fins de course. Réglez ce taux arrive sur les interrupteurs de fins de course dans un temps assez court, et sans écraser ou casser vos interrupteurs de fins de course si elles arrivent trop vite.

#### $26 - Homing debounce, ms

Chaque fois qu'un interrupteur se déclenchera, certains d'entre eux peuvent avoir le bruit électrique/mécanique qui fait 'rebondir' le signal haut et bas pendant quelques millisecondes avant l'emménagement. Pour résoudre ce problème, vous devez éviter les rebonds du signal, soit par le matériel avec une sorte de conditionneur de signal, soit avec le logiciel, avec un court délai pour permettre l'arrivée de rebondissement du signal. Grbl effectue un court délai, seulement 'homing' lors de la localisation du point zéro de la machine. Set this delay value to whatever your switch needs to get repeatable homing. Dans la plupart des cas, 5-25 millisecondes fonctionne très bien.

#### $27 - Homing pull-off, mm

Pour fonctionner parfaitement avec la fonction de limites strictes, 'homing' peut partager les mêmes fins de course, le 'homing' cycle va se déplacer hors de tous les interrupteurs de fin de course par ce pull-off après avoir terminé. En d'autres termes, il aide à prévenir le déclenchement accidentel de la limite dure après un 'homing' cycle. 

#### $100, $101 and $102 – [X,Y,Z] steps/mm

Grbl as besoin de savoir dans quelle mesure chaque pas prendra l'outil dans la réalité. Pour calculer les pas/mm pour un axe de votre machine, vous devez savoir:

* La distance en mm par tour de votre moteur pas à pas. Cela dépend de vos poulies d'entraînement de courroies ou du pas de vis.
* Les pas complet par révolution de vos moteurs pas à pas (généralement 200)
* Les micro-pas par pas de votre contrôleur (typiquement 1, 2, 4, 8, ou 16). _Astuce: En utilisant des valeurs élevées de micro-pas (par exemple, 16) peut réduire votre couple de moteur pas à pas, donc, utiliser des valeurs plus basse vous donnera une résolution de l'axe désiré et des propriétés de fonctionnement confortables._

Les pas/mm peut alors être calculée comme ceci: ```stepspermm = (stepsperrevolution *micro-pas)/mmperrev```

Calculer cette valeur pour chaque axe et enregistrer ces paramètres pour Grbl.

#### $110, $111 and $112 – [X,Y,Z] Max rate, mm/min

Ceci définit le taux maximal que chaque axe peut se déplacer. Chaque fois que Grbl planifie un déplacement, il vérifie si oui ou non le mouvement provoque un quelconque dépassement de ces taux maximum pour chaque axes. Si oui, il va ralentir le mouvement pour assurer qu'aucun des axes dépasse leurs limites de taux max. Cela signifie que chaque axe dispose indépendamment de sa propre vitesse, ce qui est extrêmement utile pour limiter la vitesse plus lente de l'axe-z. 

La façon la plus simple pour déterminer ces valeurs, est de tester chaque axe un à un, en augmentant lentement les réglages de fréquence maxi et ensuite le déplacer. Par exemple, pour tester l'axe-x, envoyer à Grbl quelque chose comme "G0 X50" avec assez de distance de mouvement de façon à ce que l'axe accélère à sa vitesse maximale. Vous saurez que vous avez touché le seuil de taux max lorsque vos moteurs pas à pas 'décrocheront'. Il fera un peu de bruit, mais pas d'inquiétudes, cela ne nuira pas à vos moteurs. Entrez un réglage de 10-20% en dessous de cette valeur, de sorte que vous pouvez tenir compte de l'usure, la friction et la masse de votre pièce/outil. Ensuite, répétez l'opération pour les autres axes.

**NOTE: Ce paramètre de taux max. définit également le taux de recherche G0.**

#### $120, $121, $122 – [X,Y,Z] Acceleration, mm/sec^2

Ce réglage définit les paramètres d'accélération des axes en mm/seconde/seconde. De manière simpliste, une valeur inférieure rend Grbl un mouvement plus lent, tandis qu'une valeur plus élevée donne des mouvements plus strictes et atteint des taux beaucoup plus rapide. Tout comme le réglage de la fréquence max, chaque axe dispose de sa propre valeur d'accélération et ils sont indépendants les uns des autres. Cela signifie qu'un mouvement multi-axe ne fera qu'accélérer aussi rapidement que le plus bas contribuant axe peut. 

Encore une fois, comme le réglage de la fréquence max, la façon la plus simple pour déterminer les valeurs de ce paramètre est de tester individuellement chaque axe de plus en plus lentement jusqu'à ce que le moteur cale. Puis finaliser vos réglages d'accélération avec une valeur de 10-20% en dessous de cette valeur absolue maximum. Cela devrait tenir compte de l'usure, de la friction et de l'inertie. Nous recommandons fortement de faire des essais de vos nouveaux réglages avec certains programmes G-code afin d'avoir la certitude qu'ils sont bons et pour les valider. Parfois, le chargement sur votre machine est différente lors du déplacement dans tous les axes ensemble.


#### $130, $131, $132 – [X,Y,Z] Max travel, mm

Ceci définit la distance maximale de bout en bout pour chaque axe, en mm. Ceci est utile uniquement si vous avez des **_Soft Limit_** (avec homing) activer, comme cela est utilisé uniquement par les fonction de **_Soft Limit_** de Grbl pour vérifier si vous avez dépassé les limites de la machine avec une commande de mouvement.

****

## Les autres commandes `$` de Grbl.

Les autres commandes `$` fournissent des contrôles supplémentaires pour l'utilisateur, telles que l'impression des commentaires sur l'état actuel de l'analyseur G-Code ou sur l'exécution du 'homing' cycle. Cette section explique ce que sont ces commandes et comment les utiliser.

#### `$#` - View gcode parameters | Afficher les paramètres gcode

Les paramètres G-Code stocke les coordonnées de valeurs de décalage pour G54-G59, les positions de travail prédéfinies G28/G30, les coordonnées de décalage G92, de décalage de longueur d'outil, et pour le sondage (pas officiellement, mais nous l'avons quand même ajoutè). La plupart de ces paramètres sont directement écrit dans la mémoire EEPROM à chaque fois qu'ils sont modifiés et ils sont persistants. Ce qui signifie qu'ils resteront les mêmes jusqu'à ce qu'ils soient modifiés explicitement. Les paramètres non persistants, qui ne sont pas conservés après une réinitialisation ou lors d'un redémarrage, sont G92, G43.1 compensations de longueur d'outil, et G38.2 les données de sondage.

Les coordonnées de travail G54-G59 peuvent être modifiées via la commande `G10 L2 Px` ou `G10 L20 Px` défini par la norme gcode NIST et la norme standard EMC2 (linuxcnc.org). Les positions pré-définies G28/G30 peuvent être modifiés via les commandes respective `G28.1` et `G30.1`. 

Lorsque `$#` est appelé, Grbl répondra avec les décalages enregistrés à partir des coordonnées machine pour chaque système comme suit. `TLO` désigne la compensation de longueur d'outil, et `PRB` désigne les coordonnées du dernier cycle de sondage.


    [G54:4.000,0.000,0.000]
    [G55:4.000,6.000,7.000]
    [G56:0.000,0.000,0.000]
    [G57:0.000,0.000,0.000]
    [G58:0.000,0.000,0.000]
    [G59:0.000,0.000,0.000]
    [G28:1.000,2.000,0.000]
    [G30:4.000,6.000,0.000]
    [G92:0.000,0.000,0.000]
    [TLO:0.000,0.000,0.000]
    [PRB:0.000,0.000,0.000]


#### `$G` - View gcode parser state

Cette commande imprime tous les modes G-Code actifs dans l'analyseur G-Code Grbl. Lors de l'envoi de cette commande à Grbl, il répondra avec quelque chose comme ceci:


    [G0 G54 G17 G21 G90 G94 M0 M5 M9 T0 S0.0 F500.0]


Ces modes actifs déterminent comment le prochain bloc G-code ou commande seront interprétés par l'analyseur G-Code Grbl. Pour ceux qui découvrent G-Code et l'usinage CNC, modes définit l'analyseur dans un état particulier de façon à ne pas avoir à dire constamment à l'analyseur comment analyser. Ces modes sont organisés en ensembles appelés "groupes modaux" qui ne peuvent pas être logiquement actifs en même temps. Par exemple, le groupe des unités modale définit si votre programme G-Code est interprété en pouces ou en millimètres. 

Une courte liste des groupes modaux, soutenu par Grbl, est illustré ci-dessous, mais des descriptions plus complètes et détaillées peuvent être trouvées sur le [site web de LinuxCNC](http://www.linuxcnc.org/docs/2.4/html/gcodeoverview.html#sec:Modal-Groups). Les commandes G-Code en **gras** indique les modes par défaut à la mise sous tension ou après avoir réinitialiser Grbl.

|     Modal Group Meaning    |                     Member Words                    |
|:--------------------------:|:---------------------------------------------------:|
|         Motion Mode        | **G0**, G1, G2, G3, G38.2, G38.3, G38.4, G38.5, G80 |
|  Coordinate System Select  |           **G54**, G55, G56, G57, G58, G59          |
|        Plane Select        |                  **G17**, G18, G19                  |
|        Distance Mode       |                     **G90**, G91                    |
|    Arc IJK Distance Mode   |                      **G91.1**                      |
|       Feed Rate Mode       |                     G93, **G94**                    |
|         Units Mode         |                     G20, **G21**                    |
| Cutter Radius Compensation |                       **G40**                       |
|     Tool Length Offset     |                    G43.1, **G49**                   |
|        Program Mode        |                 **M0**, M1, M2, M30                 |
|        Spindle State       |                    M3, M4, **M5**                   |
|        Coolant State       |                    M7, M8, **M9**                   |

En plus des modes de l'analyseur G-Code, Grbl signale le `T` numéro de l'outil actif, vitesse de l'outil `S`, et la vitesse du flux `F`, qui sont tous à 0 par défaut lors de la réinitialisation. Pour ceux qui sont curieux, ceux-ci ne correspondent pas tout à fait dans de beaux groupes modaux, mais sont tout aussi importants pour déterminer l'état de l'analyseur.

#### `$I` - View build info | Afficher les infos de version
Cette commande imprime la version et la date de construction du code source Grbl. Eventuellement, `$I` peut également stocker une courte chaîne pour aider à identifier avec quelle machine CNC vous communiquez, si vous avez plus d'une machine fonctionnant à l'aide de Grbl. Pour définir cette chaîne, envoyer `$I=xxx` à Grbl, où `xxx` est votre chaîne de personnalisation qui doit être inférieure à 80 caractères. La prochaine fois que vous interroger Grbl avec un `$I - view build info`, Grbl imprimera cette chaîne après la version et la date de construction. 

#### `$N` - View startup blocks | Afficher les blocs de démarrage

`$Nx` sont les blocs de démarrage que Grbl exécute chaque fois que vous allumez ou réinitialiser Grbl. En d'autres termes, un bloc de démarrage est une ligne de commandes C-Code que vous pouvez exécuté auto-magiquement au démarrage de Grbl pour configurer votre G-code par défaut, ou toute autre chose dont vous avez besoin que Grbl fasse à chaque fois que vous démarrez votre machine. Grbl peut stocker deux blocs de G-Code par défaut du système.

Donc, lorsque vous êtes connecté à Grbl, tapez `$N` puis entrez. Grbl doit répondre avec quelque chose de court, comme ca:

    $N0=
    $N1=
    ok

Pas grand chose à faire, mais cela signifie simplement qu'il n'y a pas de bloc G-Code stocké dans la ligne `$N0` pour que Grbl l'exécute au démarrage. `$N1` est la prochaine ligne à être exécuter. 

#### $Nx=line - Save startup block | Sauvegarder le bloc de démarrage

**IMPORTANT: Soyez très prudent quand vous stockez toute commandes de mouvement (G0/1, G2/3, G28/30) dans les blocs de démarrage. Ces commandes de mouvement se dérouleront chaque fois que vous réinitialisé Grbl, donc si vous avez une situation d'urgence et que vous réinitialiser ou redémarrer Grbl, un bloc de démarrage peut et va probablement aggraver les choses rapidement. Aussi, ne pas placer de commandes qui enregistrent des données sur l'EEPROM, tels que G10/G28.1/G30.1. Cela entraînera Grbl as constamment ré-écrire ces données à chaque démarrage et de réinitialisation, qui finira par user votre EEPROM Arduino.**

**L'usage typique d'un bloc de démarrage est simplement de définir vos états modaux préférés, tels que G20 inches mode, toujours par défaut pour un système de coordonnées différent, ou pour fournir un moyen à l'utilisateur d'exécuter certaine caractéristiques unique écrit par l'utilisateur lui même pour un projet perso**.

Pour définir un bloc de démarrage, taper `$N0=` suivi d'un bloc G-code valide et taper entrer. Grbl lancera le bloc pour vérifier si il est valide et ensuite répondra par un `ok` ou par une 'erreur:' pour vous indiquer si il as réussi ou si quelque chose s'est mal passé. Si il y a une erreur, Grbl ne sauvegardera pas.

Par exemple, disons que vous voulez utiliser votre premier bloc de démarrage `$N0` pour régler votre mode G-Code d'analyseur, avec les coordonnées de travail G54, G20 mode inches, et G17 XY. Vous tapez `$N0=G20 G54 G17` puis tapez entrer et vous devriez avoir la réponse `ok`. Vous pouvez ensuite vérifier si elles ont bien été sauvegarder en tapant `$N` et vous devriez maintenant voir une réponse comme `$N0=G20G54G17`.

Une fois que vous avez un bloc de démarrage stockée dans l'EEPROM, chaque fois que vous démarrer ou réinitialiser Grbl, vous verrez votre bloc de démarrage imprimé et une réponse de Grbl pour indiquer si elle bien été éxecutée. Donc, pour l'exemple précédent, vous verrez:

    Grbl 0.9i ['$' for help]
    G20G54G17ok

Si vous avez plusieurs blocs de démarrage G-Code, ils s'imprimeront de nouveau à chaque démarrage. Si vous souhaitez effacer l'un des blocs de démarrage (par exemple, le bloc 0) taper '$N0=' sans rien qui suit le signe égal.

Aussi, si vous avez le 'homing' activé, les blocs de démarrage seront exécuter immédiatement après le 'homing' cycle, pas au démarrage. 

#### `$C` - Check gcode mode | Vérifier le mode gcode

Cela bascule l'analyseur G-Code Grbl en prenant tous les blocs entrants traiter complètement, comme il le ferait en fonctionnement normal, mais il ne déplacera pas les axes, ignorera la position, et éteindra l'outil rotatif et le système de refroidissement. Ceci est conçu comme un moyen de fournir à l'utilisateur un moyen de vérifier comment sera interpréter par l'analyseur leurs nouveaux programme G-Codel et ainsi surveiller d'éventuelles erreurs (et vérifier les violations de **_Soft Limit_**, si activé).

Lorsque désactivés, Grbl effectuera un soft-reset automatique (^X). Ceci pour deux raisons. Il simplifie un peu la gestion du code. Mais, il empêche également les utilisateurs de démarrer un travail quand leurs modes G-code ne sont pas ce qu'ils pensent qu'ils sont. Une réinitialisation du système donne toujours à l'utilisateur, un démarrage frais et conforme.

#### `$X` - Kill alarm lock | Extinction du blocage d'alarme

Le mode alarme de Grbl est l'état quand quelque chose de critique s'est passé, comme une limite stricte ou une interruption pendant un cycle, ou si Grbl ne connaît pas sa position. Par défaut, si vous avez le 'homing' activé et que vous mettez sous tension l'Arduino, Grbl passe à l'état d'alarme, car il ne connaît pas sa position. Le mode alarme verrouille toutes les commandes G-Code jusqu'à ce que le `$H` 'homing' cycle est été effectuer. Ou si l'utilisateur a besoin d'éteindre le blocage de l'alarme pour déplacer les axes au-delà des limites de fins de course, par exemple, `$X` éteint le verrouillage de l'alarme et permettre aux fonctions G-Code de fonctionnée à nouveau.  

Mais, utiliser le avec précaution !! Cela ne devrait être utilisé que dans des situations d'urgence. La position sera probablement perdue, et Grbl ne sera peut-être pas où vous pensez qu'il est. Donc, il est conseillé d'utiliser le mode incrémental G91 pour faire des gestes courts. Ensuite, effectuez un 'homing' cycle ou réinitialiser immédiatement après. 

#### `$H` - Run homing cycle | Démarrer le 'homing' cycle

Cette commande est la seule façon d'effectuer le 'homing' cycle dans Grbl. Certains autres contrôleurs de mouvement désignent une commande G-Code spéciale pour exécuter un 'homing' cycle, mais cela est inexact selon les normes G-Code. 'Homing' est une commande complètement séparé géré par le contrôleur.

**ASTUCE:** Après l'exécution d'un 'homing' cycle, plutôt que de faire bouger manuellement tout le temps à une position dans le milieu de votre volume d'espace de travail. Vous pouvez définir G28 ou G30 position pré-definie pour être votre position de post-homing, plus proche de l'endroit où vous commencerez votre usinage. Pour régler cela, vous devez d'abord déplacer la machine à l'endroit où vous voudriez vous déplacer après le 'homing'. Tapez `G28.1` (ou `G30.1`) pour que Grbl stocke cette position. Ensuite, après `$H` homing, vous pouvez simplement taper `G28` (ou `G30`) et il va se déplacer auto-magiquement. En général, je voudrais juste déplacer l'axe XY au centre et laisser l'axe-z en haut. Cela garantit que l'outil dans la broche ne vienne pas interferer et qu'il ne se coince pas sur quoi que ce soit.


#### `$RST=$`, `$RST=#`, and `$RST="` - Restore Grbl settings and data to defaults | Restaurer les réglages et données par defaut de Grbl

Ces commandes ne sont pas répertoriés dans le message d'aide principal de Grbl `$`, mais sont disponibles pour permettre aux utilisateurs de restaurer des parties ou l'ensemble des données de l'EEPROM Grbl.  
**NOTE: Grbl se réinitialise automatiquement après l'exécution de l'une de ces commandes pour s'assurer que le système est été initialiser correctement.**

* `RST=$` : Efface et restaure les paramètres par défaut `$$` Grbl, qui est définie par le fichier de paramètres par défaut utilisée lors de la compilation Grbl. Souvent, les équipementiers vont construire leurs firmwares Grbl avec leurs paramètres recommandés spécifiques à la machine. Cela fournit aux utilisateurs et aux équipementiers un moyen rapide d'obtenir un retour à zéro si quelque chose a mal tourné ou si l'utilisateur souhaite recommencer.
* `$RST=#` : Efface et remet à zéros les coordonnées de décalages de travail G54-G59 et les positions G28/30 mémorisées dans l'EEPROM. Ce sont généralement les valeurs observées dans l'impression des paramètres `$#`. Cela fournit un moyen facile d'effacer ces données sans avoir à le faire manuellement pour chaque ensemble avec les commandes `G20 L2/20` ou `G28.1/30.1`.
* `$RST="` : Cela supprime et restaure toutes les données de l'EEPROM utilisés par Grbl. Cela inclut les paramètres `$$`, les paramètres `$#`, les lignes de démarrage `$N`, et la chaine de build info `$I`. Notez que cela ne nettoie pas entièrement EEPROM, seules les zones de données Grbl utilisées. Pour faire un nettoyage complet, utiliser l'IDE Arduino avec l'exemple de projet **EEPROM clear**. 

****

## Real-Time Commands: `~, !, ?, and Ctrl-X`

Les quatre dernieres commandes de Grbl sont des commandes en temps réel. Cela signifie qu'elles peuvent être envoyées à tout moment, n'importe où, et Grbl répondra immédiatement, peu importe ce qu'il fait. Pour ceux qui sont curieux, ce sont des caractères spéciaux qui sont 'hors-ramassé' à partir du flux de série entrant et qui diront à Grbl de les exécuter, habituellement en quelques millisecondes.

#### `~` - Cycle start | Démarrage du cycle

Cela est la commande de démarrage ou d reprise de cycle qui peut être émis à tout moment, car c'est une commande en temps réel. Lorsque Grbl à des mouvements en attente dans sa mémoire tampon et qu'il est prêt à les exécuter, la '~' commande de démarrage du cycle va démarrer l'exécution de la mémoire tampon et Grbl commencera à déplacer les axes. Toutefois, par défaut, le démarrage 'auto-cycle' est activé, de cette façon les nouveaux utilisateurs n'auront pas besoin d'utiliser cette commande à moins qu'une pause de flux soit effectuée. Quand une pause de flux est exécuté, le départ du cycle va reprendre le programme. Le démarrage du cycle est efficace que quand il y a des mouvements dans la mémoire tampon prêt à être exécuter et ne fonctionnera pas avec d'autres processus comme le 'homing'.

#### `!` - Feed hold | Pause de flux

La commande de pause de flux entrainera le cycle actif vers un arrêt via une décélération contrôlée, afin de ne pas perdre la position. Cette commande est également en temps réel et elle peut être activée à tout moment. Une fois terminé ou suspendu, Grbl attendra qu'une commande de démarrage de cycle soit émise pour reprendre le programme. La pause de flux peut seulement mettre en pause un cycle et n'affectera pas le 'homing' ou tout autre procédé.

Si vous avez besoin d'arrêter un cycle à mi-programme et que vous ne pouvez pas vous permettre de perdre la position, effectuer une commande de pause de flux pour permettre à Grbl d'apporter un arrêt contrôlé. Une fois terminé, vous pouvez effectuer une réinitialisation. Toujours essayer d'exécuter une pause de flux lorsque la machine est en marche et avant d'effectuer une réinitialisation, sauf bien sûr si il y a une situation d'urgence.

#### `?` - Current status | Statut actuel

La commande `?` retourne immédiatement l'état actif de Grbl et la position actuelle en temps réel, à la fois avec les coordonnées de la machine et avec les coordonnées de travail. Optionally, you can also have Grbl respond back with the RX serial buffer and planner buffer usage via the status report mask setting. La commande `?` peut être envoyée à tout moment et fonctionne de manière asynchrone avec tous les autres processus Grbl. Le paramètre Grbl `$13` détermine si il rapporte en millimètres ou en pouces. Quand `?` est envoyé, Grbl répondra immédiatement avec quelque chose comme ceci:


    <Idle,MPos:5.529,0.560,7.000,WPos:1.529,-5.440,-0.000>


Les états actifs de Grbl peuvent être : `Idle, Run, Hold, Door, Home, Alarm, Check`

* **Idle**: Tous les systèmes sont ok, aucun mouvements en file d'attente, et il est prêt à tout.
* **Run**: Indique qu'un cycle est en cours.
* **Hold**: Une pause de flux est en cours d'exécution, ou entrain de ralentir pour un arrêt. Après que l'attente de flux soit complètement exécuté, Grbl restera en attente et attendra un départ de cycle pour reprendre le programme.
* **Door**: _(Nouveautés de la v0.9i)_ Cette option de compilation entraîne Grbl en attente de flux, arrête l'outil rotatif et le système de refroidissement, et attend que l'interrupteur de porte est été couper et que l'utilisateur lance un départ de cycle. Utile pour les OEM qui ont besoin de portes de sécurité.
* **Home**: Dans le milieu d'un 'homing' cycle. **NOTE:** Les positions ne sont pas mises à jour en direct pendant un 'homing' cycle, mais ils vont être placé à la position 'home' une fois terminer.
* **Alarm**: Ceci indique que quelque chose c'est mal passé ou que Grbl ne connaît pas sa position. Cet état vèrrouille toutes les commandes G-code, mais vous permet d'interagir avec les paramètres de Grbl si vous en avez besoin. `$X` kill alarm lock releases this state and puts Grbl in the Idle state, which will let you move things again. Comme dit précédemment, soyez prudent avec ce que vous faites après une alarme.
* **Check**: Grbl est en mode de contrôle G-code. Il procèdera et répondra à toutes les commandes G-Code, mais pas de mouvement ou allumer quoi que ce soit. Une fois éteint avec une autre commande `$C`, Grbl se réinitialisera.

#### `Ctrl-x` - Reset Grbl

Cette commande réinitialise Grbl. C'est une commande en temps réel et elle peut être envoyé à tout moment. Comme son nom l'indique, cette commande réinitialise Grbl, mais d'une manière contrôlée, elle "conserve" la position de la machine, et cela sans éteindre votre Arduino. Les seules fois où un soft-reset pourrait perdre la position est lorsque des problèmes surgissent et que les moteurs pas à pas ont été stopper alors qu'ils se déplaçaient. Si cela se produit, il va rapporter si le suivi de la position de la machine a été perdu. En effet, une décélération incontrôlée peut conduire à des pertes de pas, et Grbl n'a pas de retour de combien il a perdu (c'est le problème en général avec les moteurs pas à pas). Sinon, Grbl sera tout simplement réinitialiser, puis il exécutera les lignes de démarrage, et continuera sur son petit bonhomme de chemin. 

**NOTE IMPORTANTE:** il est toujours recommandé de faire un soft-reset avant de commencer un travail. Cela garanti qu'il n'y a pas de modes actifs que de jouer autour ou la configuration de votre machine avant d'exécuter la tâche G-code. Donc, votre machine devra toujours commencée fraîche et constante, et votre machine fera ce que vous attendez d'elle.


## Autres ressources:
* **[DANK](http://dank.bengler.no/-/page/show/5474configuringgrbl)** _Dernière mise à jour 2/2011._ Valable uniquement pour les grbl v0.6.