TALENT Julien et RUIZ Evan
# Rapport du hacheur
## Rôle de la fonction : 

Le role du hacheur est d'adapter la tension de sortie en fonction d'un signal carré de type PWM.

## Schéma :
 ![[Pasted image 20250321114603.png]]
## Fonctionnement :
V1 est le panneau photovoltaïque (PV) qui sera simulé par un générateur et Vcom est notre générateur de PWM qui sera un GBF lors du teste de la carte.

Quand Vcom envoi un état haut (15V) dans le circuit le transistor Q2 crée un appel de courant dans Q1 ce qui le rend passant et la tension de sortie evident égale a celle du PV.

Si au lieu d'envoyé un état haut on envoie un signal PWM alors la tension de sortie sera égale a $V1 \times rapport\_cyclique$.

Le condensateur et la bobine permettent de lissé le signal de sortie pour avoir uniquement la tension moyenne du signal.
## Simulation 

## Mesures et chronogrammes

## Problème !
## Conclusion :

