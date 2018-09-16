---
id: 198
title: 'Outil de Cryptanalyse : Attaque de Bleichenbacher'
date: 2016-12-16T12:25:53+00:00
author: Zweisamkeit
layout: default
permalink: /outil-de-cryptanalyse-attaque-de-bleichenbacher/
categories:
  - Cryptanalyse
  - RSA
---
L'attaque de Bleichenbacher, applicable sur de nombreux protocoles reposant sur l'algorithme de cryptographie asymétrique RSA, permet de décrypter un message chiffré par RSA, sous réserve qu'un oracle nous indique si le padding d'un cryptogramme donné est correct ou non.

Soit une paire de clés RSA ![](/img/78517ef389c102edcfc497fea171e963.png)<!-- ((n,e), (n,d)) -->, ![](/img/42087100699a995b624941d0032560e7.png)<!-- n, e\text{ et }d --> représentant respectivement le module de la clé, l'exposant publique, et l'exposant privé.

Considérons de plus un message ![](/img/e48ef751b3214a12c7435e6f787a432c.png)<!-- M -->, et ![](/img/3ccd37bb56095d2ada10342713267168.png)<!-- C \equiv M^d \text{ mod }n --> le cryptogramme correspondant.

Notons ![](/img/6ed773568f66cc0ef15ac22718c50459.png)<!--  \mathcal{A}^n --> l'ensemble des mots de longueur inférieure à ![](/img/b1923295e9f3947504e873049f97b025.png)<!-- n --> de l'alphabet ![](/img/fbc2080d9ec4235e48d25c75cccc73ca.png)<!-- \mathcal{A} --> contenant tous les caractères usuels, ![](/img/4bb85bebf11937f74ff4f15b4f253106.png)<!-- \mathcal{C}  --> l'ensemble des cryptogrammes pouvant être générés à l'aide de la clé privée ![](/img/dbcc17f32291d703343acc718d0355e9.png)<!-- (n,d) -->, ![](/img/b2a4f90c6b49e43b39a3389f50604f88.png)<!-- E : \mathcal{A}^n \rightarrow \mathcal{C}, M \rightarrow M^e\text{ mod }n --> la fonction de chiffrement RSA, et ![](/img/59b541525e962217dc6e89edc9105b8d.png)<!--  D : \mathcal{C}\rightarrow \mathcal{A}^n, C\rightarrow C^d\text{ mod }n --> la fonction de déchiffrement RSA.

L'attaquant dispose du message chiffré ![](/img/60c156015f8e4f315493389d54bd8394.png)<!-- C -->, et a la possibilité de soumettre un message à un oracle qui va déchiffrer ce ciphertext, et nous informer de la validité du padding. Notons ![](/img/25078ba75ba4ed592da3bbc0a6df79ad.png)<!-- Oracle : \mathcal{C} \rightarrow \{True, False\} --> la fonction permettant d'effectuer une telle requête à l'oracle.

Rappelons également une propriété du chiffrement RSA qui nous sera très utile au cours de cette attaque, en conservant les notations précédemment introduites :

![](/img/66eae69ebdc7945aaa8ab585a76b3034.png)<!-- \forall M_1,M_2 \in \mathcal{A}^n,E(M_1)\times E(M_2)\equiv E(M_1\times M_2 \text{ mod } n)\text{ mod }n -->

En effet, on a, ![](/img/b28858e575cb5e618bedb92a5bc266b2.png)<!-- \forall M_1,M_2 \in \mathcal{A}^n --> :

![](/img/182bb903d1b77f9d9872ef9de47471b1.png)<!-- \begin{array}{rcl}E(M_1)\times E(M_2) &\equiv &(M_1^e \text{ mod }n \times M_2^e\text{ mod }n)\text{ mod }n\\&\equiv &(M_1^e\times M_2^e\text{ mod }n)\text{ mod }n\\&\equiv &((M_1\times M_2)\text{ mod }n)^e\text{ mod }n\\&\equiv &E(M_1\times M_2\text{ mod }n)\text{ mod }n\end{array} -->

 
### L'algorithme


L'algorithme sur lequel repose cette attaque se décompose en quatre parties :

 
1. Blinding


Il s'agit, dans un premier temps, de faire en sorte que l'oracle ne puisse pas prendre connaissance du cryptogramme que l'on essaye de déchiffrer. Pour ce faire, nous allons générer aléatoirement des entiers positifs ![](/img/0d0052b3255cd87396e61b459dc90d39.png)<!-- s_0 --> jusqu'à ce que ![](/img/a9da00e6d63c3f77ce25bbe7574b5266.png)<!-- Oracle(C\times E(s_0)\text{ mod }n) --> nous retourne vrai, c'est-à-dire jusqu'à ce que ![](/img/e7b6cba8c39344b399b0c59863e5e1ff.png)<!-- E(M\times s_0) --> soit conforme. Nous appellerons ![](/img/c39e6fcd5997697e7a1cfabdf258ed6a.png)<!-- C_0 --> le cryptogramme obtenu à l'issue de cette première étape.

Notons ![](/img/d195b265bf319771f1f665318df44dad.png)<!-- k --> la longueur en bytes de n, et ![](/img/2c59cf97b850d2d204f5b6f714eb1ad7.png)<!-- B=2^{8(k-2)} -->

 
2. Searching for PKCS conforming messages


Bleichenbacher distingue alors trois cas :

* Soit nous effectuons l'itération initiale de l'algorithme, et alors il s'agit de rechercher le premier ![](/img/75a9c599f4ed3ec03eab7497ff62d3ed.png)<!-- s \geq \lceil \frac{n}{3B}\rceil --> tel que l'on ait ![](/img/411e48aac2b56cbd255c1315f6825a4c.png)<!-- Oracle(C_0\times E(s)\text{ mod }n) -->, puis de poser ![](/img/e9d19280f89dff6a46becefb1595bbad.png)<!-- L = {[2B, 3N-1]} -->, sachant que les ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L --> seront des listes d'intervalles dont l'un encadrera le message en clair recherché ;
* Soit nous effectuons une itération non-initiale, et la longueur courante de ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L --> est supérieure à 2, et alors nous recherchons le premier ![](/img/7f67e27be79d8fe1c48df4aa711d7f00.png)<!-- s --> supérieur à la valeur du ![](/img/7f67e27be79d8fe1c48df4aa711d7f00.png)<!-- s --> obtenu lors de l'itération précédente, tel que l'on ait ![](/img/411e48aac2b56cbd255c1315f6825a4c.png)<!-- Oracle(C_0\times E(s)\text{ mod }n) --> ;
* Soit nous effectuons une itération non-initiale, et la longueur courante de ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L --> est égale à 1. Nous avons donc un unique intervalle ![](/img/85d7744aa4a18c018bb47a855a1fa1af.png)<!-- [a,b],(a,b)\in\mathbb{N}^2 -->, si bien que l'on peut utiliser une recherche binaire optimisée afin de trouver la prochaine valeur de ![](/img/7f67e27be79d8fe1c48df4aa711d7f00.png)<!-- s --> telle que l'ont ait ![](/img/411e48aac2b56cbd255c1315f6825a4c.png)<!-- Oracle(C_0\times E(s)\text{ mod }n) -->. Il s'agit tester les ![](/img/7f67e27be79d8fe1c48df4aa711d7f00.png)<!-- s --> compris entre ![](/img/21c0f70b2bb51d88817f11030ac27184.png)<!-- \lfloor\frac{2N+rn}{b}\rfloor --> et ![](/img/7fb81ae58e851343cd7429110c1ddd0d.png)<!-- \lceil\frac{3B + rn}{a}\rceil -->, avec ![](/img/5cf786104de044774a0034f8fc42b813.png)<!-- r\geq \lceil2\times\frac{bs-2B}{n}\rceil -->.



3. Narrowing the set of solutions


Une fois que nous avons trouvé ![](/img/7f67e27be79d8fe1c48df4aa711d7f00.png)<!-- s --> et que nous avons une liste d'intervalles ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L -->, nous allons créer une nouvelle liste ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L --> contenant des intervalles plus précis, et moins nombreux dans le cas où l'on a plusieurs intervalles.

Pour ce faire, il nous faut considérer chaque intervalle ![](/img/85d7744aa4a18c018bb47a855a1fa1af.png)<!-- [a,b],(a,b)\in\mathbb{N}^2 --> contenue dans ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L -->, et, pour ![](/img/4ac0eac09658731cdca2330c272656d6.png)<!--  r\in[\lceil\frac{as-3B+1}{n}\rceil,\lfloor\frac{bs-2B}{n}\rfloor] -->,

* Trouver le minimum ![](/img/89a826c64431c17e390d9591dc23ab4a.png)<!-- min_a --> entre ![](/img/24cbce03a578cca4780d38dbc2429755.png)<!-- a --> et ![](/img/d39a1f1612308502a335a21bba786177.png)<!-- \lceil\frac{2B+rn}{s}\rceil --> ;
* Trouver le maximum ![](/img/3316384a4443659df84af7316d098062.png)<!-- max_b --> entre ![](/img/ff722294c631e187a2482ff2382418b1.png)<!-- b --> et ![](/img/b7f20ba2e6f3f346979956e48547bb03.png)<!-- \lceil\frac{3B-1+rn}{s}\rceil --> ;
* Ajouter l'intervalle ![](/img/500899305b2daa08e8bb159ced1390f4.png)<!-- [min_a,max_b] --> à la nouvelle liste ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L --> que l'on est en train de former uniquement si ![](/img/8931d63f2f6a8d8d72a009512c7aed7b.png)<!-- max_b \geq \min_a -->.

Ainsi, au fur et à mesure des itérations, nous nous débarrasserons des intervalles superflues, afin d'obtenir, à terme, un unique intervalle qui pourra être affiné très rapidement grâce à la recherche binaire, jusqu'à ce que les deux bornes de celui-ci soient égales.

 
4. Computing the solution


Une fois la nouvelle liste obtenue, on distingue deux cas :

* Soit ![](/img/f64b86dbfb445e9db0e9693497758491.png)<!-- L --> est composé d'un unique encadrement ![](/img/85d7744aa4a18c018bb47a855a1fa1af.png)<!-- [a,b],(a,b)\in\mathbb{N}^2 --> d'amplitude nulle, et alors on obtient directement le message en calculant ![](/img/47c2cc64d99f9fae4297945dbb16b17b.png)<!-- a\times s_0^{-1} \text{ mod }n -->, le modulo inverse de ![](/img/0d0052b3255cd87396e61b459dc90d39.png)<!-- s_0 --> pouvant être facilement calculé à l'aide de l’algorithme d'Euclide étendu ;
* Soit on retourne à l'étape 2.

Pour plus de précisions, nous vous invitons à lire l'article original de Bleichenbacher.

 
### L'implémentation


L'implémentation de cet algorithme repose sur une optimisation bien connue : l'exponentiation modulaire. En effet, dans la pratique, l'exposant ![](/img/08fbbff44d3f28d597d403f387e3868b.png)<!-- e --> peut valoir 65537, et les ![](/img/7f67e27be79d8fe1c48df4aa711d7f00.png)<!-- s --> sont souvent composés de plus d'une quinzaine de chiffres, ce qui peut rentre le calcul de ![](/img/4f2c52782a019d1c060f2a51b4c05f07.png)<!-- s^e \text{ mod }n --> particulièrement long si l'on n'applique le modulo qu'à la fin de calcul, comme nous avons pu le voir dans certaines implémentations en Python (e ** s % n). Nous avons choisi d'utiliser la fonction built-in pow, qui permet d'effectuer les calculs modulo ![](/img/b1923295e9f3947504e873049f97b025.png)<!-- n -->, ce qui évitera de manipuler des nombres supérieurs à ![](/img/b1923295e9f3947504e873049f97b025.png)<!-- n --> lors de l'exponentiation.

Voici une démonstration de notre implémentation en Python : [Attaque de Bleichenbacher (désormais intégré à RSHack)](/rshack.html), dans le cas particulier d'un module de 2048 bits, avec un exposant publique égal à 65537, sur un unique bloc de 256 bytes. Les caractères non-imprimables affichés avant le message en clair correspondent au padding mis en place par openssl selon la norme PCKS#1 v1.5.
```
zweisamkeit@linux [ Bleichenbacher ] --> ncat -lvp 4444 -e ./oracle.py --keep-open 1>/dev/null 2>&1 &
zweisamkeit@linux [ Bleichenbacher ] --> openssl genrsa 2048 > private.key 2>/dev/null
zweisamkeit@linux [ Bleichenbacher ] --> openssl rsa -pubout < private.key > public.key 2>/dev/null
zweisamkeit@linux [ Bleichenbacher ] --> module=$(openssl rsa -pubin -in public.key -modulus 2>/dev/null | grep Modulus | cut -d '=' -f 2)
zweisamkeit@linux [ Bleichenbacher ] --> exposant=$(openssl rsa -in public.key -pubin -text -noout | grep Exponent | cut -d ' ' -f 2)
zweisamkeit@linux [ Bleichenbacher ] --> message=$(echo -ne "Elle t’a si tendrement serrée à la gorge que tu en as gardé pour toujours l’envie de pleurer.")
zweisamkeit@linux [ Bleichenbacher ] --> cryptogramme=$( echo -en $message | openssl rsautl -encrypt -inkey private.key | xxd -ps | tr -d '\n')
zweisamkeit@linux [ Bleichenbacher ] --> time ./bleichenbacher.py $module $exposant $cryptogramme localhost 4444 "Padding Error"


	~~~~~~~~~~~~~~~~~~~~~~~~~
	Attaque de Bleichenbacher
	       Zweisamkeit       
	    Licence GNU GPL v3   
	~~~~~~~~~~~~~~~~~~~~~~~~~


	Établissement de la connexion à l'Oracle...
	Connexion à l'Oracle établie.

	Lancement de l'attaque...

	Brouillage en cours...
	Brouillage terminé.

	Construction et affinage des encadrements en cours...
	Construction et affinage des encadrements terminés.

	Le message en clair est : 

          [Random Padding] Elle t’a si tendrement serrée à la gorge que tu en as gardé pour toujours l’envie de pleurer.
	Fin de l'attaque.

	Connexion cloturée.


real	3m11,328s
user	0m35,287s
sys	0m1,880s
```
