 # Serveur CRON
 
 Le serveur CRON est connecté au réseau internet par l'adresse IP `212.227.160.66`. Il contient les dossiers `/var/auto` et `var/www/html/endpoints`. 
 Le dossier `/var/auto` contient un ensemble de fichiers et de scripts exécutés périodiquement par le daemon `crontab`.
 Le dossier `/var/www/html/endpoints` contient un ensemble de scripts appelés depuis l'URL https://endpoints.cron8817.online/. 
 
 ## Tâches autonomes `/var/auto`
 
 1. ### Collecte des écritures bancaires
 
 Le script /var/auto/getbankstatement/ est exécuté quotidiennement. Il appelle l'API de BridgeIO pour chaque identifiant de chaque utilisateur 
 et reçoit les écritures bancaires nouvelles en retour. Ces écritures sont conservées dans la table `DB_BANK\user`. 
 
 2. ### Traitement des écritures bancaires

Le script `/var/auto/usebankstatement/` est exécuté quotidiennement. 

Chaque écriture est soumise à la tabke `DB_RAPPROCHEMENTS\user` par leur montant. En cas de correspondance,

- l'écriture comptable est générée et insérée dans la table `DB_ECRITURES\user`
- dans le cas où l'écriture correspondante contient la mention "non comptabilisé"
    - elle est également ajoutée à la table `DB_RAPPROCHEMENTS\user`
    - Un justificatif blank est ajouté à `DB_JUSTIFICATIFS\user`.
- L'écriture correspondante est supprimée de la table DB_RAPPROCHEMENTS\users`.
  
 Lorsqu'il n'existe pas de correspondance avec la table `DB_RAPPROCHEMENTS\user`, le libellé de l'écriture est soumise au fichier `user_data` 
  afin de vérifier une co
  
  
  
> Le fichier `user_bank_recurrence` est stocké dans la table `user_data` et s'enrichie par la saisie utilisateur. le libellé est converti en minuscule en un string privé 
  d'accents, de caractères spéciaux, de ponctuations, de chiffres et d'espaces. il est soumis à aux écritures brutes du même type.
  
En cas de correspondance entre le libellé bancaire et un élément du fichier `user_bank_recurrence`, le fichier appelle 
