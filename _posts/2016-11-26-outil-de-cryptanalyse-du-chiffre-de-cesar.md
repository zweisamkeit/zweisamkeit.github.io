---
title: Cryptanalyse du Chiffre de César
date: 2016-11-26T19:18:42+00:00
author: Zweisamkeit
layout: default
permalink: /cryptanalyse-du-chiffre-de-cesar/
categories:
  - Cryptanalyse
  - Substitutions
---
# Cryptanalyse du chiffre de César

Le Chiffre de César est un algorithme de cryptographie dit de substitution mono-alphabétique qui prend en entrée un message et une clé de chiffrement sous la forme d'une lettre de l'alphabet. Un décalage circulaire de chaque caractère alphabétique est opéré selon un offset égal à la position de la clé dans l'alphabet.

Soient l'alphabet latin ![](/img/dd223e2a4864e4d16d07e7d5ce8b847a.png)<!---  A = \{A,B,C,..,X,Y,Z\}  --->, un plaintext ![](/img/59844d1e66b4b4f7b185b5b937f27147.png)<!--- P \in A^n --->, avec ![](/img/2248b88d5dbc03c00fd16b6b03e93afe.png)<!--- n \in \mathbb{N}^* --->, et la clé ![](/img/bc8d48482408f30bc6f81c8ca520595c.png)<!--- K \in A --->.

![](/img/3b86bce1a7c7066ffd6cc158757c5f31.png)<!--- P ---> peut s'écrire comme la concaténation de ![](/img/09c8549da7b94951d4ae5ee005b82d4e.png)<!--- n ---> éléments de ![](/img/7435062052addb8b5dffaf90e5f34640.png)<!--- A ---> :

![](/img/9f3d1113e8088078d8b3ebf230c0a92f.png)<!---  P = p_0p_1...p_{n-1}\ (\forall i \in [\![0,n-1]\!], p_i \in A) --->

Soient ![](/img/d6eada2260f9e9aa9c8544c69142864c.png)<!---  position : A \rightarrow [\![0,n-1]\!] ---> une fonction renvoyant la position dans ![](/img/7435062052addb8b5dffaf90e5f34640.png)<!--- A ---> du caractère passé en argument, et ![](/img/a0a3b20fe9f2bb327fa34dc7cbdb11f9.png)<!---  lettre : [\![0,n-1]\!] \rightarrow A  ---> la fonction réciproque.

Notons ![](/img/834175e2e20855da95da5bf1166903ad.png)<!---  C\in A^n ---> le message chiffré à produire décomposé de la même manière que ![](/img/a2a78a4c79794b4b000e01f3dbc04a3b.png)<!---  P  ---> :

![](/img/dcc581a652a806a6d69e92d8977b6686.png)<!---  C = c_0c_1...c_{n-1}\ (\forall i \in [\![0,n-1]\!], c_i \in A) --->

Alors on a :

![](/img/f77ec8a1226fbd8f8ed846c7ef24a020.png)<!---  \forall i \in [\![0,n-1]\!], c_i = lettre((position(p_i)\ +\ position(K))\ mod\ 26) --->

De manière analogue, pour le déchiffrement, on a :

![](/img/a23e1e3f38edbbcd0acdeac736dc61d5.png)<!---  \forall i \in [\![0,n-1]\!], p_i = lettre((position(c_i)\ -\ position(K))\ mod\ 26) --->

Une fois le message chiffré, il est très simple de le cryptanalyser : il suffit d'effectuer une analyse fréquentielle sur le ciphertext afin de déterminer la clé utilisée, et de déchiffrer le ciphertext pour cet offset.

En voici une implémentation en Python et en Bash : [Cryptanalyse du Chiffre de César](https://github.com/zweisamkeit/Security/tree/master/Cryptanalysis/Ceasar)

Le wrapper dceaser_as.sh va, dans un premier temps, appeller dceaser.py pour chaque offset possible sur le fichier contenant le ciphertext passé en argument, et écrire les plaintext potientiels dans des fichiers dédiés situés dans le répertoire Results.
Ensuite, il exécute freq_an.py sur le ciphertext, qui va effectuer une analyse fréquentielle afin de déterminer l'offset le plus probable.
Enfin, le script va afficher une partie du texte déchiffré avec l'offset obtenu précédemment, et proposera soit d'en afficher l'intégralité, soit d'afficher une overview de l'ensemble des textes obtenus lors de l'attaque par force brute, dans le cas où l'analyse fréquentielle aurait échoué.
```
zweisamkeit@Armory [ Ceasar ] --> head -n 5 ciphertext 
O ibs doggobhs - Qvofzsg Psoirszowfs

Zo fis oggcifrwggobhs oihcif rs acw vifzowh.
Zcbuis, awbqs, sb ufobr rsiwz, rcizsif aoxsghisigs,
Ibs tsaas doggo, r'ibs aowb toghisigs
zweisamkeit@Armory [ Ceasar ] --> 
zweisamkeit@Armory [ Ceasar ] --> ./dceasar_as.sh ciphertext


	~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	Cryptanalysis of the Ceasar Cipher
	        Zweisamkeit -- 2016       
	          License GNU/GPL
	~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  The probable shift is 14

  Overview for the probable shift:

	A une passante - Charles Beaudelaire


  Do you want to see all the text for this shift (1),

  or an overiew of the other results (2)? [1/2/exit] 1



  A une passante - Charles Beaudelaire
  
  La rue assourdissante autour de moi hurlait.
  Longue, mince, en grand deuil, douleur majestueuse,
  Une femme passa, d'une main fastueuse
  Soulevant, balancant le feston et l'ourlet ;
  
  Agile et noble, avec sa jambe de statue.
  Moi, je buvais, crispe comme un extravagant,
  Dans son oeil, ciel livide ou germe l'ouragan,
  La douceur qui fascine et le plaisir qui tue.
  
  Un eclair... puis la nuit ! - Fugitive beaute
  Dont le regard m'a fait soudainement renaitre,
  Ne te verrai-je plus que dans l'eternite ?
  
  Ailleurs, bien loin d'ici ! trop tard ! jamais peut-etre !
  Car j'ignore ou tu fuis, tu ne sais ou je vais,
  O toi que j'eusse aimee, o toi qui le savais !
```
