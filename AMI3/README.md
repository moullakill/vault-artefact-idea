![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg) ![Licence](https://img.shields.io/badge/Categorie-EASYSEARCH-green)
# AMI3 : Augmented Mathematical Image 

**AMI3** est une norme de description d'image hybride conçue pour optimiser drastiquement le stockage et le transfert de données graphiques. Contrairement aux formats matriciels (pixels) ou vectoriels classiques (XML/SVG), l'AMI3 repose sur le paradigme **"Offload Storage to Computing"** : réduire le poids du fichier au strict minimum en déléguant la reconstruction visuelle à la puissance de calcul locale via des primitives mathématiques et un jeu d'instructions binaire.

---

## Concept et Philosophie

L'AMI3 a été conçu pour répondre aux problématiques de saturation de bande passante dans les environnements contraints (IoT, réseaux bas débit, stockage massif).

* **Sémantique Mathématique :** Une image n'est pas une grille de points, mais une suite de fonctions.
* **Hybridation Textuel/Binaire :**
* `.ami3t` (Textual) : Format lisible par l'homme pour le développement et le débogage.
* `.ami3b` (Binary) : Format compilé ultra-compact pour la production.


* **Légèreté extrême :** En moyenne 7 fois plus léger qu'un fichier SVG équivalent grâce à l'élimination de la verbosité du XML (sur dessin vectoriel).

---

## Structure de la norme AMI3

AMI3 repose sur une architecture modulaire inspirée des moteurs graphiques modernes.
Chaque image est décrite comme une pile de **couches (Layers)** contenant des **formes (MTF)**, des **lignes et contours (BRD)**, des **dégradés et shaders (GRD)** et des **réplicateurs spatiaux (GRID)**.
Les **Greffons (Plugins)** peuvent étendre le langage pour ajouter de nouveaux types de primitives, filtres ou générateurs procéduraux, sans casser la compatibilité.
Cette séparation claire entre **géométrie, style, répétition et effets** permet à AMI3 d’encoder des scènes complexes de façon extrêmement compacte et déterministe.

---

## État du Projet

L'AMI3 est actuellement au stade de **Concept**.

* [x] Grammaire EBNF définie.
* [x] Table des Opcodes.
* [ ] Développement du moteur de rendu (Renderer) 
* [ ] Compilateur AMI3-T to AMI3-B. 

---

## Licence

Ce projet est distribué sous licence **Apache License 2.0**. Voir les fichiers `LICENSE` et `NOTICE` pour plus de détails.

---

*Conçu et imaginé par Salengros Liam — 2026*


*Rédaction technique assistée par intelligence artificielle*

