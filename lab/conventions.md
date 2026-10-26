# Conventions du projet

## Versions de référence

| Composant | Version |
| --- | --- |
| Wazuh | 4.x.x |
| Stormshield SNS (firmware) | |
| Ubuntu Server | |
| Hyperviseur | VMware Workstation Pro |

## Plan d'adressage

| Hostname | Rôle | OS | IP | RAM | Disque |
| --- | --- | --- | --- | --- | --- |
| wazuh-server | Manager + indexer + dashboard | Ubuntu Server LTS | | | |
| wazuh-indexer | Indexer (variante distribuée) | Ubuntu Server LTS | | | |
| wazuh-manager | Manager (variante distribuée) | Ubuntu Server LTS | | | |
| wazuh-dashboard | Dashboard (variante distribuée) | Ubuntu Server LTS | | | |
| win-client | Agent | Windows 10/11 | | | |
| win-srv | Agent | Windows Server | | | |
| deb-srv | Agent | Debian / Ubuntu | | | |
| rocky-srv | Agent | Rocky Linux | | | |
| kali | Attaquant | Kali Linux | | | |
| sns | Pare-feu | Stormshield SNS | | | |

## Plages d'IDs des règles

| Plage | Thème |
| --- | --- |
| 100000–100199 | Stormshield SNS |
| 100200–100399 | Windows / Sysmon |
| 100400–100599 | Linux / auditd |
| 100600–100799 | FIM |
| 100800–100999 | Threat intelligence |

## Nommage des captures

`<section>-<numéro>_<description-courte>.png`
Exemple : `9.8-03_alarme-ips.png`

## Niveaux de profondeur

- **T** : testé en labo avec captures
- **C** : configuré mais non déployé
- **P** : présenté avec config type
