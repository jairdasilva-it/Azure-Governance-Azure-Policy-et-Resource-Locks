# Azure Governance – Azure Policy et Resource Locks

## 🎯 Objectif

Déployer et tester des mécanismes de gouvernance et de protection des ressources dans Microsoft Azure à l'aide de **Azure Policy** et **Azure Resource Locks**.

L'objectif de ce laboratoire est de simuler l'application de règles de gouvernance dans une entreprise :

- création d'un groupe de ressources dédié ;
- attribution d'une stratégie Azure Policy ;
- restriction des déploiements à la région **West Europe** ;
- vérification du refus d'une région non autorisée ;
- vérification de l'acceptation d'une région autorisée ;
- consultation de la conformité de la stratégie ;
- création d'un verrou de suppression ;
- test pratique du blocage de suppression du groupe de ressources.

Ce projet a été réalisé dans le cadre de ma préparation à la certification **Microsoft Azure AZ-104** afin de développer mes compétences en gouvernance, conformité et protection des ressources Azure.

---

## 💻 Technologies utilisées

- Microsoft Azure
- Azure Policy
- Azure Resource Locks
- Azure Resource Manager (ARM)
- Azure Resource Groups
- Azure Storage Accounts (tests de déploiement)
- Azure Policy Assignments
- Azure Policy Compliance
- Azure Portal

---

## 🏗️ Architecture

```text
                 Microsoft Azure
                        |
                        v
             Azure subscription 1
                        |
                        v
           rg-governance-policy-lab
                Resource Group
                        |
           +------------+------------+
           |                         |
           v                         v
       Azure Policy             Resource Locks
           |                         |
           v                         v
   Emplacements autorisés     lock-prevent-delete
           |                         |
           v                         v
    West Europe uniquement     Type : CanNotDelete
           |                         |
     +-----+-----+                   v
     |           |             Suppression du RG
     v           v                   |
France Central  West Europe          v
     |           |             Opération refusée
     v           v
   Refusé      Autorisé
```

La stratégie **Emplacements autorisés** est attribuée au groupe de ressources `rg-governance-policy-lab`.

Elle permet de limiter les régions dans lesquelles les ressources peuvent être déployées.

Le verrou **lock-prevent-delete** protège le même groupe de ressources contre les suppressions accidentelles.

Ces deux mécanismes répondent à des objectifs complémentaires :

- **Azure Policy** : appliquer des règles de gouvernance ;
- **Resource Locks** : protéger les ressources contre certaines opérations administratives.

---

## 🎯 Résultat

Le laboratoire a permis de valider le fonctionnement d'Azure Policy et des verrous de ressources Azure.

Les tests réalisés ont confirmé que :

- la stratégie **Emplacements autorisés** peut être attribuée à un groupe de ressources ;
- la région **France Central** est refusée par Azure Policy ;
- la région **West Europe** est acceptée dans le formulaire de création ;
- le portail affiche un état de conformité de **100 %**, avec aucune ressource évaluée au moment de la capture ;
- un verrou de type **CanNotDelete** peut être appliqué à un groupe de ressources ;
- une tentative de suppression du groupe protégé est refusée ;
- Azure indique explicitement que le groupe de ressources est verrouillé.

Ce laboratoire démontre la différence entre **contrôle des déploiements** et **protection contre la suppression des ressources**.

---

## 👤 Auteur

**Jair Da Silva**

Technicien Systèmes & Réseaux | Support IT N1/N2 | Microsoft Azure

GitHub : https://github.com/jairdasilva-it

LinkedIn : https://www.linkedin.com/in/jair-da-silva-6b14aa278

---

# 🚀 Étapes du projet

## 1️⃣ Création du groupe de ressources

Création d'un groupe de ressources Azure destiné à accueillir les tests de gouvernance.

Groupe créé :

```text
rg-governance-policy-lab
```

Ce groupe constitue le périmètre d'application des règles de gouvernance.

Il permet de tester les restrictions de déploiement et la protection contre la suppression sans modifier les autres groupes de ressources de l'abonnement.

---

## 2️⃣ Attribution d'une stratégie Azure Policy

Depuis le service **Stratégie (Azure Policy)**, attribution de la définition intégrée :

```text
Emplacements autorisés
```

Étendue de l'attribution :

```text
Azure subscription 1
        |
        v
rg-governance-policy-lab
```

La stratégie est configurée pour autoriser uniquement la région :

```text
West Europe
```

L'objectif est d'empêcher les déploiements de ressources concernées par cette stratégie dans les autres régions Azure.

---

## 3️⃣ Test d'une région non autorisée

Ouverture de l'assistant de création d'un compte de stockage Azure.

Groupe de ressources sélectionné :

```text
rg-governance-policy-lab
```

Région sélectionnée :

```text
France Central
```

Azure affiche le message suivant :

```text
Deployment denied by Azure Policy.
Only West Europe is allowed
for this resource group.
```

Ce message confirme que la stratégie empêche l'utilisation de cette région dans le formulaire de déploiement.

---

## 4️⃣ Test d'une région autorisée

Dans le même assistant de création du compte de stockage, modification de la région :

```text
West Europe
```

Le message de refus Azure Policy disparaît.

La région sélectionnée respecte désormais les paramètres de la stratégie.

Ce test valide l'acceptation de la région autorisée dans le formulaire de création.

Aucun déploiement effectif du compte de stockage n'est démontré par cette capture.

---

## 5️⃣ Vérification de la conformité Azure Policy

Retour dans le service **Stratégie**, puis consultation de la conformité de l'attribution :

```text
Emplacements autorisés
```

Azure affiche :

```text
État de conformité : Conforme

Conformité générale : 100 %

Ressources conformes : 0

Ressources non conformes : 0
```

L'état de conformité est affiché comme conforme.

Cependant, aucune ressource n'est comptabilisée dans cette évaluation au moment de la capture.

La validation principale de la restriction repose donc sur les tests effectués dans l'assistant de création du compte de stockage.

---

## 6️⃣ Création d'un verrou de ressources

Dans le groupe de ressources :

```text
rg-governance-policy-lab
```

Ouverture de la section :

```text
Paramètres → Verrous
```

Création d'un verrou nommé :

```text
lock-prevent-delete
```

Type de verrou :

```text
Supprimer (CanNotDelete)
```

Ce verrou permet d'empêcher la suppression du groupe de ressources et des ressources auxquelles il s'applique, tout en autorisant les modifications qui ne nécessitent pas de suppression.

---

## 7️⃣ Vérification du verrou

Après sa création, le verrou apparaît dans la liste des verrous du groupe de ressources.

Configuration vérifiée :

```text
Nom : lock-prevent-delete

Type : Supprimer

Portée : rg-governance-policy-lab
```

Cette étape confirme que la protection est correctement configurée.

---

## 8️⃣ Test pratique de la protection contre la suppression

Depuis la vue d'ensemble du groupe de ressources, lancement d'une tentative de suppression.

Groupe concerné :

```text
rg-governance-policy-lab
```

Azure refuse l'opération et affiche une notification :

```text
Échec de la suppression du groupe
de ressources rg-governance-policy-lab
```

Le portail précise que le groupe de ressources est verrouillé et ne peut pas être supprimé.

Ce test confirme le fonctionnement du verrou **CanNotDelete**.

Le groupe de ressources reste protégé contre la suppression.

---

# 📸 Captures d'écran

## Azure Policy

### 1. Refus d'un déploiement en France Central

Azure Policy affiche un message de refus lorsque la région France Central est sélectionnée.

![Refus Azure Policy France Central](azure-policy/01-policy-deny-france-central.png)

---

### 2. Validation de la région West Europe

La région West Europe est sélectionnée et aucun message de refus Azure Policy n'apparaît.

![Région West Europe autorisée](azure-policy/02-policy-allow-west-europe.png)

---

### 3. Vérification de la conformité Azure Policy

Consultation de l'état de conformité de la stratégie Emplacements autorisés.

Le portail affiche 100 % de conformité avec aucune ressource évaluée.

![Conformité Azure Policy](azure-policy/03-policy-compliance.png)

---

## Azure Resource Locks

### 4. Vue d'ensemble du groupe de ressources

Consultation du groupe de ressources `rg-governance-policy-lab`, utilisé pour les tests de gouvernance.

![Vue d'ensemble du groupe de ressources](resource-locks/01-resource-group-overview.png)

---

### 5. Création et vérification du verrou

Le verrou `lock-prevent-delete` apparaît dans la liste des verrous avec le type **Supprimer**.

![Verrou Azure créé](resource-locks/02-resource-lock-created.png)

---

### 6. Validation du blocage de suppression

Une tentative de suppression du groupe de ressources échoue.

Azure confirme que le groupe est verrouillé et ne peut pas être supprimé.

![Validation du verrou Azure](resource-locks/03-resource-lock-validation.png)

---

# 🔐 Principe de sécurité appliqué

Ce laboratoire applique deux mécanismes de gouvernance complémentaires.

**1. Contrôle des déploiements avec Azure Policy**

```text
Demande de déploiement
          |
          v
     Azure Policy
          |
          v
  Vérification région
          |
    +-----+-----+
    |           |
    v           v
West Europe  France Central
    |           |
    v           v
 Autorisé      Refusé
```

La stratégie permet de limiter les déploiements aux régions approuvées par l'organisation.

**2. Protection contre la suppression avec Resource Locks**

```text
Groupe de ressources
          |
          v
  Verrou CanNotDelete
          |
          v
 Tentative de suppression
          |
          v
   Opération refusée
```

Cette approche permet de protéger les ressources contre les suppressions accidentelles.

Azure Policy et Resource Locks ne remplacent pas Azure RBAC : ils complètent la gestion des autorisations en ajoutant des restrictions de gouvernance et des protections administratives.

---

# ✅ Validation

Les éléments suivants ont été testés et validés :

- création d'un groupe de ressources Azure ;
- utilisation du service Azure Policy ;
- sélection d'une définition de stratégie intégrée ;
- attribution d'une stratégie à un Resource Group ;
- configuration des emplacements autorisés ;
- restriction à la région West Europe ;
- vérification du refus de France Central ;
- vérification de l'acceptation de West Europe dans le formulaire ;
- consultation de l'état de conformité Azure Policy ;
- identification du nombre de ressources évaluées ;
- accès à la gestion des verrous Azure ;
- création d'un verrou CanNotDelete ;
- vérification de la portée du verrou ;
- tentative de suppression du groupe de ressources ;
- validation du refus de suppression ;
- vérification du message d'erreur Azure.

---

# 📚 Ce que j'ai appris

Ce projet m'a permis d'apprendre à :

- comprendre les principes de gouvernance Microsoft Azure ;
- utiliser Azure Policy ;
- distinguer une définition de stratégie d'une attribution ;
- définir la portée d'une stratégie ;
- configurer les régions autorisées ;
- comprendre le fonctionnement de l'effet Deny ;
- tester les restrictions de déploiement ;
- interpréter les messages de refus Azure Policy ;
- consulter les résultats de conformité ;
- comprendre les limites d'une évaluation sans ressources ;
- utiliser Azure Resource Locks ;
- comprendre le fonctionnement du verrou CanNotDelete ;
- protéger un groupe de ressources contre la suppression ;
- tester concrètement une protection administrative ;
- distinguer Azure Policy, Azure RBAC et Resource Locks ;
- appliquer des mécanismes de gouvernance dans un environnement Azure.

---

# 💼 Compétences démontrées

- Administration Microsoft Azure
- Azure Governance
- Azure Policy
- Azure Policy Assignments
- Azure Policy Compliance
- Gestion des stratégies intégrées
- Configuration des emplacements autorisés
- Restriction des régions de déploiement
- Contrôle de conformité Azure
- Azure Resource Locks
- Gestion des verrous CanNotDelete
- Protection des groupes de ressources
- Azure Resource Manager (ARM)
- Administration des Azure Resource Groups
- Test et validation des restrictions de déploiement
- Analyse des messages d'erreur Azure
- Protection contre les suppressions accidentelles
- Gouvernance et administration des ressources cloud
- Préparation à la certification Microsoft Azure AZ-104
