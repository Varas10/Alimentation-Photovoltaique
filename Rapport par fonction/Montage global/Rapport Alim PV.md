TALENT Julien & RUIZ Evan

# L'alimentation photovoltaïque :
## Présentation général du montage :


![[Montage final.png]]

Dans le montage nous avons : 
- la *source* qui est le panneau solaire;
- l'*alimentation* de la partie commande (fonction 3);
- *l'oscillateur* qui est la partie commande;
- le *hacheur* qui est la partie puissance;
- et enfin, la *charge* qui alimente la carte altéra ou le téléphone.

## Cahier des charges 
Le montage doit *alimenter un téléphone portable*, et *un "chenillard numérique"* à microprocesseur, c'est à dire une carte Altéra. L'alimentation ce fait *grâce à un panneau solaire*.

![[Cahier des charges images du système.png]]

Nous avons posés des questions au "client" et avons obtenue les spécifications ci dessous :
## Spécifications 


Le panneau photovoltaïque *peut délivré 10W au maximum*. De plus, la tensions fournis par le panneau solaire doit être comprise *entre 16V et 22V*, tout dépend des rayonnements du soleil.

Ensuite, on veut régler la tensions de sortie avec un *potentiomètre.* La tensions de sortie doit être *comprise entre 5V, 1A* afin de charger un téléphone portable en USB; et *7,5V, 0,8A* afin d'alimenter une carte à microprocesseur avec un câble coax.

Si le panneau solaire fournis une tension de 16V, la *tensions de sortie doit être suffisante* pour alimenter la carte, c'est à dire 7,5V.

## Schéma fonctionnelle

![[Schéma fonctionelle.png]]

La *fonction 1* permet de modifier la tensions de sortie.

La *fonction 2* permet d'envoyé les ordres à la partie puissance

La *fonction 3* permet d'adapter la tension du PV pour alimenter la partie commande. 

# Partie commande, l'oscillateur :
---

Elle permet de *choisir la tension de sortie* en *modulant la largeur de l'impulsion* (la pwm).

## Schéma :

![[Pasted image 20250316112551.png]] 
Sans le potentiomètre

![[Pasted image 20250316100859.png]]
avec le potentiomètre
## Fonctionnement :

Grâce au potentiomètre on peut modifier la largeur des impulsion ce qui permet de modifier le rapport cyclique.

La diode fait passer le courant jusqu'au condensateur lorsqu'il est en charge.

En charge le courant passe par RA et la diode, et en décharge il passe par RB et va dans le 555

## Choix des résistances :
Afin de choisir les résistances adéquats, nous avons procédé a une série de calculs : 

| Appareil à alimenter avec tension panneau = Ve = 20V | Pour le téléphone (5V) :                 | Pour la carte altéra (7.5V) :              |
| ---------------------------------------------------- | ---------------------------------------- | ------------------------------------------ |
| Tension à la sortie du hacheur :                     | 5V                                       | 7.5V                                       |
| rapport cyclique : $\alpha=\frac{Vs}{Ve}$            | $=\frac{5}{15}=25$%                      | $=\frac{7.5}{15}=38$%                      |
| période d'oscillation $T = \frac{1}{f}$              | $=\frac{1}{40k}=25\micro s$              |                                            |
| temps à l'état haut$t_{1}=\alpha * T$                | $=0.25*25^{-6}=6.25\micro s$             | $=0.38*25^{-6}=9.5\micro s$                |
| $Ra=\frac{t_{1}}{0.693*C}$                           | $=\frac{6.25^{-6}}{0.693*1^{-9}}=9k\ohm$ | $=\frac{9.5^{-6}}{0.693*1^{-9}}=13.7k\ohm$ |
| $t_{2}=(1-\alpha)*T$                                 | $=(1-0.25)*25^{-6}=18.75\micro s$        | $=(1-0.38)*25^{-6}=15.5\micro s$           |
| $RB=\frac{t_{2}}{0.693*c}-P$                         | $=27k\ohm$                               | $=22.4k\ohm$                               |
| RA+RB                                                | = 36k$\ohm$                              | = 36k$\ohm$                                |
### Simulation avec RA et RB :

|           | RA normalisée | Rb normalisée | rapport cyclique que l'on devrait obtenir | rapport cyclique vaec la simulation |
| --------- | ------------- | ------------- | ----------------------------------------- | ----------------------------------- |
| Vs = 5V   | 8.2k$\ohm$    | 27k$\ohm$     | 23%                                       | 25%                                 |
| VS = 7.5V | 15k$\ohm$     | 22k$\ohm$     | 41%                                       | 43%                                 |
On constate que la simulation est proche du résultats que l'on attendait.

## Ajout d'un potentiomètre :

| évolution de la tensions par rapport à l'ensolellement | il faut malgré tout avoir une tension en sortie du hacheur VS de : | rapport cyclique correspondant | position du potentiomètre  |
| ------------------------------------------------------ | ------------------------------------------------------------------ | ------------------------------ | -------------------------- |
| descend à 16V                                          | 7.5V                                                               | maximal de 47%                 | basse (x=100%)             |
| monte à 22V                                            | 5V                                                                 | minimal de 23%                 | haute (x=0)                |
### calcul de RA et Rb

On utilise la formule suivante :
- $RA=\frac{\alpha_{min}*P}{\alpha_{max-\alpha_{min}}}$ 
- $RB=\frac{RA-\alpha_{min}(RA+P)}{\alpha_{min}}$
obtenue à partir de la formule du rapport cyclique pour X=0 : 
- $\alpha=\frac{RA}{Ra+RB+P}$ 
avec 
- $RA+RB+P=\frac{RA+P}{\alpha_{max}}$ pour X=0 et
- $RA+RB+P=\frac{RA}{\alpha_{min}}$ pour x=1.

RA = 6.4k$\ohm$ , valeur normalisée : 6.8k$\ohm$ 
RB = 17k$\ohm$ , valeur normalisée : 18k$\ohm$ 

### Simulation :
pour V1=16v ou pour V1=22V, les rapports cyclique et tensions de sortie sont identique.

| Position du Potentiometre | rapport cyclique prévue | rapport cyclique obtenue | fréquence d'oscillation observée |
| ------------------------- | ----------------------- | ------------------------ | -------------------------------- |
| Haute(x=0)                | 19.5%                   | 21%                      | 40,2kHz                          |
| Basse (x=100%)            | 48%                     | 50%                      | 39,5kHz                          |


## Mesures et chronogrammes

### Problème !

Notre coupe RA-RB nous empêchez de descendre en dessous de 26% de rapport cyclique or on souhaite définir un rapport cyclique de 20% donc on change les résistances.

On obtient 
- RA =4.3k$\ohm$ et RB = 14k$\ohm$. 
On utiliseras des résistances normalisées E12 : 
- RA = 3.9k$\ohm$ et Rb = 15k$\ohm$.

### P en position basse(Potentiomètre à 100%) :

En position basse, le rapport cyclique doit être maximal 


#### calculs : 

| tension VC | la tension de sortie Vs est à l'état | formule  (p=10k)                   | rapport cyclique (pratique) : | Rapport cyclique (théorie) : |
| ---------- | ------------------------------------ | ---------------------------------- | ----------------------------- | ---------------------------- |
| monte      | haut pendant 11.4$\micro$s           | $t_{1}=0.693(RA+P)C = 9.6\micro s$ | 51%                           | 48%                          |
| descend    | bas pendant 10.8$\micro$s            | $t_{2}=0.693RB*C = 10.3\micro s$   |                               |                              |


### P en position haute(Potentiomètre à 0%) :

En position haute, le rapport cyclique doit être le minimum voulu :

#### calculs : 

| tension VC | la tension de sortie Vs est à l'état | formule  (p=10k)                   | rapport cyclique (pratique) : | Rapport cyclique (théorie) : |
| ---------- | ------------------------------------ | ---------------------------------- | ----------------------------- | ---------------------------- |
| monte      | haut pendant 44$\micro$s             | $t_{1}=0.693(RA+P)C = 2.7\micro s$ | 19.6%                         | 13.5%                        |
| descend    | bas pendant 16.4$\micro$s            | $t_{2}=0.693RB*C = 17.3\micro s$   |                               |                              |

## Conclusion :
Donc nous avons assemblé la partie commande :
- Grâce à la PWM, on peut adapté le rapport cyclique par rapport à nos besoins(20% - > 50%).
- De plus, les résistances choisis sont calculer pour obtenir une tension de sortie adéquate.
- Alors on peut alimenter une carte Altera en 7.5V ou un téléphone portable en 5V.



TALENT Julien & RUIZ Evan = 15

# Alimentation de la partie commande :
---
La fonction 3 permet d'adapter la tensions des panneau solaire afin d'alimenter la partie commande :
(Attention, la tensions fourni par le panneau solaire varie entre 16V et 22V en fonction des rayonnement du soleil !)
- En effet, la partie commande doit être alimenter en 15V, pour cela on se sert de la fonction 3 pour maintenir une tension de sortie de minimum 15V à partir du panneau solaire. 

- De plus, la puissance consommé doit être petite par rapport à celle fourni par le panneau.
## Etude de la fonction 3 seule

### Schéma :

Voici le schéma que l'on vas utiliser pour les simulations :
![[F3 seule.png]]

La diode Zener est en parallèle avec la résistance équivalente de l'oscillateur. La diode Zener étant à 15V, la tension de sortie Vs seras elle aussi à 15V.
### Calculs des résistances

#### Calcul de Req :

On sait que $Vs=Vz=15V$ , de plus on connait $I_{osc}=1,5mA$

Donc on peut faire le calcul : 
- $\mathrm{Re}q=\frac{Vs}{Iosc}=\frac{15}{1,5*10^{-3}}=10k\ohm$.

#### Calcul de R1 :

On ne connait pas $V_{R_{1}}$ mais on peut utiliser la loi des mailles pour retrouver la tensions dans R1 :
- $V_{R_{1}}=V_{1}-Vz$
De plus on ne connait pas $I_{R_{1}}$ cependant on peut utilisé la loi des nœud pour trouver le courant dans R1 : 
- $I_{R_{1}}=I_{z}+I_{osc}$.

Désormais on peut calculer $R_{1}$ :
- $R_{1}=\frac{V_{1}-V_{z}}{I_{z}+I_{osc}}=\frac{16-15}{(1+1,5)*10^{-3}}=400\ohm$ 


### Simulations :

On fait une simulation sur *Spice* avec l'analyse en **Dynamic DC** et on obtient les résultats suivant :

| Tension du panneau | Vs (en V) | $I_{osc}$ (absorbé) (en mA) | $I_{R_{Z}}$ (en mA) | $I_{z}$ (en pA) | $P_{absorbé}$ (en mW) |
| ------------------ | --------- | --------------------------- | ------------------- | --------------- | --------------------- |
| 16V                | 15,4      | 1,5                         | 1,5                 | 15,5            | 23,6                  |
| 22V                | 21,1      | 2,1                         | 2,1                 | 21,3            | 46,7                  |
On constate que la sortie Vs est au minimum à 15V, c'est ce que l'on attendait.

De plus, la puissance fourni par le panneau solaire est presque entièrement absorbé par la partie commande et non pas par la diode ni la résistance R1.

Par contre on rencontre un problème :
- Le courant passant dans la diode Zener est largement inférieur à 1mA alors qu'il doit être au minimum de 1mA.

## Etude de la fonction 3 relié à la partie commande

### Schéma :

On va simulé ce montage :
![[F3 relié à F2.png]]

On remplace la résistance équivalent de l'oscillateur par le montage oscillateur. La diode Zener est bien en parallèle avec le montage oscillateur.

### Simulation :

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

## Etude en pratique de tout le montage

### Schéma :

![[Montage final.png]]

On relie les 3 fonctions entre elles, on retire la résistance $R_{osc}$ car elle correspond à la résistance équivalente de l'oscillateur.
Il faut brancher la sortie du 555 (S_555) à RbQ2, là ou était placé Vcom, en effet, l'oscillateur représente la partie commande.

### Problème avec les rapports cyclique :

Lorsque nous avons fait les essais, nous n'arrivions plus à atteindre les rapports cyclique nécessaire car la partie puissance l'augmentais.

Alors on as du refaire tout les calculs pour Ra et Rb :

- Avec $\alpha_{min}=0,01$ soit 1% et $\alpha_{max}=0,5$ soit 50%. (Pour rappel, à la base on veut un $\alpha_{min}$ de 25% et un $\alpha_{max}$ de 50% ) :
- $Ra = \frac{\alpha_{min*P}}{\alpha_{max}-\alpha_{min}}= 204\ohm$, on utilise une résistance normalisé E12 de 220 $\ohm$;
- $R_{b}=\frac{R_{a}-\alpha_{min}(R_{a}+P)}{\alpha_{min}}=9800\ohm$, on utilise une résistance normalisé E12 de 10k$\ohm$.

### Rapports cyclique obtenue :

lorsque le panneau est à 16V on as :
- un rapport cyclique minimum de 8,7%<<25%,
- et un rapport cyclique maximal de 54%>50%

L'intervalle des rapports cyclique correspond bien au cahier des charges !

### Mise en garde :

ATTENTION, le rapport cyclique maximal ne doit pas être trop grand devant le rapport cyclique maximal demandé car il y a un risque de bruler des composants. 
Il faut donc vérifier que les composant supporte la tension reçu lorsque on augmente le potentiomètre.

## Validation de l'ensemble du montage

Le montage finale fonctionne très bien : le potentiomètre permet d'obtenir un intervalle de tension de sortie comprenant les tensions nécessaire à l'alimentation de la carte Altéra et du Téléphone.

| Tensions extrême des panneaux | Tension de sortie  |
| ----------------------------- | ------------------ |
| 16V                           | Entre 4,6V et 7,6V |
| 22V                           | Entre 280mV et 10V |
On atteint les 7,5V nécessaire à l'alimentation de la carte Altera. De plus on peut fournir les 5V demandé pour charger le téléphone.

Le montage est donc validé, il peut être utilisé sans aucun problème.

## Conclusion

On est désormais capable de fournir une tension nécessaire a l'alimentation d'un téléphone ou d'une carte altéra avec un seule panneau solaire.

De plus, on as adapté le montage de façon à ce que la tensions puissent fournir les volts nécessaire afin de respecter le cahier des charges : Vs = 7,5V et Vs=5V
# Schéma et Routage :

## Schéma spice

![[Alimentation-Photovolta-que/Rapport par fonction/Montage global/Montage final.png]]

## Schéma et Routage Eagle

![[Schéma Eagle montage final.png]]![[Routage .png]]

# Conclusion :
## Technique :

Le montage fonctionne très bien, cependant pour un utilisateur lambda, le réglage de la tension de sortie demande quelques compétences. 

En effet, le réglage du potentiomètre demande un peu de précision car si l'utilisateur augmente trop rapidement le pourcentage du potentiomètre, la tension peut augmenter très rapidement et faire chauffer les composants ainsi que l'appareil qui est alimenté.
Cela causerai une panne nécessitant une maintenance et la détérioration du téléphone/carte altéra.

Pour remédié à cela, il aurais fallu ajouter un système automatique qui met le potentiomètre dans la bonne position. L'utilisateur aurais juste à appuyer sur un switch pour changer la tension de sortie.

Donc notre système fonctionne très bien malgré son manque de simplicité d'utilisation qui pourrais empêcher une personne sans compétences en électronique de l'utiliser correctement.

## Personnelle

### RUIZ Evan :

Ce projet m'as plu car j'ai pu développer mes compétences dans la conception d'un système.
De plus, j'ai bien aimé le fait de trouver des solutions par moi-même.
Par rapport au premier semestre je pense m'être améliorer dans la rédaction des rapports, j'ai veillais à mettre plus de phrase tout en gardant l'intégration d'images et tableaux permettant de simplifier la lecture des résultats.

Si on me demandai de le refaire je pense que j'aurais utiliser la même méthode, cependant j'aurais prévus beaucoup plus de marge pour le calcul des résistances. En effet, à plusieurs reprise on as du recalculé la valeur des résistances car les rapports cyclique théorique était bien loin de la pratique.

J'aurais utilisé les même logiciel et fait les mêmes simulations car elles nous ont permis de mieux comprendre le fonctionnement de chaque fonction et des composants les intégrants.