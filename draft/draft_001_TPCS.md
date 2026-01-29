![Licence](https://img.shields.io/badge/Categorie-DRAFT-yellow)
# DRAFT : Spécifications du Transistor Photonique à Commutation Spatiale (TPCS)

> **STATUT : DRAFT** > **Note :** Ce document est une ébauche de recherche théorique. Il n'est pas finalisé, sujet à modification radicale, et aucune garantie n'est donnée quant à sa complétion future ou sa faisabilité expérimentale.

---

## 1. Résumé du Concept

Le **TPCS** est un composant optique de commutation conçu pour moduler un signal lumineux de forte puissance (**Source**) à l'aide d'un signal de commande de faible puissance (**Gate**). Contrairement aux transistors électroniques, le TPCS utilise une interaction lumière-matière sur une interface active pour canaliser un faisceau divergent vers un détecteur.

## 2. Architecture du Système

Le montage repose sur l'alignement de cinq éléments clés :

1. **Laser de Commande () :** Signal de contrôle précis, faible intensité.
2. **Laser Source () :** Signal de puissance, profil spatial "brouillon" (divergent/incohérent).
3. **Miroir Actif (SBR) :** Réflecteur de Bragg Saturable agissant comme interface de décision.
4. **Filtre de Seuil () :** Barrière statistique bloquant les photons diffusés.
5. **Capteur () :** Détecteur de sortie (Photodiode ou photomultiplicateur).

## 3. Principes Physiques (Théorie du Draft)

### A. Commutation par Saturation d'Absorption

Le miroir SBR utilise des puits quantiques (ex: ).

* **État OFF :** Le miroir absorbe ou diffuse le Laser Source (). L'énergie est trop éparpillée pour franchir le filtre.
* **État ON :** Le Laser de Commande () sature les états d'énergie du miroir. Cette "tache" d'activation transforme localement le miroir en réflecteur parfait, canalisant le flux de  vers le filtre.

### B. Régime Impulsionnel et Thermique

Pour éviter la destruction du composant (seuil de dommage optique), le système est théoriquement opéré en régime ultra-rapide :

* **Temps d'activation :**  femtosecondes.
* **Gestion de la chaleur :** L'activation est si brève que l'énergie thermique n'a pas le temps de se propager dans le substrat (confinement temporel), permettant des pics de puissance élevés sans fusion du matériau.

## 4. Zones d'Ombre et Travaux Restants (TODO)

* [ ] Définir le ratio précis entre la puissance de  et  pour garantir la stabilité.
* [ ] Calculer la dérive thermique en cas d'utilisation à haute fréquence continue.
* [ ] Modéliser la probabilité de "faux positifs" (photons de  franchissant le filtre par simple diffusion aléatoire).
* [ ] Optimisation des longueurs d'onde (ex:  en UV et  en Infra-rouge pour faciliter le filtrage).

---
*Conçu et imaginé par Salengros Liam — 2026*

*Rédaction technique assistée par intelligence artificielle*
