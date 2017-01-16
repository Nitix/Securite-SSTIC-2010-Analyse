\begin{titlepage}
	\centering
	{\scshape\LARGE Université de Lorraine \par}
	\vspace{1cm}
	{\scshape\Large M2 MIAGE Systèmes d’informations distribués\par}
	{\scshape\Large Sécurité\par}
	\vspace{1.5cm}
	{\huge\bfseries Analyse forensique\par}
	\vspace{2cm}
	{\Large\itshape Mathieu Mourot\par}
	{\Large\itshape Guillaume Pierson\par}
	\vfill
	License\par
	\textsc{CC BY 4.0}
	\vfill

% Bottom of the page
	{\large \today\par}
\end{titlepage}

\newpage
  \pagenumbering{roman}
  \tableofcontents
\newpage
\pagenumbering{arabic}

# Présentation du sujet

## SSTIC

L'objectif du Symposium sur la sécurité des technologies de l'information et des communications (SSTIC) est de provoquer
 la rencontre et la discussion entre ces publics très variés, autour de présentations sur l'état actuel de la sécurité informatique en France et dans le monde.
 
## Objectif du challenge
 
Avec une image de mémoire d’un téléphone sous Android, retrouver un mail de la forme @sstic.org et y envoyer un mail pour valider la résolution du challenge.
Pour ce challenge deux critères ont été pris en compte, la rapidité et la qualité du rapport.

## Rappel de ce qu'est une analyse forensique

C’est l’action d’acquérir, de recouvrer, de préserver, et de présenter des informations traitées par le système d’information et stockées sur des supports informatique.

Elle fait suite à un incident tel que la compromission d’un serveur ou est utilisé dans d’autres cas tels que la recherche de preuves pour la pédocriminalité ou l’analyse après une infection par un malware

# Présentation d'une possible solution

Lors de la rédaction de ce rapport, nous avons allégé la présentation, celle d'origine fait 64 pages sans prendre en compte les annexes du fichier pdf. 

<http://static.sstic.org/challenge2010/ebalard.pdf>

## Analyse du fichier récupéré

Outils utilisés :

* file
* 7z
* strings

Le fichier récupéré est une archive qui contient la mémoire du système Android.

Ensuite, une fois la mémoire extraite, on recherche des occurrences avec le sujet sstic et anssi grâce à la commande strings. On trouve deux résultats, deux applications android (apk).

## Récupération des APKs

Pour la récupération des APKs à partir de la mémoire, il y a eu plusieurs possibilités reconstituer toute la mémoire, ou une approche à partir de la particularité des fichiers APKs.

En effet, les APKs ressemblent à des JAR du fait qu'Android utilise un environnement très semblable à celui de Java. On peut donc en déduire que les APKs sont en fait des fichiers archivés au format ZIP.

Les fichiers sont donc sous la forme : 	

* ZIP End of Central Directory Record
* ZIP Central Directory File Header
* ZIP Local File Header
		
![Format du fichier zip](images/zip.png "Format du fichier zip")




***********************

#====================
#
# Git anti conflict
#
#====================

************************

## Utilisation de l'émulateur Android

### Installation des APK dans l'émulateur

Les APK précédement récupéré peuvent être installé sur l'émulateur grâce à l'outil adb (répertoire tools) présent dans les outils du SDK:
> $ cd tools #Change le repertoire pour tools
> $ ./tools/adb start-server
> $ ./tools/adb install com.anssi.textviewer.apk
> $ ./tools/adb install com.anssi.secret.apk

### Analyse des applications

#### TextViewer

Cette application nous montre un message chiffré. A premiere vu on voit que le message est chiffré avec un décalage de lettre (type Rot13) puisque la ponctuation semble être conserver et le nombre de lettre aussi. Suite à ce qui semble etre une lettre on a un message dont les symboles font penser à un message chiffré avec PGP.
Exemple de texte d'origine : 
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.10 (GNU/Linux)

Texte chiffré:
-----CXZES JPW PVUEEH ENF BMHVG-----
Ayazipg: ZjzJP c1.4.10 (GON/Eesog)

Avec ces indices on peut voir les rotations effectuer. Il ne s'agit pas d'un nombre fixe mais d'un cycle de rotation 1, 19, 19, 22, 5, 20, 9, 7 et 0. Grâce a un script qui change ces lettres (un des participant l'a fait en python) on récupère le vrai message:

Cher participant,
Pour retrouver le trésor, rends toi aux lieux énigmatiques suivants :
- Après les rump sessions, rendez-vous chez ce galliforme breton
- l'abandonnée de Naxos y part pour d'autres cieux
- le frère de Marvin y est né, sous la grosse table ?
- le nez de ce gigantesque capitaine ne fut libéré qu'en 1993
Une fois arrivé à chaque endroit, valide ta position dans l'application.
Rajoute ensuite le mot de passe, il est chiffré en GPG.
Enfin, valide le tout, pour la suite, tu verras :)
-----BEGIN PGP MESSAGE-----
Version: GnuPG v1.4.10 (GNU/Linux)
hQEOA5BaqIyWyerQEAP9GC73TFXOyby3E49/0G4yvHmJtHnStoVubGN/Siqgc6C1
4yJOEpiYCGu9j1/IFQDo9GGDzk2GnNRmI+XK9l0hpx6sX2ddfHbfxaJOgCJRHQmd
ssh3uIGXr4pJO4QJ9FCugOT5AM8jVXD2+Rj6k9BVC8C8LORcKhTx9uUGx9Ag2lwD
/R7i4U257WCsZbuF5FqY3qOCl9ELlTJl94qb08+62fJEv5aBepyxeLQcF97kRZ66
L2M1INN4748DWq0vM1UByl0JjpTda1V4OnPeHcUF2Z0DmUguYriG5X0FIFBXi38i
eELY2pT3LuIYWwkPh5uVOxa/kVsWaIkidRI5Z3eaL/0nhIwDKpxhBeH2e70BA/0c
YAbu+0TEXqoPhW3qH1Mvbc1Y+jZYCPEHpo69yJHeozuwpOpVeqIixcjMXAb1j3gQ
wJsrXU4YsWn2VX1KLsOVWmWQWlMxXLDhi4jwNmsH2CZbJM72pdWubzezEb8DLfVo
Ja+96C054ipAZSNrS3D1WF5wbO2FBTa0B5Q8I/xg3ck82/+oc0yaoR54eKYF9oUw
uqy7QId+gb35oL4z6SotvOAcMNQj1cCnKNgQiYsnEhTsuv7JZjBqlm16DEzv
=vexd
-----END PGP MESSAGE-----
Je t'ai remis ma clef publique si tu veux me contacter.
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.10 (GNU/Linux)
mQGiBEug+8kRBAC1eP91t96pb/Ja59uVmbz4FuTag7eEFcoWTPUd5mqU9L0OsZH2
N1z5O5p2xeyZmYkp/FcQS8gDCjFMOjCJTxtZFmmeM0o5RbbkWnWXMnc/4PtHerfN
eTeY7mFiVOceMpkonCP7ZMTjA7Ozqq3MorgTu+VvM2iqBir/GSgeHFtsawCg4iUk
8rD+8wmQVJrSFtvLnM/yyi8D/2XCPkXczbj970CimOgsg/RvdQGnQWcmWF9vLgDZ
KPhlnzwdz2DTwuhfNGjk4SCct06NfE2+PCgufD/eNOQ+jjB5CbvKA64a1WmhUs0w
uKPags6kZ5YZC6T3Mdb2m4jIWaB95+lqGFdOu3Admua2i7NZsT3hDWCWzb6hKZA7
cwH0A/45/FoDLuD4KQtRu5OwUYj5WN3MMVrVxM13bTs3vIEZEZolOHfZI656Sz5P
25
G73IUF43ZSEi7pkxRLEyLcCpApcQCMWestZGRiOGxgy0FCD0VET1OuzOVn6eAqYz
JRqDnqpcvI4bqUe1G+NPyOgk2BxE+y7mDP5WkSG8eecRlMyWX7QkUGVyc29ubmUg
PGludmFsaWRlQG5vbWRlZG9tYWluZS50bGQ+iGcEExECACcFAkug+8kCGwMFCQCe
NAAGCwkIBwMCBhUIAgkKCwMWAgECHgECF4AACgkQfh6HQwzuRlDOPgCdHIUXw5wU
ADQBSk1gycl94cCydU0AoImWUlc6t0cufYoYUrh9d/eXq9equQENBEug+8kQBADu
Dv3tRqs7+jDJM2eGjlPg1YMScjTzafGvD3np+rIACphAYhhLH0edD7kM4KNoq39W
YkNIGqW0M4acfA9tVWr9/xcxpjjb7l2GhvBahJVAGSAw4IqrD3u6ea3WrKmkhqIa
FUZq90X5JbGc9Uj1E0ks9U5TsGoI3JSg8FZlmfavQwADBQQAihN4w1JdsLQK0q27
FgSLyG84K6iZ5mP36ZEDqrFFYe6Uxvm8PMziLIalNVJYgR4wKVGeqShQ8zlOvfj0
W7LMtqEZPDZQ8UX/bM89RFSabc/qLYDNVxlbJKfT7mFd/dwB02tpryC/OwEXDEDF
cyzg7IaSvQph4Vcc95xwQOWDfPqITwQYEQIADwUCS6D7yQIbDAUJAJ40AAAKCRB+
HodDDO5GUEA7AKDhvaeXaYoyjLLftlQY4WDssi91vgCfWWNio0oCfm0eTgCNc/im
ZBJoifI=
=8IMk
-----END PGP PUBLIC KEY BLOCK

On remarque egalement que le message contient une sous-clé ElGamal
Une attaque est donc possible vu la faiblesse de la sous-clé.



#### Secret 

Cette application quant à elle demande les 4 positions dont les indices ont été donnés précédement et un mot de passe

### Récupération des informations

Sur la sous-clé ElGamal:

Forme : y = g\exp(x) mod p
- g : le générateur du groupe (i.e. 5) donne plus haut,
- x : la partie publique ElGamal de personne
- p : le modulo
- k : la clé éphémère ElGamal utilisée pour le chifrement de la clé de session.

On peut donc utiliser un algorithme de Pohling-Hellman qui (d'après Wikipédia) est un algorithme pour résoudre le problème du logarithme discret (PLD). Il divise un PLD en sous-problèmes (tous des PLD aussi) et utilise ensuite les résultats de ces sous-problèmes pour construire la solution.

Le fichier mdp.txt peut alors être déchiffrer : O7huQcYzHEPSq82m

Pour ce qui est des 4 coordonées il s'agissaient d'énigme : 
- Coordonnées du social event SSTIC
- Coordonnées de Naxos, ile grecque de la mer Egée
- Le frère de Marvin y est né : Non trouvé il a fallut un reverse engenering de l'application
- Coordonnées de El nose d'El Capitan dans la vallée de Yosémite aux USA


On obtient alors un binaire executable si les données correct sont entrés et il donnera l'adresse mail :

> $ ./binaire
> Bravo, le challenge est terminé! Le mail de validation est : \
> 4284d974a8af53aa7a85fc4e956b2d84@sstic.org







