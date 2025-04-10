TALENT Julien & RUIZ Evan = 15

# Rôle de la fonction 3
---
La fonction 3 permet d'adapter la tensions des panneau solaire afin d'alimenter la partie commande :
(Attention, la tensions fourni par le panneau solaire varie entre 16V et 22V en fonction des rayonnement du soleil !)
- En effet, la partie commande doit être alimenter en 15V, pour cela on se sert de la fonction 3 pour maintenir une tension de sortie de minimum 15V à partir du panneau solaire. 

- De plus, la puissance consommé doit être petite par rapport à celle fourni par le panneau.

# Calculs des résistances
---

## Calcul de Req :

On sait que $Vs=Vz=15V$ , de plus on connait $I_{osc}=1,5mA$

Donc on peut faire le calcul : 
- $\mathrm{Re}q=\frac{Vs}{Iosc}=\frac{15}{1,5*10^{-3}}=10k\ohm$.

## Calcul de R1 :

On ne connait pas $V_{R_{1}}$ mais on peut utiliser la loi des mailles pour retrouver la tensions dans R1 :
- $V_{R_{1}}=V_{1}-Vz$
De plus on ne connait pas $I_{R_{1}}$ cependant on peut utilisé la loi des nœud pour trouver le courant dans R1 : 
- $I_{R_{1}}=I_{z}+I_{osc}$.

Désormais on peut calculer $R_{1}$ :
- $R_{1}=\frac{V_{1}-V_{z}}{I_{z}+I_{osc}}=\frac{16-15}{(1+1,5)*10^{-3}}=400\ohm$ 

# Etude de la fonction 3 seule
---
## Schéma :

Voici le schéma que l'on vas utiliser pour les simulations :
![[F3 seule.png]]

La diode Zener est en parallèle avec la résistance équivalente de l'oscillateur. La diode Zener étant à 15V, la tension de sortie Vs seras elle aussi à 15V.

## Simulations :

On fait une simulation sur *Spice* avec l'analyse en **Dynamic DC** et on obtient les résultats suivant :

| Tension du panneau | Vs (en V) | $I_{osc}$ (absorbé) (en mA) | $I_{R_{Z}}$ (en mA) | $I_{z}$ (en pA) | $P_{absorbé}$ (en mW) |
| ------------------ | --------- | --------------------------- | ------------------- | --------------- | --------------------- |
| 16V                | 15,4      | 1,5                         | 1,5                 | 15,5            | 23,6                  |
| 22V                | 21,1      | 2,1                         | 2,1                 | 21,3            | 46,7                  |
On constate que la sortie Vs est au minimum à 15V, c'est ce que l'on attendait.

De plus, la puissance fourni par le panneau sollaire est presque entièrement absorbé par la partie commande et non pas par la diode ni la resistance R1.

Par contre on rencontre un problème :
- Le courant passant dans la diode Zener est largement inférieur à 1mA alors qu'il doit être au minimum de 1mA.

# Etude de la fonction 3 relié à la partie commande
---

## Schéma :

On va simulé ce montage :
![[F3 relié à F2.png]]

On remplace la résistance équivalent de l'oscillateur par le montage oscillateur. La diode Zener est bien en parallèle avec le montage oscillateur.

## Simulation :

On va simulé avec l'analyse transient pour observé le comportement des deux fonctions ensemble :

| Tensions du panneau | $V_{cc555}$ (en V) | $f_{osc}$(en Hz) | $\alpha_{sortie}$ | $P_{consommé}$ (en mW) |
| ------------------- | ------------------ | ---------------- | ----------------- | ---------------------- |
| 16V                 | 15                 | 35,9k            | 41%               | 15                     |
| 22V                 | 15                 | 35,9k            | 41%               | 112                    |
On constate que la tension fourni est toujours de 15V. 
- Si l'on compare au schéma précédent contenant uniquement la fonction 3, la tensions de sortie à 22V était de 21V à peut près ; 
- Tandis que maintenant elle se limite à 15V (c'est dans les paramètres du 555 que la tension absorbé est limité) : 
On respecte bien le cahier des charges !

D'après le schéma et l'analyse transient la tensions de sortie du 555 est toujours de 24mV lorsque le panneau varie entre 16v et 22V. Cela explique pourquoi le rapport cyclique et la fréquence ne changent pas.
oscillateur 

# Etude en pratique de tout le montage
---

## Schéma :
![[Montage final.png]]

On relie les 3 fonctions entre elles, on retire la résistance $R_{osc}$ car elle correspond à la résistance équivalente de l'oscillateur.
Il faut brancher la sortie du 555 (S_555) à RbQ2, là ou était placé Vcom, en effet, l'oscillateur représente la partie commande.

## Problème avec les rapports cyclique :

Lorsque nous avons fait les essais, nous n'arrivions plus à atteindre les rapports cyclique nécessaire car la partie puissance l'augmentais.

Alors on as du refaire tout les calculs pour Ra et Rb :

- Avec $\alpha_{min}=0,01$ soit 1% et $\alpha_{max}=0,5$ soit 50%. (Pour rappel, à la base on veut un $\alpha_{min}$ de 25% et un $\alpha_{max}$ de 50% ) :
- $Ra = \frac{\alpha_{min*P}}{\alpha_{max}-\alpha_{min}}= 204\ohm$, on utilise une résistance normalisé E12 de 220 $\ohm$;
- $R_{b}=\frac{R_{a}-\alpha_{min}(R_{a}+P)}{\alpha_{min}}=9800\ohm$, on utilise une résistance normalisé E12 de 10k$\ohm$.

## Rapports cyclique obtenue :

lorsque le panneau est à 16V on as :
- un rapport cyclique minimum de 8,7%<<25%,
- et un rapport cyclique maximal de 54%>50%

L'intervalle des rapports cyclique correspond bien au cahier des charges !

## Mise en garde :

ATTENTION, le rapport cyclique maximal ne doit pas être trop grand devant le rapport cyclique maximal demandé car il y a un risque de bruler des composants. 
Il faut donc vérifier que les composant supporte la tension reçu lorsque on augmente le potentiomètre.

# Validation de l'ensemble du montage
---

Le montage finale fonctionne très bien : le potentiomètre permet d'obtenir un intervalle de tension de sortie comprenant les tensions nécessaire à l'alimentation de la carte Altéra et du Téléphone.

| Tensions extrême des panneaux | Tension de sortie  |
| ----------------------------- | ------------------ |
| 16V                           | Entre 4,6V et 7,6V |
| 22V                           | Entre 280mV et 10V |
On atteint les 7,5V nécessaire à l'alimentation de la carte Altera. De plus on peut fournir les 5V demandé pour charger le téléphone.

Le montage est donc validé, il peut être utilisé sans aucun problème.

# Conclusion
---
On est désormais capable de fournir une tension nécessaire a l'alimentation d'un téléphone ou d'une carte altéra avec un seule panneau solaire.

De plus, on as adapté le montage de façon à ce que la tensions puissent fournir les volts nécessaire afin de respecter le cahier des charges : Vs = 7,5V et Vs=5V