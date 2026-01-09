# Rotational Mesh Cipher (RMC) – Concept Théorique

**Auteur :** [Ton Nom / Pseudo]  
**Date :** 2026-01-09  

---

## Introduction

Le **Rotational Mesh Cipher (RMC)** est une proposition de cryptosystème à clé publique basée sur l’**algèbre non-commutative**. L’idée centrale est d’utiliser un groupe semi-direct \( \mathbb{Z}_P^3 \rtimes \text{GL}(3, \mathbb{Z}_P) \) ou \( \mathbb{Z}_P^3 \rtimes \text{SO}(3, \mathbb{Z}_P) \) pour construire une fonction à sens unique analogue au Diffie-Hellman, mais dans un espace de rotation/translation sur un corps fini.  

Cette approche exploite la **non-commutativité** pour augmenter la complexité du problème du logarithme discret, dans l’espoir d’offrir un mécanisme d’échange de clés résistant à certaines attaques abéliennes classiques.

---

## Concept Théorique

### Structure du Groupe

- **Groupe de travail** : \( G \cong \mathbb{Z}_P^3 \rtimes \text{SO}(3, \mathbb{Z}_P) \)
- **Éléments** : \( g = (\vec{t}, R) \)  
  - \( \vec{t} \in \mathbb{Z}_P^3 \) – vecteur de translation  
  - \( R \in \text{SO}(3, \mathbb{Z}_P) \) – matrice de rotation
- **Opération de groupe** :  
  \[
  (\vec{t}_1, R_1) \odot (\vec{t}_2, R_2) = (\vec{t}_1 + R_1 \cdot \vec{t}_2, R_1 \cdot R_2)
  \]

La non-commutativité (\( g_1 \odot g_2 \neq g_2 \odot g_1 \)) est le principe central pour la difficulté du logarithme discret dans ce groupe.

### Fonction à sens unique

Pour un générateur \( G = (\vec{t}, R) \) et un scalaire secret \( n \), l’**exponentiation** est définie par :

\[
G^n = (\vec{t}, R)^n = \left( \sum_{i=0}^{n-1} R^i \cdot \vec{t}, R^n \right)
\]

La somme vectorielle est une **série géométrique matricielle** non-commutative.

---

## Avantages Théoriques

- **Non-commutativité** : complique certaines attaques classiques sur DLP et CDHP.  
- **Combinaison translation + rotation** : augmente la dimension de l’espace clé et rend les collisions plus rares.  
- **Flexibilité de construction** : possible de construire des matrices \( R \) avec de grands ordres et des propriétés contrôlées pour la sécurité.

---

## Limites et Inconvénients

- **Complexité de génération de clés** : un tirage aléatoire simple rejette souvent toutes les matrices (valeurs propres indésirables, ordres trop petits). La clé doit être **construite mathématiquement**.  
- **Calcul coûteux** : exponentiation non-commutative et contrôle des propriétés sur de grands corps finis nécessitent des entiers illimités (Python `int` ou bibliothèques GMP).  
- **Sécurité non formellement prouvée** : pas encore de réduction de sécurité complète sur des problèmes connus.  
- **Usage purement théorique** : ce projet ne doit pas être utilisé en production ou pour protéger des données sensibles.

---

## Simulations et PoC

Des PoC Python ont été développés pour :

- simuler la génération aléatoire de matrices \( R \) et vecteurs \( t \) ;  
- analyser la distribution des déterminants \(\det(I-R)\) et des ordres des matrices ;  
- constater le **taux de rejet élevé**, ce qui souligne la nécessité de construction contrôlée des clés ;  
- produire des graphiques et CSV pour l’étude théorique.

> ⚠️ Tous les résultats sont expérimentaux et servent à **comprendre la structure et les limitations théoriques** du RMC.

---

## Perspectives

- Étudier des méthodes constructives pour générer des matrices \( R \) avec un grand ordre et sans valeurs propres 1.  
- Évaluer la sécurité théorique et les possibles réductions sur des problèmes connus (DLP non-commutatif, log-problème orbital).  
- Explorer l’extension à des dimensions plus grandes ou des groupes non-abéliens différents.

---

## Conclusion

Le **RMC** est une **idée théorique** intéressante dans le domaine des cryptosystèmes non-commutatifs.  
Il met en évidence la complexité d’exponentiation dans un espace rotation/translation et la nécessité d’une **construction contrôlée des clés**.  
Cependant, **aucune implémentation de production sécurisée n’existe**, et ce projet doit être considéré comme un terrain d’étude et d’expérimentation.

---

## Licence

Ce projet est fourni **à titre purement académique et expérimental**.  
Aucune garantie n’est donnée sur la sécurité, et il ne doit **pas être utilisé pour des données sensibles**.

