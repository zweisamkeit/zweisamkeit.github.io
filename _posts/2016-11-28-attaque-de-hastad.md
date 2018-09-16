---
title: Attaque de hastad
date: 2016-11-28T07:41:38+00:00
author: Zweisamkeit
layout: default
permalink: /attaque-de-hastad/
categories:
  - Cryptanalyse
---
La plupart de failles de sécurité relatives aux algorithmes de cryptographie modernes sont dites « side-channel attack », c'est-à-dire dues à des facteurs externes, tels que les implémentations et utilisations qui en sont faites. Nous allons étudier l'Attaque de Hastad, aussi connue sous le nom de « Broadcast Attack », qui profite d'une faille d'utilisation du cryptosystème RSA.
1. Attaque de Hastad


Soit ![](/img/8a94f8e86e1c77a7c8afafcfd67e2b2d.png)<!--  P  --> un message envoyé en broadcast à ![](/img/2b2914ef52f602fdb062320ed578d1be.png)<!-- k --> personnes, ![](/img/b11cbe19e80c2017adac993ff1df550b.png)<!-- k\in\mathbb{N}^* -->.

Chacune de ces ![](/img/045fc0ccd9d1ade16386b4a8d9af3816.png)<!--  k  --> personnes possède une clé publique ![](/img/0bb2e2eda11ebfc18df030a4f3c8ad3f.png)<!--  (N_i,e)  -->, avec ![](/img/8620a9d53e52c2e236387fea04fe49a9.png)<!--  N_i\in\mathbb{N}^*, i\in[\![0,k-1]\!]  --> et ![](/img/d64e69af00b53803d04d1ed927ab973c.png)<!--  e\in \mathbb{N}^* --> l'exposant public invariant dans l'ensemble des clés utilisées.

Soit ![](/img/6f5e705138583315211b76e5800cbfd5.png)<!--  pgcd : \mathbb{N}^2 \rightarrow \mathbb{N}^* --> la fonction PGCD.

On suppose par ailleurs que :

![](/img/dfe8893b1ccd52b3fe39df2d5acb4942.png)<!-- \forall i,j\in[\![0,k-1]\!],i\ne j,pgcd(N_i,N_j)=1 -->

et que :

![](/img/47f828e52eff75bbe35db6fb7ba8bdb8.png)<!-- \forall i\in[\![0,k-1]\!],P\leq N_i -->

On envoie donc ![](/img/045fc0ccd9d1ade16386b4a8d9af3816.png)<!--  k  --> fois le message ![](/img/8a94f8e86e1c77a7c8afafcfd67e2b2d.png)<!--  P  --> chiffré avec chacune des ![](/img/045fc0ccd9d1ade16386b4a8d9af3816.png)<!--  k  --> clés publiques utilisant toutes ![](/img/0833b98facb612ed2127a0de6af19b8d.png)<!--  e  --> comme exposant publique, en supposant de plus que ![](/img/88b4ef21e885ed55e2523201867630be.png)<!--  k\ge e  -->. Nous appellerons ces messages ![](/img/fd4dd9fd53d809620b4c982c36afe189.png)<!--  C_i, i\in[\![0,k-1]\!]  -->.

Supposons qu'un attaquant ait récupéré l'intégralité de ces messages chiffrés.

En reprenant la procédure de chiffrement de l'algorithme RSA, on obtient le système de congruences suivant :

![](/img/1e95d9a7001eae2a010ea3da835f7091.png)<!--  \left\{\begin{array}{l}C_0\equiv P^e [N_0]\\C_1\equiv P^e [N_1]\\ \vdots\\C_{k-1}\equiv P^e[N_{k-1}]\end{array}\right. -->

On sait que :

![](/img/fb17d344e0faceda17539e53ca8495dd.png)<!-- \forall i,j\in[\![0,k-1]\!],i\ne j,pgcd(N_i,N_j)=1 -->.

Ainsi, d'après le Théorème des Restes Chinois, on a :

![](/img/8f6674195930a7c9a004dc7290445733.png)<!-- \exists !X\in[\![0,N-1]\!]:\forall i\in[\![0,k-1]\!],X\equiv C_i [ N_i] -->, avec ![](/img/ef2f2be2291914bcb8806bd5f46a3e0d.png)<!-- N=\prod\limits_{i=0}^{k-1}N_i -->

On a de plus :

![](/img/ccc13a2df822bb5a70340a47876fe22c.png)<!--  \forall i\in[\![0,k-1]\!], pgcd(N_i,\frac{N}{N_i})=1 -->, car ![](/img/5bc065a91a27c338923d718ffe582738.png)<!--  \forall j\in[\![0,k-1]\!],j\ne i, pgcd(N_j,N_j)=1 -->.

Ainsi, d'après le Théorème de Bachet-Bézout,

![](/img/82ec76141026329a1d80a8342034bbd0.png)<!--  \forall i\in[\![0,k-1]\!],\exists (U_i,V_i)\in\mathbb{Z}^2:U_iN_i+V_i\frac{N}{N_i}=1 -->

Soit ![](/img/7ca7fdcdb618c45b11b7798adb4360bc.png)<!-- i\in[\![0,k-1]\!] -->. Nous recherchons ici l'inverse modulaire de ![](/img/91fd6bcf888aab77c64a817fb3d0bffd.png)<!-- \frac{N}{N_i} --> modulo ![](/img/3cd3158009cdb06dac38ed02e103d84c.png)<!-- {N_i} -->, à savoir ![](/img/01ab4aad9db132f072f23565ece878a2.png)<!-- V_i --> dans notre identité de Bézout. Pour ce faire, il suffit d'appliquer l'algorithme d'Euclide étendu à ![](/img/91fd6bcf888aab77c64a817fb3d0bffd.png)<!-- \frac{N}{N_i} --> et ![](/img/3cd3158009cdb06dac38ed02e103d84c.png)<!-- {N_i} -->.

Ainsi, nous obtenons la division euclidienne ![](/img/0273029f0d40ead897850f9a1a334fa4.png)<!-- V_i\frac{N}{N_i}=U_i\times N_i+1 -->. Autrement dit, ![](/img/b4bd3d56eeca26ad53b4f9e3afb9c0de.png)<!-- V_i\frac{N}{N_i}\equiv 1 [N_i] -->.

Nous avons donc :

![](/img/f73539f48a1e4286737e2b2b8ef6b114.png)<!--  \left\{\begin{array}{l}V_i\frac{N}{N_i}\equiv 1[N_i]\\ \forall j\in [\![0,k-1]\!],j\ne i,V_i\frac{N}{N_i}\equiv 0 [N_j] \end{array}\right. -->

car

![](/img/514c0fada5725294f8825f4dff17c137.png)<!-- \forall j\in [\![0,k-1]\!],j\ne i,\exists k'\in\mathbb{Z}^*:V_i\frac{N}{N_i}=k'\times N_j  -->.

Nous pouvons ainsi proposer une solution modulo ![](/img/b11dddd55d3af5891c7baea4a9a116a2.png)<!-- n --> au système de congruences obtenu à partir du Théorème des Restes Chinois :

![](/img/36616e8488fe93bd7639cb87572a9e0b.png)<!-- X=\sum\limits_{i=0}^{k-1}C_iV_i\frac{N}{N_i} -->

En conséquence, on a ![](/img/3c4f8ee7db06a3d86a76093ea665efd6.png)<!-- X=P^e [N] -->.

Or, sachant que ![](/img/3280c3142b29e376e064338994887f28.png)<!--  \forall i\in[\![0,k-1]\!], P \leq N_i --> et que ![](/img/efb2a78e939051e0e0dba08e43b26e44.png)<!--  k \geq e  -->, on a ![](/img/fbdd3ae1f594e1eac8c6f94296508b4d.png)<!-- P^e\leq N -->.

Autrement dit, on a ![](/img/47b69a649d18e310c16779c88f9213e2.png)<!-- X=P^e -->.

Il nous suffit donc de déterminer la racine ![](/img/fc86f08db1e2e747a5f0d87d62e27731.png)<!-- e -->-ième de ![](/img/ae841fad7141f34ef547ac09e79fd21e.png)<!-- X --> pour obtenir le message déchiffré ![](/img/3a58cab8a867cf4dd00a1495b4281a40.png)<!-- P -->.

Pour résumer, l'Attaque de Hastad permet de casser un chiffrement RSA quelle que soit la taille des clés, sous réserve que l'on ait intercepté le même message chiffré avec différentes clés publiques utilisant toutes le même exposant publique, et que le nombre de messages interceptés soit au moins égal à cet exposant.
2. Implémentation


Nous avons réalisé deux implémentations de l'attaque de Hastad. La première en Bash, dans le cas particulier où ![](/img/ec81776d083158ebd0b6d3f192cc4633.png)<!-- e=3 --> (3 étant le seul exposant usuel à être relativement petit, il semble déraisonnable, avec la puissance de calcul d'un ordinateur personnel, de tenter une attaque sur des exposants plus élevés, tels que 65537), et la seconde en python, dont nous recommandons l'utilisation, qui, elle, n'impose aucune contrainte sur l'exposant.

Dans la démonstration suivante, nous avons fait en sorte que le plaintext fasse 256 octets, soit la taille d'un bloc de chiffrement RSA 2048, en le suffixant de nombreux espaces. Ainsi, openssl n'applique pas de padding aléatoire PCKS#1 v1.5, ce qui compromettrait la condition d'égalité stricte entre les messages à chiffrer qui seraient de la forme : 00 02 [ RANDOM PADDING ] 00 [ PLAINTEXT ].

Les clés et les messages ont été générés avec le script gen.sh disponible dans le répertoire Linux.

zweisamkeit@linux [ RSHack ] --> ./rshack.py 


        ~~~~~~~~~~~~~~~~~~~~~~~~~
                  RSHack         
               Zweisamkeit       
            Licence GNU GPL v3   
        ~~~~~~~~~~~~~~~~~~~~~~~~~



    List of the available attacks:


        1. Wiener Attack
        2. Hastad Attack
        3. Fermat Attack
        4. Bleichenbacher Attack
        5. Common Modulus Attack

        6. RSA public key parameters extraction

    What attack do you want to carry out? 2

             ***** Hastad Attack *****

        Arguments ([-h] -k0 path_to_key0 -k1 path_to_key1 -k2 path_to_key2 -c0 cipher1 -c1 cipher2 -c2 cipher3): -k0 public0.pem -k1 public1.pem -k2 public2.pem -c0 15[...]23 -c1 16[...]16 -c2 12[...]98


    ~~~~~~~~~~~~~~~~~~~~~~~~~
          Hastad Attack      
           Zweisamkeit       
        GNU GPL v3 License   
    ~~~~~~~~~~~~~~~~~~~~~~~~~



    Keys paramters extraction...

    Modular inverse calculation...
    Modular inverse calculation done...

    System solution cube calculation...
    System solution cube calculation done

    System solution calculation...
    System solution calculation done

    Solution interpretation...
    Solution interpretation done

        The plaintext is: Il etait paresseux, a ce que dit l'histoire,
        Il laissait trop secher l'encre dans l'ecritoire.
        Il voulait tout savoir mais il n'a rien connu.
        
             - Gerard de Nerval, Epitaphe
