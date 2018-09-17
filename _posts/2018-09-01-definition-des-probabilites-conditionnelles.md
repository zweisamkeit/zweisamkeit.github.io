---
id: 744
title: Définition des probabilités conditionnelles
date: 2018-09-01T18:13:10+00:00
author: Zweisamkeit
layout: default
permalink: /definition-des-probabilites-conditionnelles/
categories:
  - Mathématiques
---
# Définition fondamentale des probabilités conditionnelles

Pourquoi multiplie-t-on en probabilités lorsqu'il s'agit de calculer la probabilité de l'intersection de deux événements ?

Cette pratique découle de la définition des probabilités conditionnelles.

Définition : Soient ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  --> et ![](/img/47e9e5702768445a5891c67722c722ea.png)<!--  B  --> deux événements de l'univers ![](/img/34c6da428034553dc0ad5bc43dafe7cb.png)<!--  \Omega  -->, avec ![](/img/afbeb404551d2873caad9a3376b27752.png)<!--  A\ne \emptyset  -->.
La probabilité que l'événement ![](/img/47e9e5702768445a5891c67722c722ea.png)<!--  B  --> se réalise sachant que l'événement ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  --> a été réalisé est appelée probabilité conditionnelle de ![](/img/47e9e5702768445a5891c67722c722ea.png)<!--  B  -->  sachant ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  -->, est notée ![](/img/b0f5b8745894830a1d90225ee6114224.png)<!--  \mathbb{P}_A(B)  --> et vaut ![](/img/a5df25b911946931ca5033f498d88f6c.png)<!--  \frac{\mathbb{P}(A\cap B)}{\mathbb{P}(A)}  -->.

Nous allons tenter d'expliquer cette définition a priori peu intuitive et de montrer en quoi elle justifie l'emploi de la multiplication.

Explication : Soient ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  --> et ![](/img/47e9e5702768445a5891c67722c722ea.png)<!--  B  --> deux événements de l'univers ![](/img/34c6da428034553dc0ad5bc43dafe7cb.png)<!--  \Omega  -->, avec ![](/img/afbeb404551d2873caad9a3376b27752.png)<!--  A\ne \emptyset  -->.
Remarque liminaire : calculer ![](/img/b0f5b8745894830a1d90225ee6114224.png)<!--  \mathbb{P}_A(B)  --> revient à calculer ![](/img/fc1718a3210b63527d09ae087bd6c423.png)<!-- \mathbb{P}(B) --> dans ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  -->, vu comme un nouvel univers correspondant à l'ensemble des issues possibles une fois que ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  --> a été réalisé.

Par définition de la probabilité, nous avons,

![](/img/7914dc231403e4ae4ad5621604442594.png)<!--  \mathbb{P}(B)=\frac{\text{nombre de cas favorables}}{\text{nombre de cas prossibles}} = \frac{\vert A\cap B\vert}{\vert A\vert}  -->

Ainsi,

![](/img/d0d9f18edd2e66013940cd1315af9731.png)<!--  \mathbb{P}_A(B) = \frac{\vert A\cap B\vert}{\vert A\vert} =  \frac{\vert A\cap B\vert}{\vert \Omega\vert} \times \frac{\vert \Omega\vert}{\vert A\vert} = \frac{\vert A\cap B\vert}{\vert \Omega\vert} \div \frac{\vert A\vert}{\vert \Omega\vert} -->.

Nous avons donc réintroduit ![](/img/34c6da428034553dc0ad5bc43dafe7cb.png)<!--  \Omega  --> et, en remarquant que

![](/img/1fd0bab0ea63ffea94eb04ab1d3c5c31.png)<!-- \mathbb{P}(A\cap B) = \frac{\vert A\cap B\vert}{\vert \Omega\vert} \text{ et que } \mathbb{P}(A) = \frac{\vert A\vert}{\vert \Omega\vert} -->,

nous pouvons en déduire que

![](/img/bf8d9fc0d1999022eadb34dc7cd4cecf.png)<!--  \mathbb{P}_A(B) = \frac{\mathbb{P}(A\cap B)}{\mathbb{P}(A)}  -->.

Corolaire : De la définition que nous venons d'expliquer, il vient que

![](/img/fa3cbce3c6733e6e542a7646d174d48f.png)<!--  \mathbb{P}(A\cap B)  = \mathbb{P}(A) \times \mathbb{P}_A(B) -->.

ce qui explique que la multiplication soit utilisée pour calculer la probabilité de l'intersection de deux événements.

Par ailleurs, dans le cas où les événements ![](/img/80d9073f34a77c9632a7b2238d0adb85.png)<!--  A  --> et ![](/img/47e9e5702768445a5891c67722c722ea.png)<!--  B  --> sont indépendants - c'est-à-dire lorsque ![](/img/8a9f90ff151f8013cebe90f0f23a231f.png)<!--  \mathbb{P}_A(B) = \mathbb{P}(B) --> -, nous avons

![](/img/ce0fc437fc66b912e347e1b3cda1ddec.png)<!--  \mathbb{P}(A\cap B)  = \mathbb{P}(A) \times \mathbb{P}(B) -->.

Ainsi, la probabilité de l'événement "Obtenir deux fois le nombre 6 lors de deux lancés indépendants d'un dé à six faces non pipé" vaut ![](/img/3744560f63f7361c0308b4b3f6e84795.png)<!-- \frac{1}{6}\times \frac{1}{6}  --> et non ![](/img/febfffbb374a5613fc532a2af421bc44.png)<!-- \frac{1}{6} + \frac{1}{6}  -->.
