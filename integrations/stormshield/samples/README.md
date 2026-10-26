# Échantillons de logs Stormshield SNS

Un fichier par type de log (`filter.log`, `alarm.log`, `connection.log`, `auth.log`, `vpn.log`…).

**Anonymisation obligatoire avant commit :**
- IP publiques et internes → plages de documentation (192.0.2.0/24, 198.51.100.0/24, 203.0.113.0/24)
- Noms d'utilisateurs et de machines → valeurs fictives
- Numéro de série du firewall (champ `fw`) → `SNXXXXXXXXXXXXX`
