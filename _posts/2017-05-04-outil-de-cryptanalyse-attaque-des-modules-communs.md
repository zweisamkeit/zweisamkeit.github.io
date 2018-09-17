---
title: RSA - Attaque des Modules Communs
date: 2017-05-04T23:43:42+00:00
author: Zweisamkeit
layout: default
permalink: /cryptanalyse-rsa-attaque-des-modules-communs/
categories:
  - Cryptanalyse
  - RSA
---
# RSA - Attaque des Modules Communs

Parmi les nombreuses attaques side-channel  applicables à certaines utilisations maladroites du cryptosystème RSA, l'attaque des modules communs, dont l'existence est rendue possible pas une mauvaise génération de clés, se place parmi les plus simples à mettre en place, mais n'en est pas moins redoutable. Nous allons ici en développer l'idée, et en proposer une implémentation.
1. Attaque des modules communs


Soient ![](/img/e0ceae3ab9934ee34fb4bb54f20b656d.png)<!-- (n_A,e_A), (n_B,e_B) --> deux clés publiques RSA appartenant respectivement à A et B, deux récepteurs, et ![](/img/7564892b7fbee05d327210a3988a7a9c.png)<!-- p --> un message à transmettre, chiffré par un émetteur avec chacune de ces deux clés. Notons ![](/img/a22e8f58dccbee94b057aa1099641ec6.png)<!-- c_A --> et ![](/img/075dfb3ae460f2e10e49d6a43a191f63.png)<!-- c_B --> les deux cryptogrammes ainsi obtenus.

Nous avons alors :

![](/img/ce65261546d0572a57e7673f75792eb4.png)<!--  \left\{\begin{array}{l}c_A\equiv p^{e_A} [n_A]\\c_B\equiv p^{e_B}[n_B]\end{array}\right. -->


L'attaquant connaît la valeur des clés publiques de A et B, et a intercepté les deux messages chiffrés.

Supposons par ailleurs que les modules des clés sont communs, c'est-à-dire que ![](/img/c6a512a0bbe0b9be56a32393004fde01.png)<!-- n_A=n_B --> (appelons-les ![](/img/26b5ec7e463d1df48dad9170ebcf2220.png)<!-- n -->), et que les deux exposants publics sont premiers entre eux, c'est-à-dire que ![](/img/d22fe745de5da7e35ebc0230d3bd6ca9.png)<!-- pgcd(e_A,e_B)=1 -->.

D'après le théorème de Bachet-Bézout,

![](/img/955bb860380e2a7a504017b078ffb77a.png)<!-- \exists (u,v)\in\mathbb{Z}^2:e_Au+e_Bv=1 -->

Par conséquent, nous avons :

![](/img/723cf16d0bc6cc4633fa489153d923b1.png)<!-- \left\{\begin{array}{rcl}c_A^u [n] \times c_B^v [n]&\equiv &((p^{e_A})^u [n] \times (p^{e_B})^v [n]) [n]\\ & \equiv & p^{e_Au+e_Bv} [n]\\ &\equiv &p [n]\\ &= &p\text{ car, par construction, }p \leq n\end{array}\right. -->

Nous avons donc montré que l'attaquant n'a qu'à calculer le produit ![](/img/a51c8817aaed19e065cc9d8de0875cf1.png)<!-- c_A^u [n] \times c_B^v [n] --> pour obtenir le message en clair, sans disposer des clés privées de A et B.
2. Implémentation


Dans l'implémentation que nous proposons, nous avons pris une précaution qu'il convient de préciser : dans le calcul du produit sur lequel repose l'attaque des modules communs, il est important de noter que les calculs ont lieu dans l'anneau ![](/img/3bda007ec49ccdb382f4377f176979e5.png)<!-- \mathbb{Z}/n\mathbb{Z} -->, si bien qu'une optimisation conséquente peut être mise en place afin de réduire de manière considérable le temps d'exécution : l'exponentiation modulaire, gérée nativement par le langage python.

Dans l'exemple d'utilisation suivant, deux paires de clés publiques sont instanciées de telle sorte qu'elles ont le même module n, et des exposants premiers entre eux. Un texte est chiffré avec chacune d'elles, et le programme comod.py réalise une attaque par modules communs, dévoilant ainsi le message initial.

```python
# Key generation
>>> p=6468246416824864441327
>>> q=122488752143253143415671
>>> n=p*q
>>> eA=65537
>>> eB=263 # pgcd(eA,eB)=1

# Plaintext

>>> pt=1015285799393168187692228316901635442 # decimal value

# Cipher

>>> cA=pow(pt,eA,n)
>>> cB=pow(pt,eB,n)
>>> cA
40337798257696192889062357882574022113567716
>>> cB
459998051675062019977695880586159168424442224
```

```bash
# Common Modulus Attack
zweisamkeit@linux [ RSHack ] --> ./rshack.py 



        ~~~~~~~~~~~~~~~~~~~~~~~~~
                  RSHack         
               Zweisamkeit       
                GNU GPL v3       
        ~~~~~~~~~~~~~~~~~~~~~~~~~



    List of the available attacks:

        1. Wiener Attack
        2. Hastad Attack
        3. Fermat Attack
        4. Bleichenbacher Attack
        5. Common Modulus Attack
        6. Chosen Plaintext Attack

    List of the available tools:

        7. RSA Public Key parameters extraction
        8. RSA Private Key construction
        9. RSA Ciphertext Decipher
        10. RSA Ciphertext Encipher

    What attack or tool do you want to carry out? 5

             ***** Common Modulus Attack *****

         Arguments [-h] -n common modulus -e1 first exponent -e2 second exponent -c1 first cipher -c2 second cipher:

            -n 792287432151946079584633822001040067951835417 -e1 65537 -e2 263 -c1 40337798257696192889062357882574022113567716 -c2 459998051675062019977695880586159168424442224


        ~~~~~~~~~~~~~~~~~~~~~~~~~
          Common Modulus Attack  
               Zweisamkeit       
            GNU GPL v3 License   
        ~~~~~~~~~~~~~~~~~~~~~~~~~


    [+] Decimal plaintext:  1015285799393168187692228316901635442 

    [+] Interpreted plaintext:  Éternel retour
```
