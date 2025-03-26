TALENT Julien & RUIZ Evan
# 1. Introduction :
## **Présentation général du montage :**

![[Montage final.png]]

Dans le montage nous avons : 
- la *source* qui est le panneau solaire;
- l'*alimentation* de la partie commande (fonction 3);
- *l'oscillateur* qui est la partie commande;
- le *hacheur* qui est la partie puissance;
- et enfin, la *charge* qui alimente la carte altéra ou le téléphone.

## **Cahier des charges** 
Le montage doit *alimenter un téléphone portable*, et *un "chenillard numérique"* à microprocesseur, c'est à dire une carte Altéra. L'alimentation ce fait *grâce à un panneau solaire*.

![[Cahier des charges images du système.png]]

Nous avons posés des questions au "client" et avons obtenue les spécifications ci dessous :
## **Spécifications** 
Le panneau photovoltaïque *peut délivré 10W au maximum*. De plus, la tension fournis par le panneau solaire doit être comprise *entre 16V et 22V*, tout dépend des rayonnements du soleil.

Ensuite, on veut régler la tension de sortie avec un *potentiomètre.* La tension de sortie doit être *comprise entre 5V, 1A* afin de charger un téléphone portable en USB; et *7,5V, 0,8A* afin d'alimenter une carte à microprocesseur avec un câble coax.

Si le panneau solaire fournis une tension de 16V, la *tension de sortie doit être suffisante* pour alimenter la carte, c'est à dire 7,5V.

## **Schéma fonctionnelle**

![[Schéma fonctionelle.png]]

La *fonction 1* permet de modifier la tension de sortie.

La *fonction 2* permet d'envoyé les ordres à la partie puissance

La *fonction 3* permet d'adapter la tension du PV pour alimenter la partie commande. 

TALENT Julien et RUIZ Evan

TALENT Julien et RUIZ Evan  
# 2. Rapport du hacheur :  

## **Rôle de la fonction**  

Le rôle du hacheur est d'adapter la tension de sortie en fonction d'un signal carré de type PWM.  
Il sera utilisé pour produire les tensions suivantes :  
* **5V** avec un rapport cyclique de **25%** pour un téléphone  
* **7,5V** avec un rapport cyclique de **37,5%** pour une carte Altera DE1  

## **Schéma**  

![[montage hacheur.png]]  

## **Fonctionnement**  

V1 représente le panneau photovoltaïque (PV), qui sera simulé par un générateur, et Vcom est notre générateur de PWM, qui sera un GBF lors du test de la carte.  

Lorsque Vcom envoie un état haut (15V) dans le circuit, le transistor Q2 crée un appel de courant dans Q1, ce qui le rend passant et fait que la tension de sortie est égale à celle du PV.  

Si, au lieu d'envoyer un état haut, on applique un signal PWM, alors la tension de sortie sera égale à :  

$$ V_{sortie} = V_1 \times rapport\_cyclique $$  

Le condensateur et la bobine permettent de lisser le signal de sortie afin d'obtenir uniquement la tension moyenne du signal.  

## **Simulation**  

### Simulation en régime transitoire pour une sortie de 5V  
![[Courbes hacheur.png]]  

>Sur la courbe du haut, on peut voir le signal de commande du hacheur avec un rapport cyclique de **25%**.  
>La courbe rose du bas représente la tension de sortie non filtrée, tandis que la courbe constante en rouge correspond à la tension de sortie filtrée.  

La tension de sortie observée devrait être de **5V**, mais sur ces courbes de simulation, on constate qu’elle atteint **6V**, soit une valeur plus élevée que prévu. Cette différence est due au **temps de commutation du transistor de puissance**, qui est relativement lent et entraîne une augmentation du rapport cyclique effectif.  

## **Conclusion**  

Ce rapport présente le fonctionnement et la simulation d’un hacheur permettant d’abaisser une tension d’entrée en fonction d’un signal PWM.  
L’analyse des résultats montre que la tension obtenue en sortie est légèrement supérieure à la valeur théorique attendue. Cette différence s’explique par la lenteur du transistor de puissance, qui modifie le rapport cyclique effectif.  

Pour améliorer la précision de la tension de sortie, plusieurs solutions pourraient être envisagées :  
- Utiliser un **transistor plus rapide** pour minimiser le temps de commutation.  
- **Ajuster le rapport cyclique** en fonction du retard observé pour compenser l’erreur.  
- Intégrer un **correcteur de commande** permettant de stabiliser la tension en boucle fermée.  

Malgré cette légère différence, la simulation valide bien le principe du hacheur et son fonctionnement attendu.  

# 3. Partie commande, l'oscillateur :
---

Elle permet de générer un signal carrée avec un rapport cyclique variable pour contrôler le hacheur.

## **Schéma :**

![[Pasted image 20250316112551.png]] 

## **Fonctionnement :**

Grâce au potentiomètre on peut modifier la largeur des impulsion ce qui permet de modifier le rapport cyclique.

La diode fait passer le courant jusqu'au condensateur lorsqu'il est en charge.

En charge le courant passe par RA et la diode, et en décharge il passe par RB et va dans le 555

## **Choix des résistances :**

Afin de choisir les résistances adéquats, nous avons procédé à une série de calculs : 

| Appareil à alimenter avec tension panneau = Ve = 20V | Pour le téléphone (5V) :                 | Pour la carte altéra (7.5V) :              |
| ---------------------------------------------------- | ---------------------------------------- | ------------------------------------------ |
| Tension à la sortie du hacheur :                     | 5V                                       | 7.5V                                       |
| rapport cyclique : $\alpha=\frac{Vs}{Ve}$            | $=\frac{5}{15}=25$%                      | $=\frac{7.5}{15}=38$%                      |
| période d'oscillation $T = \frac{1}{f}$              | $=\frac{1}{40k}=25\micro s$              |                                            |
| temps à l'état haut$t_{1}=\alpha * T$                | $=0.25*25^{-6}=6.25\micro s$             | $=0.38*25^{-6}=9.5\micro s$                |
| $Ra=\frac{t_{1}}{0.693*C}$                           | $=\frac{6.25^{-6}}{0.693*1^{-9}}=9k\ohm$ | $=\frac{9.5^{-6}}{0.693*1^{-9}}=13.7k\ohm$ |
| $t_{2}=(1-\alpha)*T$                                 | $=(1-0.25)*25^{-6}=18.75\micro s$        | $=(1-0.38)*25^{-6}=15.5\micro s$           |
| $RB=\frac{t_{2}}{0.693*c}-P$                         | $=17k\ohm$                               | $=12.4k\ohm$                               |
| RA+RB                                                | = 26k$\ohm$                              | = 26,1k$\ohm$                              |
Donc on vas utiliser une résistance RA de 9k$\ohm$ et une résistance RB de 17k$\ohm$ pour le téléphone.
Puis une résistance RA de 13,7k$\ohm$ et une résistance RB de 12,4k$\ohm$ pour la carte altéra.

Normalement si on additionne RA et RB du 5v on doit obtenir la même valeur que la somme de RA et RB du 7.5V.

Ici c'est bien le cas donc les résistances sont adaptées à notre système.

### *Simulation avec RA et RB :*

|           | RA normalisée | Rb normalisée | rapport cyclique que l'on devrait obtenir | rapport cyclique vaec la simulation |
| --------- | ------------- | ------------- | ----------------------------------------- | ----------------------------------- |
| Vs = 5V   | 8.2k$\ohm$    | 27k$\ohm$     | 23%                                       | 25%                                 |
| VS = 7.5V | 15k$\ohm$     | 22k$\ohm$     | 41%                                       | 43%                                 |
On constate que la simulation est proche du résultats que l'on attendait.

## **Ajout d'un potentiomètre** :

### *Schéma :*

![[Pasted image 20250316100859.png]]
On ajoute un potentiomètre afin de moduler la fréquence d'oscillation de la PWM et donc de faire varier le rapport cyclique.

### *Rapport cyclique théorique*

| évolution de la tension par rapport à l'ensolellement | il faut malgré tout avoir une tension en sortie du hacheur VS de : | rapport cyclique correspondant | position du potentiomètre |
| ----------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------ | ------------------------- |
| descend à 16V                                         | 7.5V                                                               | maximal de 47%                 | basse (x=100%)            |
| monte à 22V                                           | 5V                                                                 | minimal de 23%                 | haute (x=0)               |
Donc on doit atteindre un rapport cyclique minimal de 23% lorsque le potentiomètre est en position haute. De plus, le hacheur doit fournir 5V en sortie au minimum sinon on ne pourras pas charger de téléphone.

Ensuite, on doit atteindre un rapport cyclique maximal de 47% lorsque le potentiomètre est en position basse. De plus, le hacheur doit pouvoir alimenter une carte altéra en 7.5V

Cependant, il faut prévoir une marge d'environ 10% de rapport cyclique :
- 23% --> 13%
- 47% --> 57%
### *Calcul de RA et Rb en fonction du rapport cyclique.*

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

### *Simulation :*

pour V1=16v ou pour V1=22V, les rapports cyclique et tensions de sortie sont identique.

| Position du Potentiometre | rapport cyclique prévue | rapport cyclique obtenue | fréquence d'oscillation observée |
| ------------------------- | ----------------------- | ------------------------ | -------------------------------- |
| Haute(x=0)                | 19.5%                   | 21%                      | 40,2kHz                          |
| Basse (x=100%)            | 48%                     | 50%                      | 39,5kHz                          |

Donc on as des rapports cyclique cohérent avec la théorie cependant la marge n'est pas assez grande. On verra par la suite comment ajouter de la marge.
## **Mesures**

### *Problème !*

Notre coupe RA-RB nous empêchez de descendre en dessous de 26% de rapport cyclique or on souhaite définir un rapport cyclique de 20% donc on change les résistances.

On obtient 
- RA =4.3k$\ohm$ et RB = 14k$\ohm$. 
On utiliseras des résistances normalisées E12 : 
- RA = 3.9k$\ohm$ et Rb = 15k$\ohm$.

### *P en position basse(Potentiomètre à 100%) :*

En position basse, le rapport cyclique doit être maximal 
#### calculs : 

| tension VC | la tension de sortie Vs est à l'état | formule  (p=10k)                   | rapport cyclique (pratique) : | Rapport cyclique (théorie) : |
| ---------- | ------------------------------------ | ---------------------------------- | ----------------------------- | ---------------------------- |
| monte      | haut pendant 11.4$\micro$s           | $t_{1}=0.693(RA+P)C = 9.6\micro s$ | 51%                           | 48%                          |
| descend    | bas pendant 10.8$\micro$s            | $t_{2}=0.693RB*C = 10.3\micro s$   |                               |                              |

La marge n'est pas assez grande mais le rapport cyclique reste supérieur à ce que l'on attendait.
### *P en position haute(Potentiomètre à 0%) :

En position haute, le rapport cyclique doit être le minimum voulu :

#### calculs : 

| tension VC | la tension de sortie Vs est à l'état | formule  (p=10k)                   | rapport cyclique (pratique) : | Rapport cyclique (théorie) : |
| ---------- | ------------------------------------ | ---------------------------------- | ----------------------------- | ---------------------------- |
| monte      | haut pendant 44$\micro$s             | $t_{1}=0.693(RA+P)C = 2.7\micro s$ | 19.6%                         | 13.5%                        |
| descend    | bas pendant 16.4$\micro$s            | $t_{2}=0.693RB*C = 17.3\micro s$   |                               |                              |
Le rapport cyclique est trop élevé, il va falloir corrigé pour l'adapter au cahier des charges.
Pour cela on modifiera les résistances RA et RB.
## **Conclusion :**
Donc nous avons assemblé la partie commande :
- Grâce à la PWM, on peut adapté le rapport cyclique par rapport à nos besoins(20% - > 50%).
- De plus, les résistances choisis sont calculer pour obtenir une tension de sortie adéquate.
- Alors on peut alimenter une carte Altera en 7.5V ou un téléphone portable en 5V.

Cependant, on as très peut de marge pour les rapports cyclique.
# 4. Alimentation de la partie commande :
---
La fonction 3 permet d'adapter la tension des panneau solaire afin d'alimenter la partie commande :
(Attention, la tension fourni par le panneau solaire varie entre 16V et 22V en fonction des rayonnement du soleil !)
- En effet, la partie commande doit être alimenter en 15V, pour cela on se sert de la fonction 3 pour maintenir une tension de sortie de minimum 15V à partir du panneau solaire. 

- De plus, la puissance consommé doit être petite par rapport à celle fourni par le panneau.
## **Etude de la fonction 3 seule :**

### *Schéma :*

Voici le schéma que l'on vas utiliser pour les simulations :
![[F3 seule.png]]

La diode Zener est en parallèle avec la résistance équivalente de l'oscillateur. La diode Zener étant à 15V, la tension de sortie Vs seras elle aussi à 15V.
### *Calculs des résistances*

#### Calcul de Req :

On sait que $Vs=Vz=15V$ , de plus on connait $I_{osc}=1,5mA$

Donc on peut faire le calcul : 
- $\mathrm{Re}q=\frac{Vs}{Iosc}=\frac{15}{1,5*10^{-3}}=10k\ohm$.

#### Calcul de R1 :

On ne connait pas $V_{R_{1}}$ mais on peut utiliser la loi des mailles pour retrouver la tension dans R1 :
- $V_{R_{1}}=V_{1}-Vz$
De plus on ne connait pas $I_{R_{1}}$ cependant on peut utilisé la loi des nœud pour trouver le courant dans R1 : 
- $I_{R_{1}}=I_{z}+I_{osc}$.

Désormais on peut calculer $R_{1}$ :
- $R_{1}=\frac{V_{1}-V_{z}}{I_{z}+I_{osc}}=\frac{16-15}{(1+1,5)*10^{-3}}=400\ohm$ 


### *Simulations :*

On fait une simulation sur Spice avec l'analyse en *Dynamic DC* et on obtient les résultats suivant :

| Tension du panneau | Vs (en V) | $I_{osc}$ (absorbé) (en mA) | $I_{R_{Z}}$ (en mA) | $I_{z}$ (en pA) | $P_{absorbé}$ (en mW) |
| ------------------ | --------- | --------------------------- | ------------------- | --------------- | --------------------- |
| 16V                | 15,4      | 1,5                         | 1,5                 | 15,5            | 23,6                  |
| 22V                | 21,1      | 2,1                         | 2,1                 | 21,3            | 46,7                  |
On constate que la sortie Vs est au minimum à 15V, c'est ce que l'on attendait.

De plus, la puissance fourni par le panneau solaire est presque entièrement absorbé par la partie commande et non pas par la diode ni la résistance R1.

Par contre on rencontre un problème :
- Le courant passant dans la diode Zener est largement inférieur à 1mA alors qu'il doit être au minimum de 1mA.

## **Etude de la fonction 3 relié à la partie commande**

### **Schéma :**

On va simulé ce montage :
![[F3 relié à F2.png]]

On remplace la résistance équivalent de l'oscillateur par le montage oscillateur. La diode Zener est bien en parallèle avec le montage oscillateur.

### **Simulation :**

On va simulé avec l'analyse transient pour observé le comportement des deux fonctions ensemble :

| Tension du panneau | $V_{cc555}$ (en V) | $f_{osc}$(en Hz) | $\alpha_{sortie}$ | $P_{consommé}$ (en mW) |
| ------------------ | ------------------ | ---------------- | ----------------- | ---------------------- |
| 16V                | 15                 | 35,9k            | 41%               | 15                     |
| 22V                | 15                 | 35,9k            | 41%               | 112                    |
On constate que la tension fourni est toujours de 15V. 
- Si l'on compare au schéma précédent contenant uniquement la fonction 3, la tension de sortie à 22V était de 21V à peut près ; 
- Tandis que maintenant elle se limite à 15V (c'est dans les paramètres du 555 que la tension absorbé est limité) : 
On respecte bien le cahier des charges !

D'après le schéma et l'analyse transient la tension de sortie du 555 est toujours de 24mV lorsque le panneau varie entre 16v et 22V. Cela explique pourquoi le rapport cyclique et la fréquence ne changent pas.
oscillateur 

# 5. Etude de tout le montage  :

## **Schéma :**

![[Montage final.png]]

On relie les 3 fonctions entre elles, on retire la résistance $R_{osc}$ car elle correspond à la résistance équivalente de l'oscillateur.
Il faut brancher la sortie du 555 (S_555) à RbQ2, là ou était placé Vcom, en effet, l'oscillateur représente la partie commande.

## **Simulation**

| Tensions extrême des panneaux | Tension de sortie  |
| ----------------------------- | ------------------ |
| 16V                           | Entre 4,6V et 7,6V |
| 22V                           | Entre 280mV et 10V |
La tension de sortie lorsque le panneau est à 16V est de maximum 7,6V. On est un peu juste, on vérifiera lors de l'essai que la tension est bien supérieur à 7,5V sinon il faudra refaire les calculs des résistances.
## **Essai en pratique** :
### *Problème avec les rapports cyclique :*

Lorsque nous avons fait les essais, nous n'arrivions plus à atteindre les rapports cyclique nécessaire car la partie puissance l'augmentais.

Alors on as du refaire tout les calculs pour Ra et Rb :

- Avec $\alpha_{min}=0,01$ soit 1% et $\alpha_{max}=0,5$ soit 50%. (Pour rappel, à la base on veut un $\alpha_{min}$ de 25% et un $\alpha_{max}$ de 50% ) :
- $R_{A} = \frac{\alpha_{min*P}}{\alpha_{max}-\alpha_{min}}= 204\ohm$, on utilise une résistance normalisé E12 de 220 $\ohm$;
- $R_{B}=\frac{R_{a}-\alpha_{min}(R_{a}+P)}{\alpha_{min}}=9800\ohm$, on utilise une résistance normalisé E12 de 10k$\ohm$.

### *Nouveau rapports cycliques obtenue :*

lorsque le panneau est à 16V on as :
- un rapport cyclique minimum de 8,7%<<25%,
- et un rapport cyclique maximal de 54%>50%

L'intervalle des rapports cycliques corresponds bien au cahier des charges !

### *Mise en garde :*

ATTENTION, le rapport cyclique maximal ne doit pas être trop grand devant le rapport cyclique maximal demandé car il y a un risque de bruler des composants. 
Il faut donc vérifier que les composant supportes la tension reçu lorsque on augmente le potentiomètre.

## **Validation de l'ensemble du montage**

Le montage finale fonctionne très bien : le potentiomètre permet d'obtenir un intervalle de tension de sortie comprenant les tensions nécessaire à l'alimentation de la carte Altéra et du Téléphone.

| Tensions extrême des panneaux | Le rapport cyclique de la tension de sortie de l'oscillateur varie | Tension de sortie   |
| ----------------------------- | ------------------------------------------------------------------ | ------------------- |
| 16V                           | entre 19,3% et 50%                                                 | Entre 4,6V et 7,76V |
| 22V                           | entre 22% et 42,5%                                                 | Entre 280mV et 10V  |
On remarque que la tension de sortie à 16V est de 7.76V, c'est un peu juste mais suffisant.

On atteint les 7,5V nécessaire à l'alimentation de la carte Altera. De plus on peut fournir les 5V demandé pour charger le téléphone.

Le montage est donc validé, il peut être utilisé sans aucun problème.

## **Conclusion**

On est dorénavant capable de fournir une tension nécessaire à l'alimentation d'un téléphone ou d'une carte altéra avec un seule panneau solaire.

De plus, on as adapté le montage de façon à ce que la tension puissent fournir la charge nécessaire afin de respecter le cahier des charges : Vs = 7,5V et Vs=5V
# 6. Schéma et Routage :

## **Schéma spice**

![[Alimentation-Photovoltaique/Rapport par fonction/Montage global/Montage final.png]]
La partie "alim.commande" et "oscillateur" sont assemblés par nous-mêmes tandis que la partie hacheur nous est déjà fournis sur une carte appart.
## **Schéma et Routage Eagle**

![[Schéma Eagle montage final.png]]![[Routage .png]]
Nous utilisons des special_drill_banane pour utiliser des câbles bananes afin de faire la connexion entre la carte commande et la carte puissance.
# 7. Conclusion :
## **7.1) Technique :**

### 7.1)a) Résumé du projet

L’objectif de ce projet était de concevoir une alimentation à découpage alimentée par un panneau photovoltaïque (PV) de **20V - 10W**, capable de fournir :

- **5V - 1A** pour un téléphone,
    
- **7,5V - 0,8A** pour une carte Altera DE1.
    

Pour mener à bien ce projet, nous avons utilisé **Micro-Cap** pour les simulations et **Eagle** pour le routage de la carte de commande. Une carte hacheur nous a été fournie, et nous avons conçu une carte additionnelle utilisant un **NE555** pour générer le signal de commande du hacheur.
### 7.1)b) Déroulement du projet

Le projet s'est déroulé conformément au plan initial. Nous avons adopté une approche modulaire, ce qui nous a permis d’aborder chaque étape de manière organisée :

1. **Étude du fonctionnement d’un hacheur et de la modulation PWM.**
    
2. **Simulation du circuit sous Micro-Cap** pour vérifier les performances théoriques.
    
3. **Conception et routage de la carte de commande sous Eagle.**
    
4. **Fabrication et test du circuit final** avec la carte hacheur.
    
### 7.1)c) Résultats et analyse

Les résultats finaux **correspondent aux attentes**. Nous avons réussi à obtenir les tensions de sortie prévues en ajustant le rapport cyclique du signal PWM. Cependant, un problème notable a été identifié :

- Le système conçu **n’est pas pratique pour un utilisateur lambda**, car il nécessite de **mesurer la tension de sortie avec un multimètre** et d’ajuster le rapport cyclique via un potentiomètre.
- Pour **remédié à cela**, il aurait fallu ajouter un système automatique qui met le potentiomètre dans la bonne position. L'utilisateur aurais juste à appuyer sur un switch pour changer la tension de sortie.

Donc notre système fonctionne très bien malgré son manque de simplicité d'utilisation qui pourrais empêcher une personne sans compétences en électronique de l'utiliser correctement.

## **7.2) Personnelle**

### RUIZ Evan :

Ce projet m'as plu car j'ai pu développer mes compétences dans la conception d'un système.
De plus, j'ai bien aimé le fait de trouver des solutions par moi-même.
Par rapport au premier semestre je pense m'être améliorer dans la rédaction des rapports, j'ai veillais à mettre plus de phrase tout en gardant l'intégration d'images et tableaux permettant de simplifier la lecture des résultats.

Si on me demandai de le refaire je pense que j'aurais utiliser la même méthode, cependant j'aurais prévus beaucoup plus de marge pour le calcul des résistances. En effet, à plusieurs reprise on as du recalculé la valeur des résistances car les rapports cyclique théorique était bien loin de la pratique.

J'aurais utilisé les même logiciel et fait les mêmes simulations car elles nous ont permis de mieux comprendre le fonctionnement de chaque fonction et des composants les intégrants.

### TALENT Julien :

#### **Intérêt du projet**

Ce projet a été **très intéressant**, car il m’a permis de découvrir le fonctionnement d’une alimentation à découpage et de **concevoir un oscillateur**.

#### **Apports techniques et personnels**

J’ai gagné en autonomie pour la réalisation de futurs projets et je me sens **plus confiant dans ma capacité à mener un projet à bien**.

#### **Améliorations possibles**

Si je devais refaire ce projet, je prendrais plus de temps pour réfléchir aux **tolérances des composants** à prendre en compte.

Pour améliorer le système, il serait intéressant de :

- **Intégrer toute l’électronique sur une seule carte** pour plus de compacité.
    
- **Automatiser la régulation de la tension** afin d’éviter l’ajustement manuel.
    
- **Ajouter un interrupteur de sélection** pour choisir l’appareil à alimenter (téléphone ou carte Altera DE1) sans besoin de réglage manuel.
    

#### **Conclusion**

Ce projet a été une **expérience enrichissante** tant sur le plan technique que personnel. Il m’a permis de mettre en application des notions théoriques et d’améliorer ma méthodologie de travail. Avec quelques améliorations, cette alimentation pourrait être plus ergonomique et accessible à un utilisateur non expérimenté.