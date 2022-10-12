 # Serveur CRON
 
 Le serveur CRON est connecté au réseau internet par l'adresse IP `212.227.160.66`. Il contient les dossiers `/var/auto` et `var/www/html/endpoints`. 
 Le dossier `/var/auto` contient un ensemble de fichiers et de scripts exécuté périodiquement par le logiciel crontab.
 Le dossier `/var/www/html/endpoints` contient un ensemble de scripts appelés par une URI propre dont l'URL est https://endpoints.cron8817.online/. 
 
 ## Tâches autonomes `/var/auto`
 
 1. ### Collecte des écritures bancaires
 
 Le script /var/auto/getbankstatement/ est exécuté quotidiennement. Il appelle l'API de BridgeIO pour chaque identifiant de chaque utilisateur 
 et reçoit les écritures bancaires nouvelles en retour. Ces écritures sont conservées dans la table dédié de DB_BANK. 
 
 2. ### Traitement des écritures bancaires

Le script `/var/auto/usebankstatement/` est exécuté quotidiennement. 

Le montant de chaque écriture bancaire est soumis à la table `DB_RAPPROCHEMENTS\user`. Lorsqu'il existe une correspondance, 
l'écriture comptable est générée et insérée dans la table `DB_ECRITURES\user`. L'écriture correspondante est supprimée de la table `DB_RAPPROCHEMENTS\user`. 
Dans le cas contraire, elle est soumise au fichier JSON `ecritures_periodiques` qui liste les écritures dont
le rapprochement ne s'effectue pas par la contrepartie d'une saisie utilisateur (ex: prélèvement d'assurance, d'emprunt, etc.). 
****Lorsqu'il existe une correspondance****, le script appelle un script spécifique contenu dans le dossier `/var/auto/scripts` qui va générer 
l'écriture comptable automatiquement en fonction des critères dont il dispose. **lorsqu'il n'existe pas de correspondance**, 
l'écriture est insérée dans la table de rapprochement avec la mention "non comptabilisee". Elle sera ensuite traitée par 
le script de traitement des écritures en attente de rapprochement.

3. ### Traitement des écritures en attente de rapprochement

Le script `/var/auto/checkrapprochement/` est exécuté quotidiennement. Il compte les écritures de la table `DB_RAPPROCHEMENTS\user` 
en filtrant uniquement les écritures dont le nombre de jours écoulés depuis la date d'insertion est supérieur à la valeur numérique du champ `expire`. 
Lorsque la date du champ `expire` est `NULL`, la valeur numérique par défaut est 3. 
Lorsque le décompte est positif, il insère la notification suivante dans la table `DB_NOTIFY\user` :

``
{"titre":"Ecritures bancaires non identifiées",
"sujet":"Des écritures bancaires sont en attente d'authentification et nécessite certaines précisions.",
"class":"Notify",
"function":"SetUnknownData",
"param":{
  "dateEcriture":"dd/mm/yyyy"


}
``
 
