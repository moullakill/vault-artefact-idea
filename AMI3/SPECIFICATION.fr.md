# **AMI3 — Spécification Technique (Révision)**

**Norme AMI3 – Version 1.0**  
Formats **AMI3‑T (texte)** & **AMI3‑B (binaire)**

---

## 1. Vue d’ensemble

AMI3 est un format graphique **vectoriel**, **déterministe** et **compilable**, conçu pour décrire des scènes graphiques complexes à l’aide d’un ensemble minimal d’instructions géométriques.

Le format suit la philosophie *« offload storage to computing »* : réduire la taille des fichiers en déléguant davantage de travail au moteur d’exécution.

Deux représentations coexistent :

| Format     | Description                               |
| ---------- | ----------------------------------------- |
| **AMI3‑T** | Langage texte lisible (source)            |
| **AMI3‑B** | Flux binaire optimisé pour l’exécution    |

AMI3‑T est compilé en AMI3‑B pour la production.

---

## 2. Modèle d’exécution

Une image AMI3 est constituée de **couches (layers)** empilées.

Le moteur maintient un état global comprenant :

* Résolution
* Version de la norme
* Table des variables
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

Aucun suffixe n’est requis.

---

### 3.2 Position

Représente une position :

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
#00FF0080   // Vert semi‑transparent
```

---

## 4. En‑tête obligatoire

Tout fichier AMI3 doit commencer par :

```
HEAD:AMI3
VER:x.y
RSL:width,height
```

### Exemple

```ami3
HEAD:AMI3
VER:1.0
RSL:1920,1080
```

### Règles

* `VER` définit la version de la norme utilisée.
* Un moteur doit être rétrocompatible avec les anciennes versions et ignorer les instructions inconnues.
* `HEAD`, `VER` et `RSL` ne peuvent apparaître **qu’une seule fois**.

---

## 5. Variables (VAR)

Les variables permettent de factoriser des valeurs réutilisables.

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
VAR:title=Hello_World

MTF:CIR,[$cx,300],120,$mainColor
```

### Règles

* Substitution **avant exécution**.
* Variables immuables.
* Portée globale au fichier.
* **Les variables peuvent contenir n’importe quelle chaîne de texte**, sans restriction de type (nombre, couleur, identifiant, texte libre…).
* Lorsqu’une variable est utilisée dans un contexte typé (ex. couleur, nombre), le moteur doit valider ou rejeter la valeur substituée.

---

## 6. Système de couches

```
LYR:n
```

* `n` est un entier ≥ 0.
* Les couches sont rendues **dans l’ordre croissant**.
* Changer de couche ne vide pas la précédente.

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
| `rotation`     | Angle en degrés (autour du centre)   |
| `cornerRadius` | Rayon des coins arrondis             |

#### Règles

* `cornerRadius` est clampé à `min(width,height)/2`.
* `cornerRadius = 0` ⇒ rectangle strict.
---

### 7.3 Polygone régulier

```
MTF:POL,[x,y],radius,sides,rotation,color
```

---

## 8. Pixel (PX)

```
PX:[x,y],color
```

Déssine un pixel unique.

---

## 9. Lignes & contours (BRD)

### 9.1 Ligne droite

```
BRD:LIN,[x1,y1],[x2,y2],thickness,color
```

### 9.2 Courbe de Bézier cubique

```
BRD:BEZ,[p1],[p2],[p3],[p4],thickness,color
```

---

## 10. Dégradés  (GRD)

Les dégradés affectent la **couche courante**.

### 10.1 Dégradé linéaire

```
GRD:LIN,angle,color1,color2
```

### 10.2 Dégradé radial

```
GRD:RAD,[center],radius,color1,color2
```

---

## 11. Grille de répétition (GRID)

```
GRID:cols,rows,[origin],[spacing]
    <instructions>
ENDGRID
```

Position finale :

```
origin + (i * spacing.x, j * spacing.y)
```

Les instructions internes utilisent des **coordonnées locales**.

---

## 12. Système de Gréffons (GREFON)

### 12.1 Définition

```
GREFON:id,type,[params]
    <instructions>
ENDGREFON
```

### 12.2 Utilisation

```
USE:id,[position],[params]
```

### 12.3 Règles

* Pas de récursion.
* Pas de `HEAD`, `VER`, `RSL` internes.
* Transformations locales uniquement.

---

## 13. Ordre d’exécution

1. Lecture séquentielle  
2. Substitution des variables  
3. Déploiement des GRID  
4. Déploiement des GREFON  
5. Rendu couche par couche  

---

## 14. Fin de fichier

```
END
```

---

## 15. Grammaire formelle (EBNF)

```ebnf
file        = header , version , resolution , { var } , { statement } , "END" ;

header      = "HEAD:AMI3" ;
version     = "VER:" , number ;
resolution  = "RSL:" , int , "," , int ;

var         = "VAR:" , ident , "=" , text ;

statement   = layer
            | shape
            | border
            | gradient
            | grid
            | grefon
            | use ;

layer       = "LYR:" , int ;

shape       = circle | square | polygon | pixel ;

circle      = "MTF:CIR," , vector , "," , number , "," , color ;
square      = "MTF:SQR," , vector , "," , number , "," , number ,
              "," , number , "," , number , "," , color ;
polygon     = "MTF:POL," , vector , "," , number , "," , int , "," , number , "," , color ;
pixel       = "PX:" , vector , "," , color ;

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
text        = { any_character_except_newline } ;
```

---

## 16. AMI3‑B (format binaire)

AMI3‑B est un flux compact composé de :

* Header `AMI3`
* Version
* Résolution
* Table de variables
* Flux d’opcodes

### Opcodes

| Opcode | Instruction | Indication                                      |
| ------ | ----------- | ------------------------------------------------ |
| `0x00` | VER         | Version du fichier                               |
| `0x01` | LYR         | Changement de couche                             |
| `0x02` | VAR         | Déclaration de variable                          |
| `0x10` | MTF:CIR     | Cercle                                           |
| `0x11` | MTF:SQR     | Rectangle/carré                                  |
| `0x12` | MTF:POL     | Polygone régulier                                |
| `0x13` | PX          | Pixel isolé                                      |
| `0x20` | BRD:LIN     | Ligne droite                                     |
| `0x21` | BRD:BEZ     | Courbe de Bézier cubique                         |
| `0x30` | GRD         | Gradient linéaire ou radial                      |
| `0x40` | GRID        | Début de grille                                  |
| `0x41` | ENDGRID     | Fin de grille                                    |
| `0x50` | GREFON      | Définition de gréffon                            |
| `0x51` | ENDGREFON   | Fin de gréffon                                   |
| `0x52` | USE         | Appel de gréffon                                 |
| `0xFF` | END         | Fin du fichier                                   |

---

**Conçu et imaginé par Salengros Liam — 2026**  

*Rédaction technique assistée par intelligence artificielle*
