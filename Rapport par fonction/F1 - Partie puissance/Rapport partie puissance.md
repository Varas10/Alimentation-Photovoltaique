TALENT Julien et RUIZ Evan
# Rapport du hacheur
## Rôle de la fonction : 

Le rôle du hacheur est d'adapter la tension de sortie en fonction d'un signal carré de type PWM.
Il sera utiliser pour produire les tension :
* 5V avec un rapport cyclique de 25% pour un téléphone 
* 7,5V avec un rapport cyclique de 37,5% pour une carte Altéra DE1

## Schéma :
 ![[montage hacheur.png]]
## Fonctionnement :
V1 est le panneau photovoltaïque (PV) qui sera simulé par un générateur et Vcom est notre générateur de PWM qui sera un GBF lors du teste de la carte.

Quand Vcom envoi un état haut (15V) dans le circuit le transistor Q2 crée un appel de courant dans Q1 ce qui le rend passant et la tension de sortie égale a celle du PV.

Si au lieu d'envoyé un état haut on envoie un signal PWM alors la tension de sortie sera égale a $V1 \times rapport\_cyclique$.

Le condensateur et la bobine permettent de lissé le signal de sortie pour avoir uniquement la tension moyenne du signal.
## Simulation 
Simulation en transient pour une sortie de 5V:
![[Courbes hacheur.png]]
>Sur la courbe du haut on peut voir le signal de commande de hacheur avec un rapport cyclique de 25%. La courbe rose du bas est la tension de sortie non filtré et la courbe constante en rouge est la tension de sortie filtré.

La tension de sortie observé devrai être de 5V mais sur ces courbes de simulation on peut voir que la tension de sorti du hacheur est de 6V ce qui est donc plus élevé que ce qu'elle devrait. C'est parce que le transistor de puissance est lent et augmente le rapport cyclique.

## Conclusion :

