# **AMI3 — Spécification Technique**

**Version 1.0**
Norme **AMI3-T (texte)** & **AMI3-B (binaire)**

---

## 1. Vue d’ensemble

AMI3 est un format graphique **vectoriel**, **déterministe** et **compilable**, conçu pour décrire des scènes complexes à l’aide d’instructions géométriques minimales.

Le format repose sur deux représentations :

| Format     | Description                      |
| ---------- | -------------------------------- |
| **AMI3-T** | Langage texte lisible (source)   |
| **AMI3-B** | Flux binaire compact (exécution) |

AMI3-T est compilé en AMI3-B, qui est ensuite interprété par un moteur de rendu.

---

## 2. Modèle d’exécution

Une image AMI3 est constituée de **couches (layers)** empilées.

Le moteur maintient un état global :

* Résolution
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

## 4. En-tête

Tout fichier AMI3 doit commencer par :

```
HEAD:AMI3
RSL:width,height
```

Exemple :

```
HEAD:AMI3
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

### 6.2 Rectangle / Carré

```
MTF:SQR,[x,y],width,height,rotation,color
```

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

Définition :

```
GREFON:id,type,[params]
    <instructions AMI3>
ENDGREFON
```

Exemple :

```ami3
GREFON:tree,MTF,[size,color]
    MTF:SQR,[0,50],20,80,0,#8B4513FF
    MTF:POL,[0,-30],50,3,0,#228B22FF
ENDGREFON
```

---

### 10.2 Utilisation d’un gréffon

```
USE:id,[position],[params]
```

Exemple :

```ami3
USE:tree,[300,700],[50,#228B22FF]
```

---

### 10.3 Gréffon externe

```
GREFON_LINK:id,"path/file.ami3t",HASH=0xXXXXXXXX
```

* Le hash est optionnel
* Permet le chargement de bibliothèques

---

### 10.4 Règles

* Pas de redéfinition de `HEAD` ou `RSL`
* Pas de récursion
* Les transformations sont locales

---

## 11. Ordre d’exécution

1. Lecture séquentielle
2. Déploiement des GRID
3. Déploiement des GREFON
4. Rendu couche par couche

---

## 12. Fin de fichier

```
END
```

---

## 13. Grammaire formelle (EBNF)

```ebnf
file        = header , resolution , { statement } , "END" ;

header      = "HEAD:AMI3" ;
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
square      = "MTF:SQR," , vector , "," , number , "," , number , "," , number , "," , color ;
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

### 15.1 Minimal

```ami3
HEAD:AMI3
RSL:800,600
LYR:0
MTF:CIR,[400,300],100,#FF0000FF
END
```

---

### 15.2 Motif répété

```ami3
HEAD:AMI3
RSL:800,800
LYR:0
GRID:5,5,[100,100],[120,120]
MTF:CIR,[0,0],30,#00FFFFFF
ENDGRID
END
```

---

### 15.3 Mandala

```ami3
HEAD:AMI3
RSL:1000,1000
LYR:0
GRD:RAD,[500,500],600,#050020FF,#200060FF
LYR:1
GRID:12,1,[500,500],[0,0]
MTF:POL,[0,0],300,6,0,#FF00FFFF
ENDGRID
END
```

---

**Conçu et imaginé par Salengros Liam — 2026**


*Rédaction technique assistée par intelligence artificielle*
