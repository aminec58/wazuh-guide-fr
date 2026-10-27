# Conventions du projet

> Référence pour toute la rédaction. Fixée en Séance 1 (26/10/2026) et fusionnée avec la structure du dépôt le 27/10/2026. Toute modification est datée dans le journal en bas de page.

---

## 1. Versions de référence (gel)

| Composant | Version figée | Publication | Statut vérifié le 26/10/2026 |
| --- | --- | --- | --- |
| Wazuh (manager, indexer, dashboard, agents) | **4.14.9** | 14/10/2026 | Dernière version stable 4.x. Une **4.14.10 RC 1** existe (pré-version GitHub du 21/10/2026) : non retenue |
| Stormshield SNS (firmware) | **4.8.20 LTSB** | 15/10/2026 | Branche 4.8 LTSB (LTSB depuis août 2025) : fin de maintenance **31/12/2029**, fin de vie **31/03/2030** |
| Ubuntu Server (serveur Wazuh) | **24.04 LTS** (point release relevée à l'installation) | — | Confirmé : listé dans les OS recommandés Wazuh 4.14 (Ubuntu 16.04 → 24.04, RHEL 7 → 10, CentOS Stream 10, Amazon Linux 2 / 2023) |
| Hyperviseur | **VMware Workstation Pro** (version relevée à l'installation) | — | Gratuit pour un usage personnel. **Absent de la matrice officielle des EVA** en version 4.8 (ESXi 7.0 et 8.0, XenServer 7.6, KVM sous RHEL 8.4 et 9, Hyper-V sous Windows Server 2019 et 2022) : écart signalé en Partie 9 |
| Attaquant | Kali Linux (rolling, version relevée à l'installation) | — | — |

### Règles de gel
- **Tous les composants Wazuh en 4.14.9**, agents compris : un agent ne doit jamais être plus récent que le manager.
- **Aucune mise à jour pendant le projet**, même si la 4.14.10 passe en GA : elle est seulement mentionnée (Partie 13.3). Dépôts désactivés après installation (§4.7).
- Le firmware SNS réellement installé (EVA ou boîtier) est relevé dans *Tableau de bord → Propriétés* et reporté ici. S'il diffère de 4.8.20, **la version réelle fait foi** et l'écart est signalé en Partie 9. Si la maquette de l'IUT est encore en **4.3 LTSB** : branche en fin de vie depuis le 31/03/2026, à signaler explicitement.
- Branche **LTSB** retenue (support long). SNS v5 mentionnée, non utilisée : **5.0** (sortie 24/09/2025, label EA retiré le 26/02/2026, branche non LTSB) et **5.1 EA** (13/08/2026). Aucune LTSB v5 annoncée à ce jour.

### Wazuh 5.0 — à mentionner, pas à utiliser
- État au 26/10/2026 : **phase RC** (documentation `5.0-rc` en ligne, marquée « en cours de développement »), pas de GA → hors périmètre.
- Les points ci-dessous proviennent de la doc 5.0-beta et des notes de la Beta 5 : **à revérifier à la sortie GA**.

| Changement 5.0 | Parties impactées |
| --- | --- |
| Pas de mise à jour en place depuis 4.x : nouveau déploiement, configuration recréée, données indexées 4.x non migrées | 4, 13.3 |
| Nouveau moteur (Wazuh Engine + Security Analytics) : décodeurs YAML, règles compatibles Sigma, *findings* à la place des alertes, `rule.level` → `wazuh.rule.level`, sévérités nommées (informational → critical) | 6.2, 6.14, 9.7–9.9, 10 |
| Filebeat supprimé : envoi direct manager → indexer | 2.3, 2.4, 14.2 |
| Config manager : `/var/ossec/etc/ossec.conf` → `/var/wazuh-manager/etc/wazuh-manager.conf` | toutes |
| Règles/décodeurs custom gérés par le système de gestion de contenu (plus de simple copie des fichiers XML) | 6.2, 9.7, 9.8, 15.2 |
| Options manager supprimées : `logall`, `logall_json`, `alerts_log`, `jsonout_output`, `email_*` | 7.4, 9.5, 9.6, 14.1 |
| `<syscheck>`, `<rootcheck>`, `<localfile>`, syscollector : configuration déplacée côté agent | 6.1, 6.3, 6.6, 6.7 |
| `wazuh-agentlessd` et `wazuh-maild` non installés, `agent-auth` plus fourni séparément, modules dashboard Osquery et CIS-CAT retirés | 5.3, 5.7, 6.8, 7.4 |
| Active response : JSON sur stdin avec `enable`/`disable` au lieu de `add`/`delete` | 7.1, 7.2 |
| Agents legacy sur 1514 (bloc `<remote><legacy>`), nouveau listener HTTPS sur 1517 | 2.5 |
| `integratord`, `csyslogd` : sort non confirmé dans les sources consultées | 7.4 → **À vérifier** |

- Format d'encadré : `> **Différence Wazuh 5.0** — …`

---

## 2. Organisation du dépôt et format

Dépôt : `aminec58/wazuh-guide-fr` sur GitHub, **public**. Format imposé par le professeur : **Markdown (.md)**.

### Emplacement des contenus

| Contenu | Emplacement |
| --- | --- |
| Page de garde, sommaire, statut des parties | `README.md` (racine) |
| Partie N | `docs/<NN>-<nom>/README.md` |
| Captures d'écran | `docs/assets/captures/` |
| Schémas exportés (PNG) | `docs/assets/schemas/` |
| Conventions (ce fichier), maquette | `lab/` |
| Règles, décodeurs, listes CDB, SCA, YARA, tableaux de bord | `wazuh/` (fichiers déployables) |
| Intégrations et échantillons de journaux | `integrations/<outil>/` |
| Tests `wazuh-logtest` | `tests/logtest/` |
| Fiches de scénarios (modèle `_modele.md`) | `tests/scenarios/` |
| Couverture MITRE ATT&CK | `attack-simulation/mitre-coverage.md` |
| Problèmes rencontrés (alimente 14 et 15.1) | `journal/journal-de-bord.md` |
| Historique du document, une entrée par séance | `CHANGELOG.md` |

### Configurations : fichiers déployables
- Une règle, un décodeur ou une configuration complète vit dans son propre fichier sous `wazuh/` ou `integrations/`. La documentation l'explique et le cite par son chemin dans le dépôt **et** par son chemin de destination. Exemple : `wazuh/manager/rules/100000-stormshield.xml` → **[Serveur]** `/var/ossec/etc/rules/100000-stormshield.xml`.
- Un extrait cité dans la documentation reste identique au fichier du dépôt.
- À chaque push, la CI (`.github/workflows/validate.yml`) valide la syntaxe XML des fichiers de `wazuh/` et refuse un ID de règle en double.

### Saveur Markdown
- Cible : **GitHub Flavored Markdown (GFM)**, lisible tel quel sur GitHub, GitLab, VS Code et Obsidian.
- Autorisé : titres, tableaux, blocs de code avec langage, gras, italique, liens, images relatives, citations.
- À éviter, car rendu différemment selon la visionneuse : HTML brut, notes de bas de page, syntaxe Pandoc (`{#id}`, `:::`), en-tête YAML, encadrés propres à GitHub (`> [!WARNING]`). Les encadrés restent en texte : `> **À vérifier** — …`, `> **Différence Wazuh 5.0** — …`.
- Encodage UTF-8, fins de ligne LF.

### Titres, numérotation et renvois
- `#` Partie · `##` x.y · `###` x.y.z · `####` étapes. Un seul titre `#` par fichier.
- Numéros écrits à la main dans les titres (`## 9.4 Configuration de l'envoi syslog sur le SNS`), conformes au plan : Markdown ne numérote pas.
- Renvois par numéro de section dans le texte (« cf. 9.13 »). Pas de liens entre parties ni d'ancres de titres (fragiles avec les accents) ; seul le `README.md` pointe vers les dossiers et fichiers.

### Captures
- Nom : `<section>-<numéro>_<description-courte>.png`, par exemple `9.8-03_alarme-ips.png`.
- Insertion depuis une partie : `![Alarme IPS dans le dashboard](../assets/captures/9.8-03_alarme-ips.png)`.
- Tant que la capture n'existe pas : `[Capture : 9.8-03 – alarme IPS dans le dashboard]`.
- Prises pendant la séance où la manipulation est faite ; même taille de fenêtre et même thème du dashboard du début à la fin ; recadrage sur la zone utile.
- Relues et masquées avant chaque commit (§8).

### Schémas
- Gantt (1.4), cheminement d'un événement (2.4) et architectures (2.6) en **Mermaid** (bloc `mermaid`), affiché nativement par GitHub et GitLab.
- Exports PNG éventuels dans `docs/assets/schemas/` (mermaid-cli, `mmdc`), le code Mermaid restant la source.

### Pièges de rendu
- Tableaux : pas de bloc de code ni de retour à la ligne dans une cellule ; un `|` dans une cellule (alternative de regex Wazuh `a|b`) s'écrit `\|`.
- Logs SNS : une ligne = un log, jamais coupée à la main, sinon elle n'est plus rejouable dans `wazuh-logtest`. Les lignes longues défilent horizontalement dans le bloc.
- Chaque fichier se termine par une ligne vide.

### Fichier unique (si exigé)
Généré hors du dépôt, sans rien changer pendant la rédaction ; `sed` corrige les chemins des images, qui partent alors de la racine :
```bash
cat README.md docs/*/README.md | sed 's#](\.\./assets/#](docs/assets/#g' > ../wazuh-guide-fr.md
```

---

## 3. Conventions typographiques

| Élément | Convention | Exemple |
| --- | --- | --- |
| Fichier / chemin | `code` + machine concernée | **[Serveur]** `/var/ossec/etc/ossec.conf` |
| Commande | bloc de code avec invite | voir ci-dessous |
| Langage des blocs | toujours précisé | `bash`, `xml`, `yaml`, `json`, `powershell`, `mermaid`, `text` (logs bruts) |
| Paramètre XML | `code` | `<connection>syslog</connection>` |
| Menu d'interface | gras + flèches | **Configuration → Notifications → Traces – Syslog** |
| Valeur à adapter | `<MAJUSCULES>` | `<IP_SNS>` |
| Info non vérifiée | encadré texte (recherchable) | `> **À vérifier** — …` |
| Capture | placeholder, puis image | `[Capture : 9.8-03 – alarme IPS]` → `../assets/captures/9.8-03_alarme-ips.png` |

### Invites de commande
```bash
root@wazuh-server:~#    # serveur Wazuh (root)
amine@wazuh-server:~$   # serveur Wazuh (utilisateur)
root@deb-srv:~#         # agent Debian / Ubuntu
root@rocky-srv:~#       # agent Rocky Linux
PS C:\> ...             # agent Windows (PowerShell administrateur)
kali@kali:~$            # attaquant (invite simplifiée)
SNS> ...                # CLI Stormshield
```
- Invite Kali simplifiée : une seule ligne, comme les autres invites (l'invite zsh par défaut de Kali tient sur deux lignes).

### Étiquettes de machine (en tête de chaque bloc)
`[Serveur]` · `[Indexer]` · `[Dashboard]` · `[Agent Linux]` · `[Agent Windows]` · `[Kali]` · `[SNS]`

---

## 4. Structure imposée de chaque fonctionnalité

En tête : **Niveau : T**, **C** ou **P**.

| Niveau | Signification |
| --- | --- |
| **T** | Testé en labo, avec captures |
| **C** | Configuré, mais non déployé |
| **P** | Présenté avec une configuration type |

Puis, dans l'ordre :

1. Principe et intérêt
2. Configuration exacte (fichier complet ou extrait balisé, chemin, machine)
3. Explication de chaque paramètre (tableau)
4. Redémarrage et vérification
5. Test de déclenchement
6. Résultat attendu (`rule.id`, `rule.level`, emplacement dashboard)
7. Pièges et erreurs fréquentes
8. (si pertinent) Différence Wazuh 5.0 / différence OS

---

## 5. Maquette — plan d'adressage (proposition, confirmée en Partie 3)

| Hostname | Rôle | OS | IP | vCPU | RAM | Disque |
| --- | --- | --- | --- | --- | --- | --- |
| wazuh-server | Manager + indexer + dashboard | Ubuntu Server 24.04 LTS | 192.168.10.10/24 | 4 | 8 Go | 50 Go |
| wazuh-indexer | Indexer (variante distribuée) | Ubuntu Server 24.04 LTS | 192.168.10.11 | — | — | — |
| wazuh-manager | Manager (variante distribuée) | Ubuntu Server 24.04 LTS | 192.168.10.12 | — | — | — |
| wazuh-dashboard | Dashboard (variante distribuée) | Ubuntu Server 24.04 LTS | 192.168.10.13 | — | — | — |
| win-client | Agent | Windows 11 | 192.168.10.31 | 2 | 4 Go | 64 Go |
| win-srv | Agent, Active Directory | Windows Server 2025 | 192.168.10.32 | 2 | 4 Go | 60 Go |
| deb-srv | Agent | Debian / Ubuntu | 192.168.10.21 | 1 | 2 Go | 20 Go |
| rocky-srv | Agent | Rocky Linux | 192.168.10.22 | 1 | 2 Go | 20 Go |
| kali | Attaquant | Kali Linux | 192.168.20.50/24 (zone « externe ») | 2 | 4 Go | 40 Go |
| sns | Pare-feu | Stormshield SNS 4.8.20 LTSB (EVA1) | 192.168.10.254 (LAN), 192.168.20.254 (zone « externe ») | 1 | 2 Go | image fournie |

- Variante distribuée : dimensionnée en Partie 3 (— = à définir).
- Domaine local : `lab.local` — NTP : même source pour toutes les machines (horodatage des logs SNS, cf. 9.13).
- Syslog SNS → Wazuh : **TCP 514** par défaut (UDP documenté en variante).

---

## 6. Identifiants de règles et décodeurs

| Plage | Thème | Fichier (dépôt) |
| --- | --- | --- |
| 100000–100199 | Stormshield SNS | `wazuh/manager/rules/100000-stormshield.xml` |
| 100200–100399 | Windows / Sysmon | `wazuh/manager/rules/100200-windows-sysmon.xml` |
| 100400–100599 | Linux / auditd | `wazuh/manager/rules/100400-linux-auditd.xml` |
| 100600–100799 | FIM | `wazuh/manager/rules/100600-fim.xml` |
| 100800–100999 | Threat intelligence | `wazuh/manager/rules/100800-threat-intel.xml` |

- Chaque fichier est déployé sous le même nom dans **[Serveur]** `/var/ossec/etc/rules/`.
- Thème non couvert (conteneurs, cloud, réponse active…) : nouvelle plage de 200 IDs à partir de 101000, ajoutée ici et dans le `README.md` avant la première règle.
- Règles d'un scénario (Partie 11) : dans la plage de la source qui le détecte (ex. PowerShell suspect → Windows / Sysmon).
- Décodeurs Stormshield : `wazuh/manager/decoders/stormshield_decoders.xml` → **[Serveur]** `/var/ossec/etc/decoders/stormshield_decoders.xml`.
- Décodeur parent : `stormshield`. Décodeurs enfants (sibling decoders) : **même nom `stormshield`** et `<parent>stormshield</parent>`, un champ ou un petit groupe de champs par décodeur, comme dans l'exemple officiel Wazuh 4.14.
- Groupes de règles : `stormshield,` + catégorie (`filter`, `alarm`, `auth`, `vpn`…).

---

## 7. Sources et citations
- Références prioritaires : documentation officielle Wazuh **4.14** et Stormshield **SNS v4.8 LTSB**.
- Citer les URL versionnées (`documentation.wazuh.com/4.14/…`) plutôt que `/current/`, qui basculera vers la 5.0 à sa sortie.
- Chaque partie se termine par une section « Sources » ; bibliographie consolidée en annexe 15.2.
- Toute info non trouvée dans la doc officielle est marquée `> **À vérifier** — …`.

### Sources consultées en Séance 1 (26 et 27/10/2026)
- Wazuh — Release notes 4.14.9 : https://documentation.wazuh.com/current/release-notes/release-4-14-9.html
- Wazuh — Index des release notes 4.14 : https://documentation.wazuh.com/4.14/release-notes/index.html
- Wazuh — Pré-version 4.14.10 RC 1 (GitHub) : https://github.com/wazuh/wazuh/releases/tag/v4.14.10-rc1
- Wazuh — Quickstart (matériel et OS recommandés) : https://documentation.wazuh.com/current/quickstart.html
- Wazuh — Sibling decoders (4.14) : https://documentation.wazuh.com/4.14/user-manual/ruleset/decoders/sibling-decoders.html
- Wazuh — Documentation 5.0-rc (statut) : https://documentation.wazuh.com/5.0-rc/installation-guide/wazuh-indexer/index.html
- Wazuh — Transition 4.x → 5.x, manager (doc 5.0-beta) : https://documentation.wazuh.com/5.0-beta/transitioning-from-4x-to-5x/wazuh-manager.html
- initMAX — « Wazuh 5.0 is almost here! » (Beta 5, 06/09/2026) : https://www.initmax.com/wazuh-5-0/
- Stormshield — Notes de version SNS 4.8.20 LTSB : https://documentation.stormshield.eu/SNS/v4/en/Content/Release_Notes_SNS/Getting_Started_RNO.htm
- Stormshield — Cycle de vie SNS, matrice des versions : https://documentation.stormshield.eu/PLC/SNS/en/Content/SNS_Product_Life_Cycle/Matrices_firmwares.htm
- Stormshield — Cycle de vie SNS, historique : https://documentation.stormshield.eu/PLC/SNS/en/Content/SNS_Product_Life_Cycle/Getting_started.htm
- Stormshield — Hyperviseurs supportés par les pare-feu virtuels : https://documentation.stormshield.eu/PLC/SNS/en/Content/SNS_Product_Life_Cycle/Matrices_virtual_firewall_hypervisors.htm
- Linuxiac — VMware Workstation Pro 17.6.2 Now Free for All Users (19/12/2024) : https://linuxiac.com/vmware-workstation-pro-is-now-free-for-all-users/

---

## 8. Publication (dépôt public)
- Tout ce qui est poussé est lisible par tous et reste dans l'historique Git, même après suppression du fichier.
- Jamais de secret réel dans un fichier, un journal ou une capture : mots de passe, clés d'API (VirusTotal, Slack…), URL de webhook, `client.keys`, certificats et clés privées. Dans le document, ils sont remplacés par des `<MAJUSCULES>`. Les fichiers sensibles courants sont exclus par `.gitignore`.
- Échantillons de journaux : anonymisés avant commit selon `integrations/stormshield/samples/README.md`.
- Captures : mots de passe, numéros de série, licences et compte MyStormshield masqués avant commit.
- Secret publié par erreur : le **révoquer ou le changer** immédiatement.

---

## Journal des modifications
| Date | Modification |
| --- | --- |
| 26/10/2026 | Création — gel Wazuh 4.14.9 / SNS 4.8.20 LTSB, proposition de format Markdown → PDF |
| 26/10/2026 | Séance 1 — versions revérifiées (4.14.10 RC 1 et 5.0 RC notées, cycle de vie SNS ajouté, Ubuntu 24.04 confirmé) ; tableau des changements 5.0 sourcé avec parties impactées ; marqueur « À vérifier » en texte et invite Kali simplifiée ; ressources du serveur ajoutées |
| 26/10/2026 | Format imposé par le professeur : **Markdown (.md)** ; chaîne Pandoc/PDF abandonnée |
| 27/10/2026 | Fusion avec la structure du dépôt `aminec58/wazuh-guide-fr` (public) : arborescence `docs/<NN>-<nom>/`, nommage des captures, noms d'hôtes et plages d'IDs du dépôt retenus ; hyperviseur VMware Workstation Pro ; niveaux T/C/P ; règles de publication (§8) ; nommage des sibling decoders corrigé d'après la doc officielle 4.14 |
