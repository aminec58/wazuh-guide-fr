# Wazuh – Guide complet en français

Documentation de A à Z de la mise en place et de l'exploitation de **Wazuh** (SIEM / XDR open source) : installation, agents, détection, réponse active, conformité, exploitation, durcissement et dépannage.

> Intégration détaillée : **Stormshield SNS** (collecte, décodage et analyse des logs du pare-feu).

---

## Architecture du labo

[Schéma : docs/assets/schemas/topologie.png]

| Machine | Rôle | OS | IP |
| --- | --- | --- | --- |
| wazuh-server | Manager + indexer + dashboard | Ubuntu Server LTS | à compléter |
| win-client | Agent | Windows 10/11 | à compléter |
| win-srv | Agent | Windows Server | à compléter |
| deb-srv | Agent | Debian / Ubuntu | à compléter |
| rocky-srv | Agent | Rocky Linux | à compléter |
| kali | Attaquant | Kali Linux | à compléter |
| sns | Pare-feu (syslog) | Stormshield SNS | à compléter |

Détails : [`lab/conventions.md`](lab/conventions.md)

## Versions

| Composant | Version |
| --- | --- |
| Wazuh | 4.x.x (à fixer) |
| Stormshield SNS | à fixer |

## Sommaire

| # | Partie | Statut |
| --- | --- | --- |
| 1 | [Cadrage](docs/01-cadrage/) | ⬜ |
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

Légende des niveaux de profondeur : **T** = testé en labo · **C** = configuré, non déployé · **P** = présenté avec config type

## Couverture MITRE ATT&CK

Voir [`attack-simulation/mitre-coverage.md`](attack-simulation/mitre-coverage.md).

## Organisation du dépôt

```
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
