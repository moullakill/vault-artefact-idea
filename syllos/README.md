# Syllos : Framework d'Architecture d'IA en "Chandelier"

**Syllos** est un concept d'architecture logicielle conçu pour transformer l'inférence des grands modèles de langage (LLM) d'un moteur de probabilité en un moteur de vérification formelle.

Basé sur une topologie descendante dite en "Chandelier", Syllos privilégie la **vérité documentaire** et la **stabilité logique** sur la rapidité, suivant une philosophie *High Cost, High Quality*.

> [!IMPORTANT]
> **Syllos est une idée conceptuelle.** Ce n'est ni un logiciel fini, ni un produit commercial. Ce dépôt propose une structure de pensée et un workflow que n'importe qui peut implémenter, modifier et adapter sous licence Apache 2.0.

---

## 1. Vision Philosophique

L'architecture Syllos repose sur trois piliers :

1. **La Fragmentation des Biais :** Aucun modèle ne traite la question seul.
2. **Le Score de Vérité () :** Une réponse n'est valide que si elle est physiquement présente dans les sources, sans aucune inférence autorisée.
3. **L'Antagonisme Argumenté :** En cas de divergence de sources fiables, le système refuse de trancher et produit une réponse "Yes and No" documentée.

---

## 2. Architecture du Workflow (Le Chandelier)

L'architecture se déploie en couches successives pilotées par un **Arbitre Central**.

### Étage 0 : Phase d'Exploration (Largeur X)

Le système déploie  agents spécialisés en parallèle.

* **Segmentation des sources :** Chaque agent est confiné à une "bulle" spécifique (ex: sources académiques uniquement, sources gouvernementales, etc.).
* **Production :** Chaque agent génère une réponse brute accompagnée de son raisonnement (*Chain of Thought*) et de l'indexation précise de ses sources.

### Étages Intermédiaires : Raffinement (Densité Y)

Chaque modèle de l'étage  reçoit les entrées de  modèles de l'étage supérieur.

* **Contrôle croisé :** Les modèles comparent les faits. En cas de contradiction, ils émettent un signal d'alerte vers l'Arbitre plutôt que de moyenner les statistiques.
* **Hétérogénéité :** Il est recommandé d'utiliser des modèles de familles et de tailles différentes à chaque étage pour éviter les biais de formation communs.

---

## 3. L'Arbitre Central (Cœur Logique)

L'Arbitre est le superviseur du flux. Il ne génère pas de contenu, il calcule la validité du processus via trois métriques :

1. **Score de Ressemblance () :** Mesure la cohérence sémantique entre les modèles. Une chute de  indique une instabilité du sujet ou une tentative de manipulation.
2. **Score de Vérité () :** Vérifie que chaque affirmation est strictement supportée par les sources citées (Inférence Zéro).
3. **Validation de Descente :** Autorise le passage à l'étage inférieur uniquement si les seuils de sécurité sont atteints.

---

## 4. Protocole de Sécurité et Anti-Jailbreak

Syllos est conçu pour être "Stateless" (sans état) par défaut, garantissant une isolation totale entre les requêtes.

* **Détection de Malware (Jailbreak) :** Si l'Arbitre détecte un comportement suspect ou une tentative de contournement des instructions (via le ), la requête est renvoyée au Dispatcher avec un flag de sécurité.
* **Redondance de Sécurité :** Une requête suspecte est traitée par un second Chandelier indépendant. Si l'anomalie persiste, la requête est définitivement abandonnée ("Poubelle").
* **Système de Réputation :** Le système permet de moduler les ressources allouées en fonction de la fiabilité de l'origine, sans pour autant bloquer les utilisateurs légitimes.

---

## 5. Spécifications Techniques Suggérées

| Composant | Rôle | Technologie recommandée (exemples) |
| --- | --- | --- |
| **Dispatcher** | Load Balancer & Sécurité | Nginx / Custom Go Dispatcher |
| **Orchestrateur** | Gestion du Chandelier | Python (LangGraph / Temporal.io) |
| **Agents** | Inférence de données | Modèles experts (Mistral, Llama, Claude) |
| **Arbitre** | Calcul de cohérence | Modèle SOTA (GPT-4o / Claude 3.5) |
| **Mémoire** | Stockage temporaire | Redis (In-memory uniquement) |

---

## 6. Mise en Application

Cette architecture est idéale pour les domaines où l'erreur n'est pas une option :

* Aide au diagnostic médical.
* Analyse de conformité juridique et réglementaire.
* Intelligence stratégique et cybersécurité.

### Pourquoi Apache 2.0 ?

Nous pensons que la sécurité de l'IA doit être transparente. En utilisant la licence Apache 2.0, Syllos permet :

* **La modification commerciale :** Les entreprises peuvent bâtir leurs propres versions de Syllos.
* **La protection des brevets :** Garantit une collaboration saine entre contributeurs.
* **La redistribution :** N'importe qui peut partager ses améliorations du modèle d'Arbitrage.

---

## Licence

Ce concept est partagé sous licence **Apache 2.0**. Voir le fichier `LICENSE` pour plus de détails.

