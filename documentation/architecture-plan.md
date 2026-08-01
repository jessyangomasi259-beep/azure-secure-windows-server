# Plan d’architecture Azure

## 1. Présentation du projet

Ce projet consiste à déployer un serveur Windows sécurisé sur Microsoft Azure pour une petite entreprise fictive.

Le serveur hébergera une application web interne de démonstration basée sur IIS.

L’environnement devra être :

- organisé avec une convention de nommage cohérente ;
- isolé dans un réseau virtuel ;
- administrable à distance de manière sécurisée ;
- protégé par Azure Backup ;
- supervisé avec Azure Monitor ;
- associé à des alertes opérationnelles ;
- documenté et reproductible ;
- optimisé pour limiter les coûts du lab.

---

## 2. Scénario client

Une petite entreprise souhaite héberger une application interne sur un serveur Windows.

Les exigences sont les suivantes :

- héberger l’application sur Windows Server ;
- permettre l’administration distante ;
- éviter l’exposition permanente du port RDP à Internet ;
- sauvegarder automatiquement le serveur ;
- pouvoir restaurer des fichiers supprimés ;
- surveiller les performances et les événements système ;
- recevoir une alerte en cas d’incident ;
- limiter les dépenses Azure ;
- disposer d’une documentation de transfert.

---

## 3. Région Azure

Région principale prévue :

`West Europe`

Abréviation utilisée dans les noms :

`weu`

La région définit l’emplacement principal dans lequel seront créées les ressources du projet.

Le choix définitif pourra être ajusté selon :

- la disponibilité des tailles de machines virtuelles ;
- la disponibilité des services Azure nécessaires ;
- les quotas de l’abonnement ;
- les coûts affichés lors du déploiement.

---

## 4. Convention de nommage

Format général :

`<type>-<projet>-<environnement>-<région>-<instance>`

Valeurs utilisées :

- Projet : `sws`
- Environnement : `lab`
- Région : `weu`
- Première instance : `01`

`sws` signifie `Secure Windows Server`.

### Noms prévus

| Ressource | Nom prévu |
|---|---|
| Groupe de ressources | `rg-sws-lab-weu-01` |
| Réseau virtuel | `vnet-sws-lab-weu-01` |
| Sous-réseau serveur | `snet-server-lab-weu-01` |
| Groupe de sécurité réseau | `nsg-sws-lab-weu-01` |
| Machine virtuelle | `vm-sws-lab-weu-01` |
| Interface réseau | `nic-sws-lab-weu-01` |
| Disque du système d’exploitation | `osdisk-sws-lab-weu-01` |
| Espace Log Analytics | `law-sws-lab-weu-01` |
| Règle de collecte de données | `dcr-sws-lab-weu-01` |
| Coffre Recovery Services | `rsv-sws-lab-weu-01` |
| Groupe d’actions | `ag-sws-lab-weu-01` |

Certains noms pourront être adaptés si une ressource Azure applique des restrictions particulières.

---

## 5. Tags prévus

Les tags suivants seront appliqués aux ressources lorsque le service les prend en charge :

| Tag | Valeur |
|---|---|
| `Environment` | `Lab` |
| `Project` | `SecureWindowsServer` |
| `Purpose` | `Portfolio` |
| `CostCenter` | `Learning` |
| `Criticality` | `Low` |
| `DataClassification` | `Test` |

Aucune donnée réelle ou confidentielle ne sera stockée dans cet environnement.

---

## 6. Plan réseau

### Réseau virtuel

Nom :

`vnet-sws-lab-weu-01`

Plage d’adresses :

`10.10.0.0/16`

### Sous-réseau serveur

Nom :

`snet-server-lab-weu-01`

Plage d’adresses :

`10.10.1.0/24`

La machine virtuelle Windows sera placée dans ce sous-réseau.

### Adresse IP privée prévue

L’adresse IP privée sera attribuée dynamiquement par Azure.

### Adresse IP publique

L’objectif initial est de ne pas attribuer d’adresse IP publique directement à la machine virtuelle.

Une solution d’accès sécurisé comme Azure Bastion Developer sera étudiée.

Si cette solution n’est pas disponible dans la région ou l’abonnement, un accès temporaire pourra être configuré avec les protections suivantes :

- autorisation du port TCP 3389 uniquement depuis une adresse IP publique précise ;
- utilisation d’un préfixe `/32` ;
- suppression ou désactivation de la règle après la configuration ;
- absence d’autorisation depuis `0.0.0.0/0`.

---

## 7. Machine virtuelle

### Système d’exploitation

Windows Server 2022 Datacenter Azure Edition ou une image Windows Server équivalente disponible lors du déploiement.

### Taille cible

Une petite machine virtuelle de la famille B sera utilisée pour ce lab.

La taille exacte sera sélectionnée selon :

- la disponibilité dans la région ;
- les quotas de l’abonnement ;
- la mémoire nécessaire à Windows Server et IIS ;
- le prix affiché dans le portail.

Configuration cible approximative :

- 2 processeurs virtuels ;
- 4 Gio de mémoire ;
- charge de travail légère et non critique.

### Stockage

Le disque du système d’exploitation utilisera un stockage économique adapté à un environnement de lab.

Aucune donnée de production ne sera utilisée.

### Arrêt automatique

Un arrêt automatique sera configuré afin de réduire les coûts de calcul lorsque la machine virtuelle n’est pas utilisée.

L’arrêt de la machine virtuelle ne supprime pas les coûts des disques et de certaines ressources associées.

---

## 8. Application de démonstration

Le rôle Windows Server IIS sera installé.

Une page web interne de démonstration sera créée afin de :

- vérifier le fonctionnement de la machine virtuelle ;
- tester le service web ;
- produire une preuve visuelle ;
- simuler un incident en arrêtant IIS ;
- vérifier la détection et la résolution de l’incident.

---

## 9. Sécurité

Les principes suivants seront appliqués :

- pas de mot de passe publié dans GitHub ;
- pas d’identifiant sensible dans les captures ;
- pas de port RDP ouvert à tout Internet ;
- utilisation du moindre privilège ;
- séparation entre administration Azure et administration Windows ;
- limitation des règles réseau ;
- suppression des ressources inutilisées ;
- vérification des journaux et des événements ;
- utilisation exclusive de données fictives.

Une matrice RBAC sera créée plus tard pour tester différents niveaux d’accès.

---

## 10. Sauvegarde

Un coffre Recovery Services sera utilisé pour protéger la machine virtuelle.

Les tests prévus sont :

1. créer plusieurs fichiers de démonstration ;
2. exécuter une sauvegarde ;
3. supprimer volontairement un fichier ;
4. lancer une procédure de récupération ;
5. vérifier que le fichier a été restauré ;
6. documenter la durée et le résultat.

Le projet ne considérera pas la sauvegarde comme fonctionnelle tant qu’une restauration n’aura pas été testée.

---

## 11. Supervision

La supervision reposera sur :

- Azure Monitor ;
- Azure Monitor Agent ;
- une Data Collection Rule ;
- un espace Log Analytics ;
- des métriques de la machine virtuelle ;
- des événements Windows ;
- un groupe d’actions ;
- des règles d’alerte.

Les signaux étudiés comprendront notamment :

- utilisation du processeur ;
- disponibilité de la machine virtuelle ;
- espace disque ;
- événements système ;
- fonctionnement du service IIS.

---

## 12. Tests d’incident

Des incidents contrôlés seront simulés.

### Incident 1 — Arrêt du service IIS

Le service web sera arrêté volontairement.

Le test devra montrer :

- l’indisponibilité de l’application ;
- la détection de l’incident ;
- le diagnostic ;
- le redémarrage du service ;
- le retour à un fonctionnement normal.

### Incident 2 — Charge processeur élevée

Une charge temporaire sera générée afin de tester une alerte sur l’utilisation du processeur.

### Incident 3 — Suppression d’un fichier

Un fichier de test sera supprimé puis restauré depuis Azure Backup.

---

## 13. Gestion des coûts

Les mesures suivantes seront appliquées :

- choix d’une petite taille de machine virtuelle ;
- arrêt automatique ;
- désallocation de la machine après les tests ;
- création d’un budget Azure ;
- création d’alertes budgétaires ;
- suppression des ressources inutiles ;
- contrôle régulier de Cost Management ;
- suppression complète du lab à la fin du projet si nécessaire.

Les montants publiés dans le portfolio seront basés sur les prix et estimations réellement observés.

---

## 14. Architecture logique prévue

```text
Administrateur
      |
      | Accès distant sécurisé
      v
Azure Bastion ou accès RDP temporairement restreint
      |
      v
vnet-sws-lab-weu-01
      |
      v
snet-server-lab-weu-01
      |
      +--- nsg-sws-lab-weu-01
      |
      v
vm-sws-lab-weu-01
      |
      +--- Windows Server
      +--- IIS
      +--- Azure Monitor Agent
      |
      +--- Azure Monitor / Log Analytics
      +--- Azure Backup / Recovery Services Vault
      +--- Action Group / Alertes
