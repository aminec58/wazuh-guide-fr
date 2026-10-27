# Wazuh – Guide complet en français

Documentation de A à Z de la mise en place et de l'exploitation de **Wazuh** (SIEM / XDR open source) : installation, agents, détection, réponse active, conformité, exploitation, durcissement et dépannage.

> Intégration détaillée : **Stormshield SNS** (collecte, décodage et analyse des logs du pare-feu).

Le guide est écrit pour être **reproductible** : un lecteur qui ne connaît pas Wazuh doit pouvoir reconstruire la maquette et obtenir les mêmes alertes (identifiants de règles, niveaux) en suivant uniquement ce document.

---

## Versions

Versions figées pour toute la durée du projet : aucune mise à jour n'est appliquée en cours de route.

| Composant | Version |
| --- | --- |
| Wazuh (manager, indexer, dashboard, agents) | 4.14.9 |
| Stormshield SNS (firmware) | 4.8.20 LTSB |
| Ubuntu Server (serveur Wazuh) | 24.04 LTS |
| Hyperviseur | VMware Workstation Pro |

Wazuh 5.0, encore en phase de release candidate, est seulement signalé dans des encadrés « Différence Wazuh 5.0 ».

## Architecture du labo

[Schéma : docs/assets/schemas/topologie.png]

| Machine | Rôle | OS | IP |
| --- | --- | --- | --- |
| wazuh-server | Manager + indexer + dashboard | Ubuntu Server 24.04 LTS | 192.168.10.10 |
| win-client | Agent | Windows 11 | 192.168.10.31 |
| win-srv | Agent, Active Directory | Windows Server 2025 | 192.168.10.32 |
| deb-srv | Agent | Debian / Ubuntu | 192.168.10.21 |
| rocky-srv | Agent | Rocky Linux | 192.168.10.22 |
| kali | Attaquant | Kali Linux | 192.168.20.50 |
| sns | Pare-feu (syslog) | Stormshield SNS 4.8.20 LTSB (EVA) | 192.168.10.254 / 192.168.20.254 |

Plan d'adressage proposé, confirmé en Partie 3. Détails : [`lab/conventions.md`](lab/conventions.md)

## Sommaire

| # | Partie | Statut |
| --- | --- | --- |
| 1 | [Cadrage](docs/01-cadrage/) | ✅ |
| 2 | [Présentation de Wazuh](docs/02-presentation/) | ⬜ |
| 3 | [Prérequis et dimensionnement](docs/03-prerequis/) | ⬜ |
| 4 | [Installation](docs/04-installation/) | ⬜ |
| 5 | [Agents](docs/05-agents/) | ⬜ |
| 6 | [Capacités de détection](docs/06-detection/) | ⬜ |
| 7 | [Réponse active et automatisation](docs/07-reponse-active/) | ⬜ |
| 8 | [Conformité réglementaire](docs/08-conformite/) | ⬜ |
| 9 | [Intégration Stormshield SNS](docs/09-integration-stormshield/) | ⬜ |
| 10 | [Dashboard et exploitation](docs/10-dashboard-exploitation/) | ⬜ |
| 11 | [Scénarios de démonstration](docs/11-scenarios/) | ⬜ |
| 12 | [Sécurisation et durcissement](docs/12-securisation/) | ⬜ |
| 13 | [Maintenance](docs/13-maintenance/) | ⬜ |
| 14 | [Dépannage](docs/14-depannage/) | ⬜ |
| 15 | [Conclusion et annexes](docs/15-conclusion-annexes/) | ⬜ |

Statut : ✅ rédigée · 🟡 en cours · ⬜ à faire

Légende des niveaux de profondeur : **T** = testé en labo · **C** = configuré, non déployé · **P** = présenté avec config type

**Ordre de lecture conseillé :** Parties 1 et 2 pour découvrir l'outil, 3 à 5 pour construire la maquette, 6 à 8 pour la détection et la réponse, 9 pour Stormshield, 10 à 14 pour l'exploitation, 15 pour les annexes.

## Conventions de lecture

| Élément | Signification | Exemple |
| --- | --- | --- |
| Étiquette en tête de bloc | Machine sur laquelle agir | `[Serveur]`, `[Agent Linux]`, `[Agent Windows]`, `[Kali]`, `[SNS]` |
| Invite de commande | Machine et utilisateur | `root@wazuh-server:~#`, `PS C:\>`, `kali@kali:~$` |
| `<MAJUSCULES>` | Valeur à adapter à votre environnement | `<IP_SNS>` |
| **Gras + flèches** | Chemin dans une interface graphique | **Configuration → Notifications** |
| `> **À vérifier** — …` | Information non confirmée par la documentation officielle | — |
| `> **Différence Wazuh 5.0** — …` | Changement prévu dans la version 5.0 de Wazuh | — |
| `[Capture : …]` | Emplacement d'une capture d'écran | `[Capture : 9.8-03 – alarme IPS]` |

## Couverture MITRE ATT&CK

Voir [`attack-simulation/mitre-coverage.md`](attack-simulation/mitre-coverage.md).

## Organisation du dépôt

```text
docs/               Documentation, une partie par dossier
lab/                Maquette : conventions, topologie, VM
wazuh/              Configuration déployable (manager, agents, SCA, YARA, dashboards)
integrations/       Intégrations externes (Stormshield, VirusTotal, MISP, TheHive…)
tests/              Tests logtest et scénarios
attack-simulation/  Simulations d'attaques et matrice MITRE
journal/            Journal de bord (erreurs, causes, solutions)
```

## Plages d'IDs des règles personnalisées

| Plage | Thème | Fichier |
| --- | --- | --- |
| 100000–100199 | Stormshield SNS | `wazuh/manager/rules/100000-stormshield.xml` |
| 100200–100399 | Windows / Sysmon | `wazuh/manager/rules/100200-windows-sysmon.xml` |
| 100400–100599 | Linux / auditd | `wazuh/manager/rules/100400-linux-auditd.xml` |
| 100600–100799 | FIM | `wazuh/manager/rules/100600-fim.xml` |
| 100800–100999 | Threat intelligence | `wazuh/manager/rules/100800-threat-intel.xml` |

## Auteur

**aminec58** – BUT Réseaux & Télécommunications, parcours Cybersécurité

## Licence

Code et configurations : MIT · Documentation : CC BY 4.0 (voir [`LICENSE`](LICENSE))
