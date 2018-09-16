---
title: Cryptanalyse du Chiffre de Vigenère
date: 2016-11-27T09:02:32+00:00
author: Zweisamkeit
layout: default
permalink: /cryptanalyse-du-chiffre-de-vigenere/
categories:
  - Cryptanalyse
  - Substitutions
---
Le Chiffre de Vigenère est un algorithme de substitution poly-alphabétique dont le fonctionnement est assez proche de celui du Chiffre de César. La différence se situe dans la taille de la clé : le Chiffre de Vigenère prend également un message clair en entrée, mais, contrairement à César, la clé n'est pas nécessairement constituée d'un unique caractère, mais peut en contenir plusieurs. Le message chiffré est obtenu en appliquant un décalage circulaire sur chaque élément du plaintext, en prenant pour offset la position dans l'alphabet considéré des éléments de la clé répétée autant de fois que nécessaire.

Pour être plus clair, considérons l'alphabet latin ![](/img/dd223e2a4864e4d16d07e7d5ce8b847a.png)<!--  A = \{A,B,C,..,X,Y,Z\}  -->, un plaintext ![](/img/59844d1e66b4b4f7b185b5b937f27147.png)<!-- P \in A^n -->, avec ![](/img/2248b88d5dbc03c00fd16b6b03e93afe.png)<!-- n \in \mathbb{N}^* -->, et la clé ![](/img/c8008694fe9d76b5d6ffe0424fcdfd6e.png)<!-- K \in A^m -->, avec ![](/img/6c22b5a2a4c40cc855076cbc0231628c.png)<!-- m \in \mathbb{N}^* -->.

![](/img/d49ed4717c59f4d7eb7d94c46c5a0054.png)<!-- P --> et ![](/img/08f06271caa863a48e43931ac3a59d4b.png)<!-- K --> peuvent s'écrire comme la concaténation de ![](/img/b2f6e69e011c2b31a664012f56b65cfe.png)<!-- n --> éléments de ![](/img/1947923bdd0b133dc1b35e120e8ff9f8.png)<!-- A --> :

![](/img/22589955ac4076d35ed51344658394cc.png)<!-- \left\{\begin{array}{l}P = p_0p_1...p_{n-1}\ (\forall i \in [\![0,n-1]\!], p_i \in A) \\K = k_0k_1...p_{m-1}\ (\forall i \in [\![0,m-1]\!], k_i \in A)\end{array}\right. -->

Soient ![](/img/95890ff6ae3f1bc0627abf094b59fad0.png)<!--  position : A \rightarrow [\![0,n-1]\!] --> une fonction renvoyant la position dans ![](/img/1947923bdd0b133dc1b35e120e8ff9f8.png)<!-- A --> du caractère passé en argument, et ![](/img/ab0c5e0f9315878920a9524b9de05a53.png)<!--  lettre : [\![0,n-1]\!] \rightarrow A  --> la fonction réciproque.

Notons ![](/img/440a7796a601a0f5862efe28f84e376a.png)<!--  C\in A^n --> le message chiffré à produire décomposé de la même manière que ![](/img/234f3927f14ca75a9a013f64279affe3.png)<!--  P  --> :

![](/img/29e25030e8672fe02a54255f3f36231d.png)<!--  C = c_0c_1...c_{n-1}\ (\forall i \in [\![0,n-1]\!], c_i \in A) -->

Alors on a :

![](/img/176327b2b49e76730c34974bac4a75cb.png)<!--  \forall i \in [\![0,n-1]\!], c_i = lettre((position(p_i)+position(k_{i\ mod\ m}))\ mod\ 26) -->

De manière analogue, pour le déchiffrement, on a :

![](/img/a8cbee2ef3e47e5c86caeb6bb23fd6f6.png)<!--  \forall i \in [\![0,n-1]\!], p_i = lettre((position(c_i)-position(k_{i\ mod\ m}))\ mod\ 26) -->

La cryptanalyse du Chiffre de Vigenère est un peu plus compliquée que celle du Chiffre de César.

Dans un premier temps, il s'agit de trouver le taille de la clé ![](/img/817aa9d65d900251cd55685129c3bc61.png)<!--  K  -->, notée  ![](/img/6b20269491aea4ca25e98420c56c7828.png)<!-- m --> au début de l'article, et inconnue dans notre situation.

Pour cela, introduisons la notion d'indice de coïncidence, noté ![](/img/05e72c64027524bb4c80e2ead2a2f933.png)<!--  I_c  -->, en considérant que, ![](/img/fc5f6574574182a2a2c637f9bd3c244e.png)<!-- \forall i \in [\![0,26]\!], n_i --> correspond au nombre de répétitions de la lettre  ![](/img/cd95e102657de6b28f667568cf41d99e.png)<!-- lettre(i) --> dans ![](/img/557350dc0f9717f3378abf55ad26189b.png)<!-- C --> :

![](/img/3801f8897c16eafc8b9cf3ea51e0b36f.png)<!--  I_c=\sum\limits_{i=0}^{26} \frac{n_i(n_i-1)}{n(n-1)} -->

Cet indice correspond à la probabilité que deux éléments d'un texte donné tirés aléatoirement soient identiques. Chaque langue a un indice de coïncidence spécifique. Notons ![](/img/f69834bcc86f38710374f779504e4c83.png)<!--  I_l  --> l'indice de coïncidence de la langue utilisée.

Par la suite, on utilise le Test de Friedman :

On décompose le cryptogramme en ![](/img/6b20269491aea4ca25e98420c56c7828.png)<!-- m --> listes de sous-cryptogrammes, que nous noterons ![](/img/2833c46518208b52f87e71669c3b762b.png)<!-- C_{i,*}\ ,i\in[\![0,n-1]\!] --> :

![](/img/a263662c0978dfbdda16277053fec8c4.png)<!--  \forall i \in [\![0,n-1]\!], C_{i,*}=\{c_0...c_{\frac{n}{i}},c_{\frac{n}{i}+1}...c_{\frac{2n}{i}},...,c_{\frac{(i-1)n}{i}}...c_n\} -->

Et on note :

![](/img/581eb935ad2e7c19b72d4da459a6f578.png)<!--  \forall i \in [\![0,n-1]\!],j\in[\![0,i-1]\!] , C_{i,j}= c_{\frac{jn}{i}+1}...c_{\frac{(j+1)n}{i}} -->

Pour chaque liste, on calcule l'indice de coïncidence des différents sous-cryptogrammes qui la composent, puis on calcule la moyenne de ces indices, notée ![](/img/fa6e07259f418e7d842669fb1fb3fbdd.png)<!--  I_{c,i}, \forall i \in [\![0,n-1]\!] -->

L'indice le plus proche de l'indice de référence indiquera la longueur de clé la plus probable :

![](/img/711295daf81b6f1a749c27e4563fea48.png)<!-- \min\limits_{i\in[\![0,n-1]\!]} |\ I_{c,i}-I_l\ | -->

Une fois la taille ![](/img/ef02f95c5b0d751c08cb0934707a17a2.png)<!--  m  --> de la clé connue, il suffit de décomposer le cryptogramme en ![](/img/6b20269491aea4ca25e98420c56c7828.png)<!-- m --> sous-cryptogrammes de la manière suivante :

![](/img/15054f40efeb0820eb7fa0ee7b420ca3.png)<!--  \forall i \in [\![0,m-1]\!], Cs_{i}=c_ic_{m+i}c_{2m+i}...  -->

Sur chacun de ces sous-cryptogrammes, on effectue une analyse fréquentielle relative à la langue utilisée afin de déterminer le décalage « local », nous permettant ainsi de recomposer le sous-plaintext correspondant, ainsi que nous l'avons fait pour le Chiffre de César.

Enfin, il suffit de reconstituer ![](/img/234f3927f14ca75a9a013f64279affe3.png)<!--  P  --> à partir des sous-plaintext obtenus.

Voici une implémentation en C de cette méthode ce cryptanalyse : [Cryptanalyse du Chiffre de Vigenère](https://github.com/zweisamkeit/Security/tree/master/Cryptanalysis/Vigenere)
```
zweisamkeit@linux [ Vigenere ] --> ./decrypt_vigenere ciphertext plaintext fr


		~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

		Cryptanalysis of the Chiffre de Vigenère

		           Zweisamkeit -- 2016

		             License GNU/GPL

		~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Probable length of the key : 5

Probable key  : RILKE

zweisamkeit@linux [ Vigenere ] --> cat plaintext 
Dites vos tristesses et vos désirs, les pensées qui vous viennent,
votre foi en une beauté. Dites tout cela avec une sincérité intime,
tranquille et humble. Utilisez pour vous exprimer les choses qui
vous entourent, les images de vos songes, les objets de vos souvenirs.
Si votre quotidien vous paraît pauvre, ne l’accusez pas. Accusez-vous
vous-même de ne pas être assez poète pour appeler à vous ses richesses.
Pour le créateur rien n’est pauvre, il n’est pas de lieux pauvres,
indifférents. Même si vous étiez dans une prison, dont les murs
étoufferaient tous les bruits du monde, ne vous resterait-il pas
toujours votre enfance, cette précieuse, cette royale richesse,
ce trésor des souvenirs ?

- Lettres à un jeune poète, Rainer Maria Rilke
```
