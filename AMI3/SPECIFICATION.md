# **AMI3 — Spécification Technique**

**Version de la norme : 1.0**
Norme **AMI3-T (texte)** & **AMI3-B (binaire)**

---

## 1. Vue d’ensemble

AMI3 est un format graphique **vectoriel**, **déterministe** et **compilable**, conçu pour décrire des scènes complexes à l’aide d’instructions géométriques minimales.

Le format repose sur deux représentations :

| Format     | Description                      |
| ---------- | -------------------------------- |
| **AMI3-T** | Langage texte lisible (source)   |
| **AMI3-B** | Flux binaire compact (exécution) |

AMI3-T est compilable en AMI3-B.

---

## 2. Modèle d’exécution

Une image AMI3 est constituée de **couches (layers)** empilées.

Le moteur maintient un état global :

* Résolution
* Version de la norme
* Couche active
* Origine locale
* Grille de répétition (GRID)
* Style et shader actifs

Les instructions sont évaluées **séquentiellement**.

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

## 4. En-tête (obligatoire)

Tout fichier AMI3 **doit commencer par trois lignes** :

```
HEAD:AMI3
VER:x.y
RSL:width,height
```

### Règles

* `VER` définit la version de la norme utilisée
* Le moteur **doit refuser** une version non supportée
* La version ne peut pas être redéfinie ailleurs

### Exemple

```
HEAD:AMI3
VER:1.0
RSL:1920,1080
```

---

## 5. Système de couches

```
LYR:n
```

* `n` est un entier ≥ 0
* Les couches sont rendues **dans l’ordre croissant**

---

## 6. Formes (MTF — Meta Transformable Forms)

### 6.1 Cercle

```
MTF:CIR,[x,y],radius,color
```

---

### 6.2 Rectangle / Carré (avec rotation et coins arrondis)

```
MTF:SQR,[x,y],width,height,rotation,cornerRadius,color
```

#### Règles

* `rotation` est exprimée en **degrés**
* La rotation est appliquée autour du **centre**
* `cornerRadius` est clampé à `min(width,height)/2`
* `cornerRadius = 0` → rectangle strict

---

### 6.3 Polygone régulier

```
MTF:POL,[x,y],radius,sides,rotation,color
```

---

## 7. Lignes & contours (BRD)

### 7.1 Ligne droite

```
BRD:LIN,[x1,y1],[x2,y2],thickness,color
```

---

### 7.2 Courbe de Bézier cubique

```
BRD:BEZ,[p1],[p2],[p3],[p4],thickness,color
```

---

## 8. Dégradés & shaders (GRD)

Les shaders affectent la **couche courante**.

### 8.1 Dégradé linéaire

```
GRD:LIN,angle,color1,color2
```

---

### 8.2 Dégradé radial

```
GRD:RAD,[center],radius,color1,color2
```

---

## 9. Grille de répétition (GRID)

Permet de répéter un groupe d’instructions.

```
GRID:cols,rows,[origin],[spacing]
    <instructions>
ENDGRID
```

Formule de position :

```
position_finale = origin + (i * spacing.x, j * spacing.y)
```

Les instructions internes utilisent des **coordonnées locales**.

---

## 10. Système de Gréffons (GREFON)

Les Gréffons permettent d’encapsuler des motifs graphiques réutilisables.

---

### 10.1 Gréffon inline

```
GREFON:id,type,[params]
    <instructions AMI3>
ENDGREFON
```

---

### 10.2 Utilisation d’un gréffon

```
USE:id,[position],[params]
```

---

### 10.3 Gréffon externe

```
GREFON_LINK:id,"path/file.ami3t",HASH=0xXXXXXXXX
```

---

### 10.4 Règles

* Pas de redéfinition de `HEAD`, `VER` ou `RSL`
* Pas de récursion
* Transformations locales uniquement

---

## 11. Ordre d’exécution

1. Lecture séquentielle
2. Validation de version
3. Déploiement des GRID
4. Déploiement des GREFON
5. Rendu couche par couche

---

## 12. Fin de fichier

```
END
```

---

## 13. Grammaire formelle (EBNF)

```ebnf
file        = header , version , resolution , { statement } , "END" ;

header      = "HEAD:AMI3" ;
version     = "VER:" , number ;
resolution  = "RSL:" , int , "," , int ;

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
square      = "MTF:SQR," , vector , "," , number , "," , number , "," , number , "," , number , "," , color ;
polygon     = "MTF:POL," , vector , "," , number , "," , int , "," , number , "," , color ;

border      = line | bezier ;

line        = "BRD:LIN," , vector , "," , vector , "," , number , "," , color ;
bezier      = "BRD:BEZ," , vector , "," , vector , "," , vector , "," , vector , "," , number , "," , color ;

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
```

---

## 14. AMI3-B (format binaire)

AMI3-B est un flux compact composé de :

* Header `AMI3`
* Version (float32)
* Résolution
* Opcodes + données

### 14.1 Opcodes principaux

| Opcode | Instruction |
| ------ | ----------- |
| `0x01` | LYR         |
| `0x10` | MTF:CIR     |
| `0x11` | MTF:SQR     |
| `0x12` | MTF:POL     |
| `0x20` | BRD:LIN     |
| `0x21` | BRD:BEZ     |
| `0x30` | GRD         |
| `0x40` | GRID        |
| `0x50` | GREFON      |
| `0x51` | USE         |
| `0xFF` | END         |

---

## 15. Exemples AMI3-T

### Rectangle arrondi rotatif

```ami3
HEAD:AMI3
VER:1.1
RSL:800,600
LYR:0
MTF:SQR,[400,300],300,180,25,32,#FF0000FF
END
```

---

**Conçu et imaginé par Salengros Liam — 2026**

*Rédaction technique assistée par intelligence artificielle*
