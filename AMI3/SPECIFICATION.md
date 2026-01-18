# **AMI3 — Spécification Technique**

**Norme AMI3 – Version 1.0**
Formats **AMI3-T (texte)** & **AMI3-B (binaire)**

---

## 1. Vue d’ensemble

AMI3 est un format graphique **vectoriel**, **déterministe** et **compilable**, conçu pour décrire des scènes graphiques complexes à l’aide d’instructions géométriques minimales.

AMI3 privilégie la philosophie "offload storage to computing".

Deux représentations existent :

| Format     | Description                       |
| ---------- | --------------------------------- |
| **AMI3-T** | Langage texte lisible (source)    |
| **AMI3-B** | Flux binaire optimisé (exécution) |

AMI3-T est compilé en AMI3-B pour gain de place en production.

---

## 2. Modèle d’exécution

Une image AMI3 est constituée de **couches (layers)** empilées.

Le moteur maintient un état global comprenant :

* Résolution
* Version de la norme
* Table de variables
* Couche active
* Origine locale
* Grille de répétition (GRID)
* Style et shader actifs

Les instructions sont évaluées **séquentiellement**, sans ambiguïté.

---

## 3. Types fondamentaux

### 3.1 Nombres

| Type    | Description  |
| ------- | ------------ |
| `int`   | Entier signé |
| `float` | Nombre réel  |

Les deux sont acceptés sans suffixe.

---

### 3.2 Vecteur

Représente une position ou un déplacement :

```
[x,y]
```

Exemples :

```
[500,250]
[-30,45]
```

---

### 3.3 Couleur

Format RGBA hexadécimal :

```
#RRGGBBAA
```

| Champ | Description          |
| ----- | -------------------- |
| RR    | Rouge                |
| GG    | Vert                 |
| BB    | Bleu                 |
| AA    | Alpha (transparence) |

Exemples :

```
#FF0000FF   // Rouge opaque
#00FF0080   // Vert semi-transparent
```

---

## 4. En-tête obligatoire

Tout fichier AMI3 **doit obligatoirement** commencer par :

```
HEAD:AMI3
VER:x.y
RSL:width,height
```

### Exemple :

```ami3
HEAD:AMI3
VER:1.0
RSL:1920,1080
```

### Règles

* `VER` définit la version de la norme utilisée
* Un moteur doit être rétrocompatible avec les ancienne version de la norme et ignorer les instruction inconue.
* `HEAD`, `VER` et `RSL` ne peuvent apparaître **qu’une seule fois**

---

## 5. Variables (VAR)

Les variables permettent de factoriser des valeurs numériques ou couleurs.

### Déclaration

```
VAR:name=value
```

### Utilisation

```
$name
```

### Exemple

```ami3
VAR:cx=500
VAR:mainColor=#FF00FFFF

MTF:CIR,[$cx,300],120,$mainColor
```

### Règles

* Substitution **avant exécution**
* Variables immuables
* Portée globale au fichier

---

## 6. Système de couches

```
LYR:n
```

* `n` est un entier ≥ 0
* Les couches sont rendues **dans l’ordre croissant**
* Changer de couche ne vide pas la précédente

---

## 7. Formes (MTF — Meta Transformable Forms)

### 7.1 Cercle

```
MTF:CIR,[x,y],radius,color
```

---

### 7.2 Rectangle / Carré (norme 1.1)

```
MTF:SQR,[x,y],width,height,rotation,cornerRadius,color
```

| Paramètre      | Description                          |
| -------------- | ------------------------------------ |
| `rotation`     | Angle en degrés (centre de la forme) |
| `cornerRadius` | Rayon des coins arrondis             |

#### Règles

* `cornerRadius` est clampé à `min(width,height)/2`
* `cornerRadius = 0` ⇒ rectangle strict
* La rotation est appliquée **après translation**

---

### 7.3 Polygone régulier

```
MTF:POL,[x,y],radius,sides,rotation,color
```

---

## 8. Lignes & contours (BRD)

### 8.1 Ligne droite

```
BRD:LIN,[x1,y1],[x2,y2],thickness,color
```

---

### 8.2 Courbe de Bézier cubique

```
BRD:BEZ,[p1],[p2],[p3],[p4],thickness,color
```

---

## 9. Dégradés & shaders (GRD)

Les shaders affectent la **couche courante**.

### 9.1 Dégradé linéaire

```
GRD:LIN,angle,color1,color2
```

---

### 9.2 Dégradé radial

```
GRD:RAD,[center],radius,color1,color2
```

---

## 10. Grille de répétition (GRID)

Permet de répéter un groupe d’instructions.

```
GRID:cols,rows,[origin],[spacing]
    <instructions>
ENDGRID
```

Formule :

```
position_finale = origin + (i * spacing.x, j * spacing.y)
```

Les instructions internes utilisent des **coordonnées locales**.

---

## 11. Système de Gréffons (GREFON)

Les Gréffons permettent d’encapsuler des motifs graphiques réutilisables.

### 11.1 Définition

```
GREFON:id,type,[params]
    <instructions>
ENDGREFON
```

---

### 11.2 Utilisation

```
USE:id,[position],[params]
```

---

### 11.3 Règles

* Pas de récursion
* Pas de `HEAD`, `VER`, `RSL` internes
* Transformations locales uniquement

---

## 12. Ordre d’exécution

1. Lecture séquentielle
2. Substitution des variables
3. Déploiement des GRID
4. Déploiement des GREFON
5. Rendu couche par couche

---

## 13. Fin de fichier

```
END
```

---

## 14. Grammaire formelle (EBNF)

```ebnf
file        = header , version , resolution , { var } , { statement } , "END" ;

header      = "HEAD:AMI3" ;
version     = "VER:" , number ;
resolution  = "RSL:" , int , "," , int ;

var         = "VAR:" , ident , "=" , value ;

statement   = layer
            | shape
            | border
            | gradient
            | grid
            | grefon
            | use ;

layer       = "LYR:" , int ;

shape       = circle | square | polygon ;

circle      = "MTF:CIR," , vector , "," , number , "," , color ;
square      = "MTF:SQR," , vector , "," , number , "," , number ,
              "," , number , "," , number , "," , color ;
polygon     = "MTF:POL," , vector , "," , number , "," , int , "," , number , "," , color ;

border      = line | bezier ;

line        = "BRD:LIN," , vector , "," , vector , "," , number , "," , color ;
bezier      = "BRD:BEZ," , vector , "," , vector , "," , vector , "," , vector ,
              "," , number , "," , color ;

gradient    = linear_grad | radial_grad ;

linear_grad = "GRD:LIN," , number , "," , color , "," , color ;
radial_grad = "GRD:RAD," , vector , "," , number , "," , color , "," , color ;

grid        = "GRID:" , int , "," , int , "," , vector , "," , vector ,
              { statement },
              "ENDGRID" ;

grefon      = "GREFON:" , ident , "," , ident , "," , "[" , params , "]" ,
              { statement } ,
              "ENDGREFON" ;

use         = "USE:" , ident , "," , vector , "," , "[" , params , "]" ;

vector      = "[" , number , "," , number , "]" ;
color       = "#" , hex , hex , hex , hex , hex , hex , hex , hex ;
number      = int | float ;
value       = number | color ;
```

---

## 15. AMI3-B (format binaire)

AMI3-B est un flux compact composé de :

* Header `AMI3`
* Version
* Résolution
* Table de variables
* Flux d’opcodes

### Opcodes 

| Opcode | Instruction |  Indication                                            |
| ------ | ----------- | ------------------------------------------------------ |
| `0x00` | VER         |  version du fichier                             |
| `0x01` | LYR         |  couche                                         |
| `0x02` | VAR         |  variable                                       |
| `0x10` | MTF:CIR     |  cercle                                         |
| `0x11` | MTF:SQR     |  rectangle/carré avec rotation et cornerRadius  |
| `0x12` | MTF:POL     |  polygone régulier                              |
| `0x20` | BRD:LIN     |  ligne droite                                   |
| `0x21` | BRD:BEZ     |  courbe de Bézier cubique                       |
| `0x30` | GRD         |  gradient linéaire ou radial                    |
| `0x40` | GRID        |  début de grille                                |
| `0x41` | ENDGRID     |  fin de grille                                  |
| `0x50` | GREFON      |  définition de gréffon                          |
| `0x51` | ENDGREFON   |  fin de gréffon                                 |
| `0x52` | USE         |  appel de gréffon                               |
| `0xFF` | END         |  fin du fichier                                 |


---

**Conçu et imaginé par Salengros Liam — 2026**


*Rédaction technique assistée par intelligence artificielle*

