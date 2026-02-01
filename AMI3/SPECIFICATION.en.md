# **AMI3 — Technical Specification (Revision)**

**AMI3 Standard – Version 1.0**  
Formats **AMI3‑T (text)** & **AMI3‑B (binary)**

---

## 1. Overview

AMI3 is a **vector**, **deterministic**, and **compilable** graphic format designed to describe complex graphic scenes using a minimal set of geometric instructions.

The format follows the *“offload storage to computing”* philosophy: reducing file size by delegating more work to the execution engine.

Two representations coexist:

| Format     | Description                           |
| ---------- | ------------------------------------- |
| **AMI3‑T** | Human‑readable text language (source) |
| **AMI3‑B** | Binary stream optimized for execution |

AMI3‑T is compiled into AMI3‑B for production.

---

## 2. Execution Model

An AMI3 image is composed of stacked **layers**.

The engine maintains a global state including:

* Resolution  
* Standard version  
* Variable table  
* Active layer  
* Local origin  
* Repetition grid (GRID)  
* Active style and shader  

Instructions are evaluated **sequentially**, without ambiguity.

---

## 3. Fundamental Types

### 3.1 Numbers

| Type    | Description     |
| ------- | --------------- |
| `int`   | Signed integer  |
| `float` | Real number     |

No suffix is required.

---

### 3.2 Position

Represents a position:

```
[x,y]
```

Examples:

```
[500,250]
[-30,45]
```

---

### 3.3 Color

Hexadecimal RGBA format:

```
#RRGGBBAA
```

| Field | Description        |
| ----- | ------------------ |
| RR    | Red                |
| GG    | Green              |
| BB    | Blue               |
| AA    | Alpha (transparency) |

Examples:

```
#FF0000FF   // Opaque red
#00FF0080   // Semi‑transparent green
```

---

## 4. Mandatory Header

Every AMI3 file must begin with:

```
HEAD:AMI3
VER:x.y
RSL:width,height
```

### Example

```ami3
HEAD:AMI3
VER:1.0
RSL:1920,1080
```

### Rules

* `VER` defines the version of the standard used.  
* An engine must be backward‑compatible with older versions and ignore unknown instructions.  
* `HEAD`, `VER`, and `RSL` may appear **only once**.

---

## 5. Variables (VAR)

Variables allow reusable values to be factored.

### Declaration

```
VAR:name=value
```

### Usage

```
$name
```

### Example

```ami3
VAR:cx=500
VAR:mainColor=#FF00FFFF
VAR:title=Hello_World

MTF:CIR,[$cx,300],120,$mainColor
```

### Rules

* Substitution occurs **before execution**.  
* Variables are immutable.  
* Global file scope.  
* **Variables may contain any text string**, with no type restriction (number, color, identifier, free text…).  
* When a variable is used in a typed context (e.g., color, number), the engine must validate or reject the substituted value.

---

## 6. Layer System

```
LYR:n
```

* `n` is an integer ≥ 0.  
* Layers are rendered **in ascending order**.  
* Changing layer does not clear the previous one.

---

## 7. Shapes (MTF — Meta Transformable Forms)

### 7.1 Circle

```
MTF:CIR,[x,y],radius,color
```

---

### 7.2 Rectangle / Square (standard 1.1)

```
MTF:SQR,[x,y],width,height,rotation,cornerRadius,color
```

| Parameter      | Description                           |
| -------------- | ------------------------------------- |
| `rotation`     | Angle in degrees (around the center)  |
| `cornerRadius` | Rounded corner radius                 |

#### Rules

* `cornerRadius` is clamped to `min(width,height)/2`.  
* `cornerRadius = 0` ⇒ strict rectangle.

---

### 7.3 Regular Polygon

```
MTF:POL,[x,y],radius,sides,rotation,color
```

---

## 8. Pixel (PX)

```
PX:[x,y],color
```

Draws a single pixel.

---

## 9. Lines & Borders (BRD)

### 9.1 Straight Line

```
BRD:LIN,[x1,y1],[x2,y2],thickness,color
```

### 9.2 Cubic Bézier Curve

```
BRD:BEZ,[p1],[p2],[p3],[p4],thickness,color
```

---

## 10. Gradients (GRD)

Gradients affect the **current layer**.

### 10.1 Linear Gradient

```
GRD:LIN,angle,color1,color2
```

### 10.2 Radial Gradient

```
GRD:RAD,[center],radius,color1,color2
```

---

## 11. Repetition Grid (GRID)

```
GRID:cols,rows,[origin],[spacing]
    <instructions>
ENDGRID
```

Final position:

```
origin + (i * spacing.x, j * spacing.y)
```

Internal instructions use **local coordinates**.

---

## 12. Plugin System (GREFON)

### 12.1 Definition

```
GREFON:id,type,[params]
    <instructions>
ENDGREFON
```

### 12.2 Usage

```
USE:id,[position],[params]
```

### 12.3 Rules

* No recursion.  
* No internal `HEAD`, `VER`, `RSL`.  
* Local transformations only.

---

## 13. Execution Order

1. Sequential reading  
2. Variable substitution  
3. GRID expansion  
4. GREFON expansion  
5. Layer‑by‑layer rendering  

---

## 14. End of File

```
END
```

---

## 15. Formal Grammar (EBNF)

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

## 16. AMI3‑B (binary format)

AMI3‑B is a compact stream composed of:

* Header `AMI3`  
* Version  
* Resolution  
* Variable table  
* Opcode stream  

### Opcodes

| Opcode | Instruction | Meaning                                      |
| ------ | ----------- | -------------------------------------------- |
| `0x00` | VER         | File version                                 |
| `0x01` | LYR         | Layer change                                 |
| `0x02` | VAR         | Variable declaration                         |
| `0x10` | MTF:CIR     | Circle                                       |
| `0x11` | MTF:SQR     | Rectangle/square                             |
| `0x12` | MTF:POL     | Regular polygon                              |
| `0x13` | PX          | Single pixel                                 |
| `0x20` | BRD:LIN     | Straight line                                |
| `0x21` | BRD:BEZ     | Cubic Bézier curve                           |
| `0x30` | GRD         | Linear or radial gradient                    |
| `0x40` | GRID        | Grid start                                   |
| `0x41` | ENDGRID     | Grid end                                     |
| `0x50` | GREFON      | Plugin definition                            |
| `0x51` | ENDGREFON   | Plugin end                                   |
| `0x52` | USE         | Plugin call                                  |
| `0xFF` | END         | End of file                                  |

---

**Designed and imagined by Salengros Liam — 2026**  

*Technical writing assisted by artificial intelligence*
