# **AMI3 – Spécification Technique**

*Version 1.0 – Norme AMI3-T & AMI3-B*

---

## 1. Vue d’ensemble

AMI3 est un format de description graphique **déterministe**, **vectoriel** et **compilable**.
Il est conçu pour encoder des scènes graphiques complexes sous forme de **commandes géométriques minimales**.

Deux formats existent :

| Format     | Rôle                                      |
| ---------- | ----------------------------------------- |
| **AMI3-T** | Format texte lisible (source)             |
| **AMI3-B** | Format binaire compilé (exécution rapide) |

AMI3-T est compilé en AMI3-B, qui est ensuite interprété par un moteur de rendu.

---

## 2. Modèle d’exécution

Une image AMI3 est une **pile de couches (Layers)**.
Chaque couche contient une suite de **commandes de dessin**.

Le moteur maintient un état global :

* Résolution
* Couche active
* Origine de transformation
* Grille de répétition (GRID)
* Styles et shaders actifs

Chaque instruction modifie ou consomme cet état.

---

## 3. Types fondamentaux

### 3.1 Nombres

| Type    | Description  |
| ------- | ------------ |
| `int`   | Entier signé |
| `float` | Nombre réel  |

AMI3-T accepte les deux.

---

### 3.2 Vecteur

Représente une position ou un déplacement.

```
[x,y]
```

Exemple :

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

| Composant | Valeur               |
| --------- | -------------------- |
| RR        | Rouge                |
| GG        | Vert                 |
| BB        | Bleu                 |
| AA        | Alpha (transparence) |

Exemple :

```
#FF0000FF   (rouge opaque)
#00FF0080   (vert 50%)
```

---

## 4. En-tête

Chaque fichier doit commencer par :

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

Change la couche active.

Les couches sont rendues dans l’ordre croissant.

---

## 6. Formes (MTF – Meta Transformable Forms)

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

## 7. Lignes et contours (BRD)

### 7.1 Ligne

```
BRD:LIN,[x1,y1],[x2,y2],thickness,color
```

---

### 7.2 Bézier

```
BRD:BEZ,[p1],[p2],[p3],[p4],thickness,color
```

---

## 8. Dégradés & Shaders (GRD)

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

Permet de dupliquer des formes ou groupes.

```
GRID:cols,rows,[origin],[spacing]
...
ENDGRID
```

Toutes les commandes entre GRID et ENDGRID sont répétées.

Position finale :

```
final_position = origin + (i * spacing.x, j * spacing.y)
```

---

## 10. Ordre de rendu

1. Le moteur lit ligne par ligne
2. Chaque instruction est ajoutée à la couche courante
3. Les GRID sont déroulés en instructions internes
4. Les couches sont fusionnées du bas vers le haut

---

## 11. Fin de fichier

```
END
```

---

## 12. Grammaire formelle (EBNF)

```
file        = header , resolution , { statement } , "END" ;

header      = "HEAD:AMI3" ;
resolution  = "RSL:" , int , "," , int ;

statement   = layer
            | shape
            | border
            | gradient
            | grid ;

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

vector      = "[" , number , "," , number , "]" ;
color       = "#" , hex , hex , hex , hex , hex , hex , hex , hex ;
number      = int | float ;
```

---

## 13. AMI3-B (binaire)

AMI3-B est une version compacte contenant :

* Magic header `AMI3`
* Résolution
* Flux d’opcodes
* Données flottantes et couleurs compressées

Chaque opcode correspond à une instruction AMI3-T.

Exemple :

| Opcode | Fonction |
| ------ | -------- |
| `0x01` | LYR      |
| `0x10` | CIR      |
| `0x11` | SQR      |
| `0x12` | POL      |
| `0x20` | LIN      |
| `0x21` | BEZ      |
| `0x30` | GRD      |
| `0x40` | GRID     |
| `0xFF` | END      |

---

## 15. Exemples de code AMI3-T

### 15.1 Exemple minimal

```ami3
HEAD:AMI3
RSL:800,600

LYR:0
MTF:CIR,[400,300],100,#FF0000FF

END
```

Affiche un cercle rouge centré sur l’image.

---

### 15.2 Scène multicouches

```ami3
HEAD:AMI3
RSL:1000,1000

// Fond
LYR:0
GRD:LIN,90,#001122FF,#334455FF

// Soleil
LYR:1
MTF:CIR,[800,200],120,#FFD700FF

// Sol
LYR:2
MTF:SQR,[500,850],1000,300,0,#228B22FF

END
```

Crée un ciel en dégradé, un soleil et une bande de sol verte.

---

### 15.3 Motif répété avec GRID

```ami3
HEAD:AMI3
RSL:800,800

LYR:0
GRID:5,5,[100,100],[120,120]
MTF:CIR,[0,0],30,#00FFFFFF
ENDGRID

END
```

Dessine une grille 5×5 de cercles cyan espacés régulièrement.

---

### 15.4 Rosace géométrique

```ami3
HEAD:AMI3
RSL:1000,1000

LYR:0
GRD:RAD,[500,500],600,#050020FF,#200060FF

LYR:1
GRID:12,1,[500,500],[0,0]
MTF:POL,[0,0],300,6,0,#FF00FFFF
MTF:POL,[0,0],220,6,15,#00FFFFFF
ENDGRID

LYR:2
MTF:CIR,[500,500],80,#FFFFFFFF

END
```

Crée un mandala hexagonal tournant sur un fond radial.

---

### 15.5 Combinaison formes + lignes

```ami3
HEAD:AMI3
RSL:1200,800

LYR:0
MTF:SQR,[600,400],1200,800,0,#000000FF

LYR:1
MTF:CIR,[600,400],200,#FF0000FF
BRD:LIN,[400,400],[800,400],4,#FFFFFFFF
BRD:LIN,[600,200],[600,600],4,#FFFFFFFF

END
```

Produit un cercle central traversé par deux axes lumineux.

--- 

*Conçu et imaginé par Salengros Liam — 2026*

*Rédaction technique assistée par intelligence artificielle*
