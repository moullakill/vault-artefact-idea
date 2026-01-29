![Licence](https://img.shields.io/badge/Categorie-DRAFT-yellow)

# DRAFT : Spécifications du Transistor Photonique à Commutation Spatiale (TPCS)

> **STATUT : DRAFT**
> 
> **Note :** Ce document est une ébauche de recherche théorique. Il n'est pas finalisé, sujet à modification radicale, et aucune garantie n'est donnée quant à sa complétion future ou sa faisabilité expérimentale.

---

## 1. Résumé du Concept

Le **TPCS** est un composant optique de commutation conçu pour moduler un signal lumineux de forte puissance (**Laser-Source**) à l'aide d'un signal de commande de faible puissance (**Laser-Commande**). Contrairement aux transistors électroniques classiques, le TPCS utilise une interaction lumière-matière sur une interface active pour canaliser un faisceau initialement divergent vers un détecteur précis.

## 2. Architecture du Système

Le montage repose sur l'alignement de cinq éléments clés :

1. **Laser de Commande (L-cmd) :** Signal de contrôle précis, faible intensité, haute cohérence.
2. **Laser Source (L-src) :** Signal de puissance brute, profil spatial volontairement "brouillon" ou divergent.
3. **Miroir Actif (SBR) :** Réflecteur de Bragg Saturable agissant comme l'interface de décision.
4. **Filtre de Seuil :** Barrière physique ou statistique bloquant les photons diffusés hors axe.
5. **Capteur de Sortie :** Détecteur final (type photodiode) validant l'état logique.

## 3. Principes Physiques (Théorie du Draft)

### A. Commutation par Saturation d'Absorption

Le miroir SBR est le cœur du dispositif. Son comportement change selon l'excitation reçue :

* **État OFF :** Le miroir est dans son état naturel absorbant. Il "étouffe" ou éparpille le **L-src**. L'énergie reçue par le capteur est quasi nulle.
* **État ON :** Le **L-cmd** frappe le miroir et sature ses capacités d'absorption. Cette zone devient instantanément réfléchissante. Le **L-src** est alors renvoyé proprement vers le filtre.

### B. Régime Impulsionnel et Thermique

Pour éviter la dégradation du matériau, le système est conçu pour opérer via des impulsions ultra-brèves :

* **Confinement temporel :** L'activation se fait sur une échelle de temps tellement courte (femtosecondes) que la chaleur n'a pas le temps de se propager aux couches profondes du miroir.
* **Refroidissement passif :** Le temps de repos entre deux impulsions permet une dissipation naturelle, évitant la fusion des nanostructures du miroir SBR.

## 4. piste d'amélioration

* [ ] Définir le ratio de puissance nécessaire entre **L-cmd** et **L-src** pour éviter le déclenchement accidentel.
* [ ] Tester différents matériaux pour le miroir (ex: Graphène ou Cristaux Photoniques) pour augmenter la vitesse d'extinction.
* [ ] Modéliser la perte de signal au passage du filtre de seuil.
* [ ] Étudier la possibilité de "cascader" ces transistors (la sortie d'un TPCS servant de commande au suivant).

---

*Conçu et imaginé par Salengros Liam — 2026*

*Rédaction technique assistée par intelligence artificielle*
