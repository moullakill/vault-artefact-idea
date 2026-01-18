![Licence](https://img.shields.io/badge/Categorie-RESEARCH%SUJET-purple)

# Rotational Mesh Cipher (RMC) – Concept Théorique

---

## 1. Introduction

Le **Rotational Mesh Cipher (RMC)** est un concept de cryptosystème à clé publique basé sur la non-commutativité des groupes semi-directs.  
L'idée centrale est de combiner des **rotations et translations dans un espace fini** pour créer une fonction à sens unique, exploitable pour l'échange de clés et le chiffrement hybride.

> ⚠️ Ce projet est **à l’état théorique**. Il ne doit pas être utilisé pour des applications réelles sans audits de sécurité approfondis et validation cryptographique rigoureuse.

---

## 2. Structure Algébrique

Le RMC utilise un **groupe non-abélien** défini comme un produit semi-direct :

$$
G \cong \mathbb{Z}_P^3 \rtimes \text{SO}(3, \mathbb{Z}_P)
$$

où :

- $P$ est un grand nombre premier cryptographiquement sûr,  
- $\vec{t} \in \mathbb{Z}_P^3$ représente un **vecteur de translation**,  
- $R \in \text{SO}(3, \mathbb{Z}_P)$ représente une **matrice de rotation**.

La loi de composition est donnée par :

$$
(\vec{t}_1, R_1) \odot (\vec{t}_2, R_2) = (\vec{t}_1 + R_1 \cdot \vec{t}_2, R_1 \cdot R_2)
$$

Cette loi est **non-commutative** :

$$
(\vec{t}_1, R_1) \odot (\vec{t}_2, R_2) \neq (\vec{t}_2, R_2) \odot (\vec{t}_1, R_1)
$$

---

## 3. Exponentiation et Fonction à Sens Unique

L'exponentiation dans le groupe $G$ est définie comme :

$$
G^n = (\vec{t}, R)^n = \left( \sum_{i=0}^{n-1} R^i \cdot \vec{t}, R^n \right)
$$

où la série de translations est équivalente à une **somme géométrique matricielle** :

$$
\sum_{i=0}^{n-1} R^i = (I - R^n)(I - R)^{-1}, \quad \text{si } I-R \text{ est inversible}
$$

Cette construction crée une fonction difficile à inverser, servant de base au **Problème du Logarithme Discret dans le groupe non-commutatif (DLP-RMC)**.

---

## 4. Échange de Clé Théorique

Le RMC peut être utilisé pour un échange de clé similaire à Diffie-Hellman :

1. Alice choisit une clé privée $s_A$, Bob une clé privée $s_B$.  
2. Les clés publiques sont calculées :

$$
P_A = G^{s_A}, \quad P_B = G^{s_B}
$$

3. Le secret partagé est :

$$
K = P_A^{s_B} = P_B^{s_A} = G^{s_A s_B}
$$

La non-commutativité du groupe introduit une **complexité supplémentaire**, qui pourrait renforcer la sécurité contre certaines attaques sur les groupes abéliens classiques.

---

## 5. Propriétés Théoriques

**Avantages théoriques :**

- Exploite la **non-commutativité**, rendant certaines attaques classiques moins efficaces.  
- Utilise un **espace vectoriel + rotation**, permettant de concevoir des fonctions à sens unique peu classiques.  
- Compatible avec une **architecture hybride**, utilisant le RMC pour la clé et AES pour le chiffrement rapide.

**Inconvénients / Limitations :**

- Le DLP-RMC est **non standard** et n’a pas encore été formellement réduit à des problèmes connus.  
- La génération aléatoire de matrices $R$ sûres est difficile, nécessitant **une construction mathématique contrôlée**.  
- Calculs matriciels modulo $P$ pour de grands nombres premiers peuvent être **très lourds**.  
- Pas de validation pratique ni de preuves de résistance aux attaques classiques (quantum ou algébriques).

---

## 6. Considérations de sécurité

- Les matrices $R$ doivent être choisies pour **éviter les valeurs propres 1** et avoir un ordre suffisamment grand.  
- Le vecteur $\vec{t}$ doit être choisi **hors des sous-espaces propres de $R$**.  
- Les paramètres doivent être audités pour garantir que le **DLP-RMC reste difficile à résoudre**.  
- Ce concept est purement **théorique** et ne doit pas être utilisé en production sans recherche et audit approfondis.

---

## 7. Conclusion

Le RMC est un **concept théorique** qui combine :

- non-commutativité,  
- structure translation + rotation,  
- exponentiation matricielle modulaire.

Il pourrait fournir **une alternative intéressante** aux DLP classiques et aux groupes elliptiques, mais nécessite :

- une **construction mathématique contrôlée** des clés,  
- des **preuves formelles de sécurité**,  
- des **analyses approfondies des sous-groupes et des collisions**.

Le RMC doit être considéré comme **une idée de recherche** et non un protocole finalisé.

---

## 8. Références

1. Diffie, W. & Hellman, M. (1976). *New Directions in Cryptography.*  
2. Menezes, A., van Oorschot, P., & Vanstone, S. (1996). *Handbook of Applied Cryptography.*  
3. Recherche interne sur **groupes non-commutatifs** et problèmes de logarithme discret.  
4. Concepts de **semi-direct product et matrices compagnon**.

---

## 9. Licence

Ce projet est fourni **à titre expérimental et éducatif**.  
Aucune garantie n’est donnée sur la sécurité ou la validité cryptographique.


*Conçu et imaginé par Salengros Liam — 2026*

*Rédaction technique assistée par intelligence artificielle*
