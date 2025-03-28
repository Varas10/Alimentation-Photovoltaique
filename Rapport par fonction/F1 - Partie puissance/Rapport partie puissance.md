TALENT Julien et RUIZ Evan  
# Rapport du hacheur  

## Rôle de la fonction  

Le rôle du hacheur est d'adapter la tension de sortie en fonction d'un signal carré de type PWM.  
Il sera utilisé pour produire les tensions suivantes :  
* **5V** avec un rapport cyclique de **25%** pour un téléphone  
* **7,5V** avec un rapport cyclique de **37,5%** pour une carte Altera DE1  

## Schéma  
![[montage hacheur.png]]  

## Fonctionnement  

V1 représente le panneau photovoltaïque (PV), qui sera simulé par un générateur, et Vcom est notre générateur de PWM, qui sera un GBF lors du test de la carte.  

Lorsque Vcom envoie un état haut (15V) dans le circuit, le transistor Q2 crée un appel de courant dans Q1, ce qui le rend passant et fait que la tension de sortie est égale à celle du PV.  

Si, au lieu d'envoyer un état haut, on applique un signal PWM, alors la tension de sortie sera égale à :  

$$ V_{sortie} = V_1 \times rapport\_cyclique $$  

Le condensateur et la bobine permettent de lisser le signal de sortie afin d'obtenir uniquement la tension moyenne du signal.  

## Simulation  

### Simulation en régime transitoire pour une sortie de 5V  
![[Courbes hacheur.png]]  

>Sur la courbe du haut, on peut voir le signal de commande du hacheur avec un rapport cyclique de **25%**.  
>La courbe rose du bas représente la tension de sortie non filtrée, tandis que la courbe constante en rouge correspond à la tension de sortie filtrée.  

La tension de sortie observée devrait être de **5V**, mais sur ces courbes de simulation, on constate qu’elle atteint **6V**, soit une valeur plus élevée que prévu. Cette différence est due au **temps de commutation du transistor de puissance**, qui est relativement lent et entraîne une augmentation du rapport cyclique effectif.  

## Conclusion  

Ce rapport présente le fonctionnement et la simulation d’un hacheur permettant d’abaisser une tension d’entrée en fonction d’un signal PWM.  
L’analyse des résultats montre que la tension obtenue en sortie est légèrement supérieure à la valeur théorique attendue. Cette différence s’explique par la lenteur du transistor de puissance, qui modifie le rapport cyclique effectif.  

Pour améliorer la précision de la tension de sortie, plusieurs solutions pourraient être envisagées :  
- Utiliser un **transistor plus rapide** pour minimiser le temps de commutation.  
- **Ajuster le rapport cyclique** en fonction du retard observé pour compenser l’erreur.  
- Intégrer un **correcteur de commande** permettant de stabiliser la tension en boucle fermée.  

Malgré cette légère différence, la simulation valide bien le principe du hacheur et son fonctionnement attendu.  
