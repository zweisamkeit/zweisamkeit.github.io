---
title: RSA - Attaque de Fermat
permalink: /cryptanalyse-rsa-attaque-de-fermat/
date: 2016-12-29T17:24:01+00:00
author: Zweisamkeit
layout: default
categories:
  - Cryptanalyse
  - RSA
---
La solidité du chiffrement RSA repose sur la difficulté de factoriser le module d'une paire de clés donnée. En effet, il s'agit de retrouver, à partir du module obtenu lors de la création des clés en calculant le produit de deux nombres premiers, dans les faits très grands.

Une méthode naïve pourrait être de chercher l'ensemble des nombres premiers inférieurs à la racine carrée de n, et de tenter de diviser n par chacun de ces nombres de manière dynamique.

Cette méthode est évidemment très complexe : même les tests de primalité probabilistes, tels que celui de Miller - Rabin, ont une complexité polynomiale (voir ces diapositives), bien que relativement efficace. De plus, les divisions successives de grands nombres peuvent s'avérer également très coûteuse.

Le mathématicien Pierre de Fermat a proposé une méthode de factorisation des nombres impairs (en particulier des nombres premiers tels que les modules RSA) qui, dans certain cas, va s'avérer très efficace.

 
### Prérequis


Soit ![](/img/85ed301d2dec3500a4a7a6b9599251f6.png)<!-- n --> un nombre premier, obtenu en effectuant le produit de deux nombres premiers distincts ![](/img/85fd369a34bcb690cd5cc13153ab2c51.png)<!-- p --> et ![](/img/c69e06211d8d74949a67e9b16517a22a.png)<!-- q -->.

Nous avons alors :

![](/img/1ae854509fd1a4fbf27c87a26305f919.png)<!-- n = (\frac{p+q}{2})^2-(\frac{p-q}{2})^2 -->

En effet,

![](/img/9678312cf1652725634e24b2772f0200.png)<!-- \left\{\begin{array}{rrcl}&n&=&(\frac{p+q}{2})^2-(\frac{p-q}{2})^2\\ \Leftrightarrow&n&=&\frac{p^2+2pq+q^2 - p^2 + 2pq - q^2}{4}\\ \Leftrightarrow&n&=&\frac{4pq}{4}\\ \Leftrightarrow&n&=&pq\end{array}\right. -->.

Autrement dit, ![](/img/7b0b5f1a3299b85662cbc6b01ac8fb36.png)<!-- 4n = x^2-y^2=(x-y)(x+y) -->, avec ![](/img/3ac1a37d2a2c1d4dad9243e2f4c8998d.png)<!--  \left\{\begin{array}{r}x=p+q\\y=p-q\end{array}\right. -->.

Par ailleurs, nous remarquons que ![](/img/a2ff7a38227ded16704d70839f3c9a0f.png)<!--  x^2 = 4n + y^2 > n \Rightarrow x > 2\sqrt{n} -->.

En effet, par construction de ![](/img/85ed301d2dec3500a4a7a6b9599251f6.png)<!-- n -->, ![](/img/19d635840e265ab3355cf423dcf5a57c.png)<!-- y=p-q --> est non-nul.

Afin de factoriser ![](/img/85ed301d2dec3500a4a7a6b9599251f6.png)<!-- n -->, nous devons donc trouver deux entier ![](/img/fb341fe2cc1cadccb1751e5cc07c52ad.png)<!-- x --> et ![](/img/f9cc2768507371dadcfc7d11820baa3f.png)<!-- y --> tels que ![](/img/e9839b2d3cd455aee89482e5e4ab1b5c.png)<!-- 4n=x^2-y^2 -->.

 
### Méthode de Fermat


Partant de ces observation, Fermat propose l'algorithme suivant :

* Posons ![](/img/40c96e27e5acfcc9e5fbce03fa066cf2.png)<!-- x = \lceil 2\sqrt{n}\rceil -->, la première valeur de ![](/img/b5be717cba1b2ad64f2986aed2540851.png)<!-- a --> possible ;
* Tant que ![](/img/8e9e5e63f5c839135d4424bc75413f52.png)<!-- c=x^2 - 4n --> n'est pas un carré parfait, nous incrémentons ![](/img/b5be717cba1b2ad64f2986aed2540851.png)<!-- a --> de 1;
* Une fois que nous avons trouvé ![](/img/fb341fe2cc1cadccb1751e5cc07c52ad.png)<!-- x --> tel que ![](/img/d2455df22e99bf3bf19ca85fe8a079e4.png)<!-- x^2 - 4n --> est un carré parfait, nous avons la décomposition de ![](/img/85ed301d2dec3500a4a7a6b9599251f6.png)<!-- n --> en une différence de carrés : ![](/img/00181f9dcf0826f8f2c15b639fcfcb5a.png)<!--  4n = x^2 - y^2=(x-y)(x+y) --> ;
* Par identification, nous avons retrouvé les facteurs premiers de n : ![](/img/bd9e1e3daeca2df8ecaec940d6004ed5.png)<!-- \left\{\begin{array}{l}p = \frac{x + y}{2}\\q = \frac{x - y}{2}\end{array}\right. -->.

 
### Optimisation


Notons ![](/img/70d713b9358a1361823e4a4be1c63150.png)<!-- x_i --> la valeur de x à l'itération ![](/img/938d7eb55ea8a71b03bd6fc53ec0c3fc.png)<!-- i --> appartenant à ![](/img/77d75490c376733caa7fc55af6d3f2c3.png)<!-- \mathbb{N} -->.

Nous avons donc, à l'itération ![](/img/938d7eb55ea8a71b03bd6fc53ec0c3fc.png)<!-- i -->, ![](/img/73acd53befc598c1c77d343c6f2076ff.png)<!-- x_i=\lceil 2\sqrt{n}\rceil +i -->.

![](/img/b4fdc2cf942cafd6d284fd4dfdf78fdd.png)<!-- (x_i)_{\mathbb{N}} --> est une suite arithmétique de raison 1, si bien que nous obtenons la relation de récurrence suivante :

![](/img/c409fc422def35a630253f3e10d3c2ab.png)<!-- \left\{\begin{array}{r}x_0=\lceil 2\sqrt{n}\rceil\\ \forall i\in\mathbb{N}, x_{i+1}= x_i +1\end{array}\right. -->

Ainsi, nous avons, ![](/img/eb7655362bbb7d75079c9ba5ebf7b1c7.png)<!-- \forall i\in\mathbb{N} --> :

![](/img/7e97798b790eebed32bb8e48c38e1f08.png)<!-- \left\{\begin{array}{rcl}x_{i+1}^2 - 4n&=&(x_i+1)^2-4n\\\\&=&x_i^2-4n+2x_i+1\end{array}\right. -->

Ainsi, à l'itération ![](/img/82a8b9cdd39985556a716ac420c95c71.png)<!-- i+1 -->, il ne sera pas nécessaire de calculer le carré de ![](/img/cf02f957f6a6393edb37b0ccc3045e2c.png)<!-- x_{i+1} -->. En effet, il nous suffira de reprendre la valeur de ![](/img/fde5bb8d6a73ddd0927655fc3cff7823.png)<!-- x_i^2 -4n -->, et de lui ajouter ![](/img/b7483f4d56112eb74c85d73d2ca6943b.png)<!-- 2x_i+1 -->.

Nous avons donc remplacé le calcul du carré par de simples sommes.

### Implémentation

Dans notre implémentation, nous avons ajouté une fonctionnalité permettant de retrouver l'exposant privé de la paire de clés, ainsi que la reconstruction de la clé privée.

Dans l'exemple suivant, nous avons généré un module ![](/img/85ed301d2dec3500a4a7a6b9599251f6.png)<!-- n --> obtenu à partir de nombres premiers ![](/img/85fd369a34bcb690cd5cc13153ab2c51.png)<!-- p --> et ![](/img/c69e06211d8d74949a67e9b16517a22a.png)<!-- q --> vérifiant la condition ![](/img/4eda1ededf6dd9e47ea13184ccefc251.png)<!-- %VERT%p-q%VERT% \leq c\lfloor\sqrt[4]{n}\rfloor -->, avec ![](/img/3acbdbcdf3eea57742e7dffe99416a74.png)<!-- c\in\mathbb{R}^+ --> une constante suffisamment petite.

En effet, nous avons ![](/img/e6d90fce9e63c4ef27091ab4d8a24f78.png)<!-- %VERT%p-q%VERT% =%VERT%1586161373-1586935751%VERT%=774378 -->, d'une part, et ![](/img/73da89a9417a4ca152d5476e348d078b.png)<!-- \lfloor\sqrt[4]{n}\rfloor=\lfloor\sqrt[4]{2517136189668946123}\rfloor=39831 -->, d'autre part, si bien que ![](/img/9237740405559d98d04e8bc9ec820fbb.png)<!-- c = \lceil\frac{%VERT%p-q%VERT%}{\lfloor\sqrt[4]{n}\rfloor}\rceil\approx 19,44 -->.

```
zweisamkeit@linux [ RSHack ] --> ./rshack.py 


        ~~~~~~~~~~~~~~~~~~~~~~~~~
                  RSHack         
               Zweisamkeit       
            Licence GNU GPL v3   
        ~~~~~~~~~~~~~~~~~~~~~~~~~



    List of the available attacks:


        1. Wiener Attack
        2. Hastad Attack (Linux only)
        3. Fermat Attack
        4. Bleichenbacher Attack
        5. Common Modulus Attack

        6. Parameters extraction

    What attack do you want to carry out? 3

             ***** Fermat Attack *****

         Arguments ([-h] -n modulus -e exponent): -n 2517136189668946123 -e 65537




        ~~~~~~~~~~~~~~~~~~~~~~~~~
              Fermat Attack      
               Zweisamkeit       
            GNU GPL v3 License   
        ~~~~~~~~~~~~~~~~~~~~~~~~~

    n factorization: 1586161373 * 1586935751 

    Private exponent: 629120813201733473 

    Private key: 

         -----BEGIN RSA PRIVATE KEY-----
        MDoCAQACCCLuqgc0f3DLAgMBAAECCAi7FgvfsbdhAgReiubdAgRelrfHAgQxGkdp
        AgQWaJJlAgQc69EW
        -----END RSA PRIVATE KEY-----
```
