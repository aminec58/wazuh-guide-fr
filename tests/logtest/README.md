# Tests wazuh-logtest

Un fichier par source. Chaque test associe un log en entrée au résultat attendu.

Format :

```
### <nom du test>
Log :
<ligne de log brute, anonymisée>

Attendu :
- decoder.name : 
- rule.id      : 
- rule.level   : 
```

Exécution sur le manager :

```bash
sudo /var/ossec/bin/wazuh-logtest
```
