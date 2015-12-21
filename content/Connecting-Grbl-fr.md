Tout d'abord, pour connecter vos moteurs pas à pas à Grbl, vous aurez besoin de quelques pilotes de moteur pas à pas pour alimenter vos moteurs et connecter vos entrées de pilote aux broches du contrôleur Arduino. Il y a un certain nombre de pilotes qui peuvent le faire, disponible entièrement pré-construit, partiellement pré-construit, ou à assembler soi-même. Il y a quelques exemples plus bas dans cette page. Les pilotes pas à pas devront partager la broche d'activation de moteur (*D8*) pour leurs broches d'activation respectives, tandis que les broches de direction et d'impulsions de pas (*D2-D7*) devront être connectés à leurs broches respectives sur les pilotes. Assurez-vous que l'Arduino et tous vos pilotes partagent une masse (prise terre) commune (mis à la terre avec votre alimentation de pilotes moteur). Ceci est tout ce que vous aurez besoin pour commencer.

Ensuite, une fois que vous décidez que vous êtes prêt ou si vous souhaitez activer et /ou des limites strictes à tête chercheuse, vous devrez connecter un commutateur de fin de course normalement ouvert à chacune des broches associées (*D9-D11*). Homing et les limites strictes utilisent les mêmes commutateurs. Ces broches de fin de course sont déjà tenus de haut avec une résistance pull-up interne, donc tout ce que vous avez à faire est de les connecter à la terre/masse. Ainsi, lorsque vous fermez un commutateur, il fera rentrer la broche de fin de course à la masse. Si vous souhaitez avoir des fins de course strictes sur les deux extrémités d'un axe, connecter seulement deux commutateur de fins de course en parallèle à l'axe de limite correspondant et à la terre/masse. Assurez-vous que vous avez les commutateurs installés avant de tenter d'effectuer un cycle de ralliement, et assurez-vous que vos câblage sont bons pour minimiser les interférences électrique externe sur les broches d'entrée.

Dans Grbl v0.8 et plus récent, il y a brochage pour les commandes de début de cycle, de mise en pause, et de réinitialisation, de façon à pouvoir avoir des boutons de contrôle physique pour votre machine. Tout comme les broches de commutateurs de fin de course, ces broches sont maintenues avec une forte résistance pull-up interne, donc tout ce que vous avez à faire est de connecter un commutateur normalement ouvert sur chaque broche et à la terre. Encore une fois, assurez vous d'avoir un bon câblage pour minimiser les interférences électrique externe sur l'entrée des broches.

Si vous avez besoin de controller un outil rotatif ou un équipement de refroidissement, Grbl fera basculer ces broches de sortie (*D12, D13, A3*) high ou low, selon les ordres G-Code que vous envoyez à Grbl. Avec v0.9 et l'activation PWM d'outil rotatif variable, la broche D11 sortira une gamme de tensions de 0V à 5V qui fera varier la vitesse de l'outil rotatif en fonction de la commande G-Code. 0V indique que l'outil rotatif est éteint. Étant donné que ces broches et leurs fonctions dépendent de la façon dont elles sont utilisés, nous vous laissons le soin de déterminer comment les contrôler et les utiliser pour votre machine. Vous pouvez aussi hacker les fichiers source de commande d'outil rotatif et de refroidissement pour modifier facilement comment ils fonctionnent et puis compiler et télécharger votre Grbl modifiée via l'IDE l'Arduino.

# Première méthode: EasyDriver V4.4
_Dernière mise à jour: 2012-01-29 par @Atrixium._

Cela est une interface assez simple pour une machine 3 axes.

Le 'step signal ground' pour chaque EasyDriver est reliée ensemble et attaché à la broche GND de l'Arduino. Ne pas confondre avec la masse du moteur ou de toute autre connexion à la terre sur l'EasyDriver!
La broche 'Step' pour les axes X, Y et Z est fixé aux broches digital, respectivement sur les 2, 3 et 4.
La broche 'Dir' pour les axes X, Y et Z est fixé aux broches digital, respectivement 5, 6 et 7.

## Ressources

![GRBL EasyDriver](http://dl.dropbox.com/u/54312401/grbl/GRBL%20EasyDriver_small.png)  
  
[Big Version](http://dl.dropbox.com/u/54312401/grbl/GRBL%20EasyDriver_big.png)  
[Fritzing File](http://dl.dropbox.com/u/54312401/grbl/GRBL%20EasyDriver.fz)  
[EasyDriver Website](http://www.schmalzhaus.com/EasyDriver/)  

# Deuxième méthode: Grblshield
_Dernière mise à jour: 2012-02-21 par aldenhart._

[grblshield](http://www.synthetos.com/wiki/index.php?title=Projects:grblShield) - brancher sur Arduino pour un control de 3 axes - jusqu'à 2,5 ampères par enroulement. Les pilotes sont thermiquement protégés contre les surtensions et sont donc extrêmement résistant à l'épuisement professionnel ou de l'échec.  
  
![grblshield v2](http://dl.dropbox.com/u/45988398/6251974637_3aea9c2e08_z.jpg){: .synthetos}


# Troisième méthode: Stepper Shield
_Dernière mise à jour: 2012-02-03 par edwardrford._

[buildlog.net Arduino stepper shield](http://www.buildlog.net/blog/2011/08/stepper-driver-arduino-shield/) - support de pilote pololu Shield Arduino pour 3 axes. Pilotes remplaçables en cas de dommages.  
  
![stepper shield](http://buildlog.net/cnc_laser/store/images/stepper_shield.JPG){: .buildlog}  
  
[guide utilisateur](http://www.buildlog.net/cnc_laser/erp/get_doc.php?docNumber=A40008)


# Quatrième méthode: Arduino CNC Shield ou Raspberry Pi CNC Board/Hat
_Dernière mise à jour: 01/09/2015 par Protoneer._

[Arduino CNC Shield](http://blog.protoneer.co.nz/arduino-cnc-shield/) - 3 axes indépendants et un axe clones.  
Utilise des pilotes Stepstick ou Pololu avec toutes les broches GRBL accesible. _Compatible GRBL 0.9._  
  
![Arduino CNC Shield](http://blog.protoneer.co.nz/wp-content/uploads/2013/07/Arduino_CNC_Sheild_V310_Populated.jpg){: .protoneer}  
  
[Raspberry Pi CNC Board/Hat](http://wiki.protoneer.co.nz/RaspberryPiCNC) - 3 axes indépendants et des borniers à vis amovibles.  
Utilise des pilotes Stepstick ou Pololu avec toutes les broches GRBL accesible. _Compatible GRBL 0.9._  
  
![Raspberry Pi CNC Board/Hat](http://wiki.protoneer.co.nz/images/a/aa/Raspberry-Pi-CNC-Board-1.jpg){: .protoneer}  

# NON SUPPORTÉ: Pilotes H-bridge.

Certains premiers travaux sur le soutien de ceci peut être trouvée [ici]
(http://ssecganesh.blogspot.de/2008/05/driving-stepper-motor-using-uln2003.html). Il est obsolète et utilise Grbl v0.7.


## Autres ressources:
* **[DANK](http://dank.bengler.no/-/page/show/5473connectinggrbl)** _Dernière mise à jour 2/2011._
* **[Connecter Grbl à un pilote H-Bridge](https://github.com/grbl/grbl/wiki/H-Bridge-as-stepper-driver)**