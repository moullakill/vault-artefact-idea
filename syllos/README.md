![Licence](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey) ![Licence](https://img.shields.io/badge/Categorie-WHITEPAPER-purple)
# Syllos : Architecture Conceptuelle de Vérification par Cascade 

**Syllos** est un cadre d'architecture logicielle conçu pour transformer l'intelligence artificielle générative d'un moteur de probabilité en un système de vérification formelle. Contrairement aux modèles de chat standards, Syllos utilise une structure descendante appelée **Topologie en Chandelier** pour garantir l'exactitude des faits et la sécurité des réponses.

> **Avertissement :** Syllos est un **concept théorique** et une méthode d'organisation de l'information. Ce dépôt ne contient pas de code exécutable, mais définit les spécifications pour une mise en application par des tiers.

---

## 1. Philosophie : "High Cost, High Quality"

L'architecture Syllos part du principe que pour les questions critiques (médicales, juridiques, stratégiques), le coût de calcul et la latence sont secondaires par rapport à la **vérité**.

* **Zéro Inférence Sauvage** : Le système ne doit rien "inventer" ; chaque segment de réponse doit être physiquement traçable vers une source.
* **Divergence Argumentée** : Si les sources sont contradictoires, Syllos renvoie une analyse comparative ("Yes and No") plutôt qu'une réponse arbitraire.

---

## 2. La Topologie en "Chandelier" (Workflow)

L'architecture décompose le processus de réflexion en couches successives afin de réduire l'entropie et d'éliminer les hallucinations.

### Étage 0 : Phase d'Exploration (Largeur X)

Le système déploie  agents spécialisés en parallèle. Chaque agent est confiné dans une "bulle de recherche" thématique unique pour éviter le biais de confirmation :

* **Segmentation** : Un agent interroge les bases de données médicales, un autre les sources gouvernementales, un troisième la presse scientifique, etc.
* **Production** : Chaque agent génère une réponse brute, son raisonnement (*Chain of Thought*) et l'indexation précise de ses sources.

### Étages Intermédiaires : Raffinement (Densité Y)

Chaque modèle de l'étage  reçoit les entrées de  modèles de l'étage supérieur (typiquement ).

* **Contrôle croisé** : Le modèle compare les faits reçus. S'il y a une contradiction, il ne tente pas de moyenne statistique mais signale le conflit à l'Arbitre Central.
* **Hétérogénéité** : Pour maximiser la détection d'erreurs, chaque étage utilise des modèles de familles et de tailles différentes.

---

## 3. L'Arbitre Central : Le Cœur Logique

L'Arbitre ne génère pas de texte ; il agit comme un superviseur de flux et un juge de validité. Il calcule trois scores critiques :

1. **Score de Ressemblance (Sr)** : Mesure la cohérence sémantique entre les modèles. Si  est trop bas, le système détecte une instabilité et peut relancer une recherche.
2. **Score de Vérité (Sv)** : Vérifie que chaque affirmation est strictement présente dans les sources citées.
3. **Validation de Descente** : L'Arbitre autorise le passage à l'étage inférieur uniquement si les seuils de sécurité sont atteints.

---

## 4. Sécurité Active et Anti-Jailbreak

Syllos est conçu pour résister aux manipulations de type "Prompt Injection" :

* **Le Flag Malware** : Si l'Arbitre détecte une tentative de détournement des instructions, il marque la requête d'un flag et la renvoie à un second Chandelier indépendant pour confirmation.
* **Circuit de Purge** : Si l'anomalie est confirmée sur deux clusters distincts, la requête est supprimée ("Drop").
* **Score de Réputation (CSC)** : Les IP présentant des patterns d'attaque répétés voient leur score de confiance chuter dans le *Cluster Server Cache*, entraînant un blacklistage ou une demande de "Preuve de Travail".
* **Isolation Matérielle** : En cas de dérive logicielle critique, l'architecture prévoit une coupure physique de l'alimentation des serveurs concernés via un Smart PDU pour purger la RAM.

---

## 5. Spécifications du Système

| Couche | Rôle principal | Caractéristique |
| --- | --- | --- |
| **Logiciel** | Orchestration Syllos | Architecture **Stateless** (uniquement en RAM). |
| **Mémoire (PSC)** | Personal Server Cache | Stockage temporaire ultra-rapide pour les calculs. |
| **Index (CSC)** | Cluster Server Cache | Base de données de confiance (Whitelist/Blacklist) mise à jour en temps réel. |
| **Interface** | Sortie Utilisateur | Rapport d'expertise sourcé ou Analyse de Divergence. |

---

## 6. Licence

Le concept **Syllos** est publié sous la licence **Creative Commons Attribution 4.0 International (CC BY 4.0)**.

**Vous êtes autorisé à :**

* **Partager** : copier et distribuer le concept.
* **Adapter** : remixer, transformer et créer à partir de ce concept pour toute utilisation, y compris commerciale.

**Selon les conditions suivantes :**

* **Attribution** : Vous devez créditer le concept original (Syllos) et indiquer si des modifications ont été effectuées.

---

## 7. Clause de Non-Responsabilité

Syllos est une proposition architecturale. L'efficacité du système dépend des modèles d'IA et des sources de données choisis lors de l'implémentation. Les auteurs déclinent toute responsabilité quant aux résultats produits par une application basée sur ce README.

---

**Conçu et imaginé par Salengros Liam — 2026**


*Rédaction technique assistée par intelligence artificielle*
