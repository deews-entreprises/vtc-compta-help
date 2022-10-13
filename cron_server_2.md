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
  
 Lorsqu'il n'existe pas de correspondance avec la table `DB_RAPPROCHEMENTS\user`, le libellé de l'écriture est soumise au fichier à certains éléments 
 du fichier `user_data`. Ces éléments sont 
 - Instruments
 - Emprunts
 - Assurance
 - Télépéage
 - Abonnements à échéance fixe


(écriture au débit)
Le fichier user_bank_recurrence récupère la date d'échéance ou de facturation ainsi que le compte comptable correspondant afin de générer l'écriture de la facturation ou de l'échéance et celle du paiement. s'agissant du justificatif, soit il a déjà été fourni et son numéro est récupéré; soit il s'agit d'une facturation mensuelle et on génère un fichier blank pour lequel l'utilisateur devra insérer la facture. 

L'interface NOTIFY affiche une liste de choix dont Saisir la dépense, je ne sais pas, échéance récurrence. 
Si dépense, on demande la catégorie 



 (c) assurance
 (d) télépéage
 (e) loyers
 (
  
  
  
> Le fichier `user_bank_recurrence` est stocké dans la table `user_data` et s'enrichie par la saisie utilisateur. le libellé est converti en minuscule en un string privé 
  d'accents, de caractères spéciaux, de ponctuations, de chiffres et d'espaces. il est soumis à aux écritures brutes du même type.
  
En cas de correspondance entre le libellé bancaire et un élément du fichier `user_bank_recurrence`, le fichier appelle 

### v2

- Le montant de l'écriture bancaire est soumis à la table de rapprochement.
- en cas de correspondance, on génère l'écriture comptable et on l'insère dans DB_ECRITURES. Lorsque l'écriture de rapprochement contient la mention "non-comptabilise", elle est comptabilisé et un justificatif blank est généré.
- en cas d'échec, on soumet l'écriture à la table user_bank_recurrence. Le libellé est compressé. 
- En cas de correspondance, on génère l'écriture par l'appel du script correspondant
- En cas d'echec, on ajoute l'écriture à la table DB_RAPPROCHEMENTS avec la mention "non-comptabilise"

- dans DB_RAPPROCHEMENT, les éléments expirés sont récupérés et ajoutés à DB_NOTIFY; 
- l'utilisateur accède à la liste des éléments 




