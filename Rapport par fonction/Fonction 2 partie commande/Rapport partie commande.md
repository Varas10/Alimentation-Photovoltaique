TALENT Julien et RUIZ Evan

# Rôle de la fonction : 

Elle permet de choisir la tension de sortie en modulant la largeur de l'impulsion (la pwm).

# Schéma :
![[Pasted image 20250316112551.png]] 
Sans le potentiomètre

![[Pasted image 20250316100859.png]]
avec le potentiomètre
# Fonctionnement :

Grâce au potentiomètre on peut modifier la largeur des impulsion ce qui permet de modifier le rapport cyclique.

La diode permet de laisser passer ou non le courant en fonction de si le condensateur est en charge ou décharge.

En charge le courant passe par RA et la diode, et en décharge il passe par RB et va dans le 555

# Choix des résistances :
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

# Ajout d'un potentiomètre :

| évolution de la tensions par rapport à l'ensolellement | il faut malgré tout avoir une tension en sortie du hacheur VS de : | rapport cyclique correspondant | position du potentiomètre  |
| ------------------------------------------------------ | ------------------------------------------------------------------ | ------------------------------ | -------------------------- |
| descend à 16V                                          | 7.5V                                                               | maximal de 47%                 | basse (x=100%)             |
| monte à 22V                                            | 5V                                                                 | minimal de 23%                 | haute (x=0)                |
## calcul de RA et Rb

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

<span style="color:rgb(192, 0, 0)">FAIRE LES TEST EN SIMULATION </span>
# Mesures et chronogrammes

## Problème !

Notre coupe RA-RB nous empêchez de descendre en dessous de 26% de rapport cyclique or on souhaite définir un rapport cyclique de 20% donc on change les résistances.

On obtient 
- RA =4.3k$\ohm$ et RB = 14k$\ohm$. 
On utiliseras des résistances normalisées E12 : 
- RA = 3.9k$\ohm$ et Rb = 15k$\ohm$.

## P en position basse(Potentiomètre à 100%) :

En position basse, le rapport cyclique doit être maximal 


### calculs : 

| tension VC | la tension de sortie Vs est à l'état | formule  (p=10k)                   | rapport cyclique (pratique) : | Rapport cyclique (théorie) : |
| ---------- | ------------------------------------ | ---------------------------------- | ----------------------------- | ---------------------------- |
| monte      | haut pendant 11.4$\micro$s           | $t_{1}=0.693(RA+P)C = 9.6\micro s$ | 51%                           | 48%                          |
| descend    | bas pendant 10.8$\micro$s            | $t_{2}=0.693RB*C = 10.3\micro s$   |                               |                              |


## P en position haute(Potentiomètre à 0%) :

En position haute, le rapport cyclique doit être le minimum voulu :

### calculs : 

| tension VC | la tension de sortie Vs est à l'état | formule  (p=10k)                   | rapport cyclique (pratique) : | Rapport cyclique (théorie) : |
| ---------- | ------------------------------------ | ---------------------------------- | ----------------------------- | ---------------------------- |
| monte      | haut pendant 44$\micro$s             | $t_{1}=0.693(RA+P)C = 2.7\micro s$ | 19.6%                         | 13.5%                        |
| descend    | bas pendant 16.4$\micro$s            | $t_{2}=0.693RB*C = 17.3\micro s$   |                               |                              |

