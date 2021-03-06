# One time pad

Le *One Time Pad* (OTP) aussi appelé *masque jetable* est une technique simple mais sûr.

A ne pas confondre avec le One Time Password.

## Concept

L'OTP est une technique de cryptographie qui ne peut pas être cassée mais qui requiert l'utilisation d'une clé partagée à utilisation unique de la même longueur que le message à envoyer.

La clé est aussi appelée *pad* ou *masque*.

Chaque bit/octet du message est chiffré en le combinant avec le bit/octet correspondant de la clé secrète (*one-time pad*) en utilisation l'addition modulaire (*A + B mod C*).

### Exemple

+ Message : taille **n** octet
+ Pad : taille **n** octet aléatoire
+ Alphabet : taille **x**
+ Chiffrement : `(message + clé) mod 26`
+ Déchiffrement : `(message - clé) mod 26`

![](src/otp-Diagram.svg)

Chiffrement :

```
07 (H)  04 (E)  11 (L)  11 (L)  14 (O)  message
+
23 (X)  12 (M)  02 (C)  10 (K)  11 (L)  clé
=
30      16      13      21      25      message + clé
=
04 (E)  16 (Q)  13 (N)  21 (V)  25 (Z)  (message + clé) mod 26
```

Déchiffrement :

```
004 (E) 16 (Q)  13 (N)  21 (V)  25 (Z)  texte chiffré
-
023 (X) 12 (M)  02 (C)  10 (K)  11 (L)  clé
=
-19     4       11      11      14      texte chiffré - clé
=
007 (H) 04 (E)  11 (L)  11 (L)  14 (O)  message
```

### Tentative de Cryptanalyse

+ Texte chiffré : `EQNVZ`
+ Clé n°1 : `XMCKL` -> message n°1 : `HELLO`
+ Clé n°2 : `TQURI` -> message n°2 : `LATER`

Si un attaquant possède un temps infini et qu'il peut essayer toutes les combinaisons de clé possibles, il sera en mesure de trouvé plusieurs clés avec un message plausible.

En fait, il est possible de décrypté tous les messages possibles avec le même nombre de caractères, mais il n'y a aucune information dans le texte permettant de choisir le bon message.

Dès que le message est grand, la clé étant de même taille, il devient impossible de tester toutes les possibilités (ex: clé de 50Mo), et le nombre de texte possible devient aussi démesurément grand.

## Sécurité parfaite

Le one-time pad est le seul chiffrement respectant les critères de sécurité inconditionnelle (un attaquant ne peut pas obtenir d'information sur le texte clair à partir du texte chiffré et donc l'algorithme est incassable par Cryptanalyse).

La sécurité d'OPT est parfaite car pour une clé donnée, parfaitement aléatoire et utilisée seulement une fois, le message chiffré peut être traduit en tous les messages chiffrés de même taille, et ils sont tous équiprobable.

La probabilité qu'une message en clair soit le bon est égale à la probabilité qu'un message en clair soit le bon étant donné le message chiffré.

Cela peut être écrit:
+ `H(M) = H(M|C)`
+ `H(M)` : entropie
+ `H(M|C)` : entropie conditionnelle

## Histoire

D'abord décrit par Frank Miler en 1882 pour sécuriser le télégraphe, l'OTP a été ré-inventé en 1917. Le 22 juillet 1919, un brevet états-uniens a été déposé par Gilbert Vernam sur l'opération XOR utilisée pour le chiffrement d'OTP.

L'OTP est un dérivé du chiffrement de Vernam mais la forme originelle du chiffrement de Vernam était vulnérable car la clé était répétée en boucle.

Le mot *pad* vient des premières implémentations où le support des clés était des bloc-notes (*pad of paper*).

## Problèmes

Malgré que sa sécurité soit très forte le one-time pad possède de sérieux inconvénient en pratique car il requiert:
+ Des pad parfaitement aléatoires (non pas pseudo-aléatoires), ce qui est difficile à produire en informatique.
+ Une génération et un échange des pad de manière sécurisée
+ Les pad doivent être au moins de taille égale au message
+ Garder les pad secrets, sachant qu'un pad ne doit être utilisé qu'une fois, il va falloir garder beaucoup de pad et veiller à ce qu'ils ne soient pas ré-utilisés.
+ Difficultés à complètement effacer les données sur un média informatique (ex: traces des pad en mémoire)

A l'heure d'aujourd'hui, il existe plusieurs algorithmes dont la sécurité n'est pas considérée comme menacée qui sont beaucoup plus facile à utiliser qu'OTP en pratique.

### Distribution des clefs

Le pad est comme un secret il doit être donné et gardé de manière sécurisé, et il doit être aussi long que le message.

Donc pourquoi ne pas directement envoyer le message plutôt que le pad puisque les deux font la même taille et qu'ils doivent être envoyés de manière sécurisé ?

Par contre on peut s'envoyer un long pad (ex: un disque dur plein de données aléatoires) qui pourra être utilisé pour plusieurs échange jusqu'à ce que la somme de leur tailles fassent celle du pad.

Comme les clés sont très grandes ils faut souvent les transporter sur des supports comme un DVD, une clé USB ou un disque dur mais transporter le pad physiquement reste tout de même beaucoup plus contraignant comparé aux algorithme de négociation de clé.

Et plus le réseaux est grand plus il sera difficile de se partager les clés.

### Authentification

Le mode de fonctionnement du One-time-pad implique qu'il propose pas de service d'authentification. Un attaquant connaissant une partie du message pourrait donc déterminer une partie de la clef grâce à la combinaison du clair et du chiffré connus.
Il pourrais ainsi utiliser cette partie de clef connus et réécrire une partie du message, sans que cette modification n'ai pu être détectée.

Des méthodes d'authentification classiques peuvent donc êtres utilisées en plus de one-time-pad, par exemple:
- Hashage classique
- MAC: Code d'authentification de message (Semblable au hash du message (message + clef secrète))
- Russian copulation (Ré-arrangement de message)

Cependant ces techniques d'authentification ne disposent pas de la qualité d'être parfaitement sûres comme one-time pad.

### Véritable aléa

Générer des nombre vraiment aléatoire est difficile. La plupart des lib ne peuvent pas être utilisées même les générateurs de nombre aléatoires physique qui peuvent utiliser des fonctions crytpo qui n'ont pas été prouvé.

Le pad ne doit surtout pas être ré-utilisé sinon de simples opérations mathématiques peuvent réduire l'OTP à une simple substitution polyalphabétique. Et si le message est dans un langage naturel alors il sera probable de réussir à retrouver le message par de la cryptanalyse heuristique.

## Cas d'utilisations

### Applicabilité

- Malgré ses problèmes le One-time-pad conserve un certain intéret pratique. Il est notamment utile car il est possible de l'utiliser "à la main", là ou la pluparts des autres algorithmes sont impossibles à appliquer sans l'aide d'ordinateurs.
Dans le monde de l'espionnage un espion pourra se voir remettre une liste des clefs à utiliser et ainsi chiffrer/déchiffrer ses messages sans avoir besoin de matériel risquant d'attirer l'attention.
- Sa capacité à être applicable à la main était très intéressante lorsque les ordinateurs n'existait pas encore. Et elle le reste encore aujourd'hui lorsque, par exemple, aucun matériel informatique de confiance n'est disponible.
- Il est notamment utilisé dans le processus de distribution quantique de clefs.
- Les algorithmes de chiffrement de flux imite le fonctionnement du One-time-pad. Ces algorithmes sacrifient la propriété d'être parfaitement sécurisés au profit d'une utilisation plus aisée. Notamment en permettant la réutilisation d'une clef de petite taille pour chiffrer plusieurs messages.

### Utilisations historiques

One-time-pad est connu pour avoir été utilisé, depuis les années 1900, pour les communications spéciales des états:

- Il a été utilisé pour les échanges diplomatiques. Dès 1923 en Allemagne  ainsi que pour la mise en place du téléphone rouge entre Washington et Moscou.
- Il a été utilisé par les services secrets de multiples pays dans le monde depuis sa création.
- Au même titre que les agents secrets et les forces spéciales des armées du monde entier ont eu recours à One-time-pad pour transmettre leurs messages.
- Dès la seconde guerre mondiale il a été implémenté sous forme de machines à ruban perforés. D'abord à des fins militaires et ensuite développer pour le monde civil après la guerre.

![Noreen machine](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Noreen.jpg/1280px-Noreen.jpg)

### Exploitations

+ Many Time Pad Attack – Crib Dragging

## Conclusion

Les algorithmes de chiffrement asymétriques dépendent des problèmes mathématiques difficiles à résoudre comme la factorisation d'entier ou les logarithmes discrets. Cependant il n'y a pas de preuve que ces problèmes sont dur, et une avancée mathématique (comme les mathématiques quantiques) pourrait rendre le système vulnérable.

Les algorithmes de chiffrement symétriques utilisent des combinaisons de substitutions et de transpositions. Pour les meilleurs d'entre eux, on ne sait pas si il existe un moyen cryptographique de faire l'inverse de ces procédés sans connaître la clé utilisée.

OTP est le seul algorithme prouvé comme sûr.

OTP est donc une bonne alternative lorsque l'on a besoin de garantir le chiffrement de message très confidentiel et que l'on peut respecter les pré-requis.

## Sources

+ [Wikipedia One-time pad](https://en.wikipedia.org/wiki/One-time_pad)
+ [Wikipedia Information-theoric security](https://en.wikipedia.org/wiki/Information-theoretic_security)
+ [Wikipedia Entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory))
+ [Wikipedia Conditional entropy](https://en.wikipedia.org/wiki/Conditional_entropy)
+ [Wikipedia Authentication](https://en.wikipedia.org/wiki/Authentication)
+ [Wikipedia Russian copulation](https://en.wikipedia.org/wiki/Russian_copulation)
+ [Wikipedia Universal hashing](https://en.wikipedia.org/wiki/Universal_hashing)
+ [Wikipedia Data remanence](https://en.wikipedia.org/wiki/Data_remanence)
+ [Wikipedia Running key cipher](https://en.wikipedia.org/wiki/Running_key_cipher)
+ [Blog: Many Time Pad Attack Crib Dragging](http://travisdazell.blogspot.fr/2012/11/many-time-pad-attack-crib-drag.html)
