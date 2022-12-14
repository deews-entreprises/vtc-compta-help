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

Le montant de chaque écriture bancaire est soumis à la table `DB_RAPPROCHEMENTS\user`. 

- Lorsqu'il existe une correspondance, l'écriture comptable est générée et insérée dans la table `DB_ECRITURES\user`. L'écriture correspondante est supprimée de la table `DB_RAPPROCHEMENTS\user`. Lorsque l'écriture correspondante contient la mention "non comptabilisée", elle est également insérée dans la table SQL `DB_ECRITURES\user`. Un justificatif `blank` est généré sans empreinte numérique `dPrint`.

- Dans le cas contraire, l'écriture bancaire est soumise au fichier JSON `ecritures_periodiques` qui liste les écritures dont
le rapprochement ne s'effectue pas par la contrepartie d'une saisie utilisateur (ex: prélèvement d'assurance, d'emprunt, etc.).

```
{ 
 "ref":"",
 "label_original":"",
 "label_brut:"", // (label original en minuscule, sans ponctuation ni caractères spéciaux ni espaces),
 "categorie":"",
 "script":"",
 "
}
```

****Lorsqu'il existe une correspondance****, le script `/var/auto/usebankstatement/` appelle le script correspondant dans le dossier `/var/auto/scripts`. Ces scripts sont généralisés aux écritures remarquables qui sont :

 - prélèvement d'emprunt (débit)
 - prélèvement d'assurance (débit)
 - prélevement de télépéage (débit)
 - prélevement de carte bancaire (débit




****lorsqu'il n'existe pas de correspondance****, l'écriture est insérée dans la table SQL `DB_RAPPROCHEMENTS\user` avec la mention "non comptabilisee". Elle sera ensuite traitée par le script de traitement des écritures en attente de rapprochement.

3. ### Traitement des écritures en attente de rapprochement

Le script `/var/auto/checkrapprochement/` est exécuté quotidiennement. Il compte les écritures de la table `DB_RAPPROCHEMENTS\user` 
en filtrant uniquement les écritures dont le nombre de jours écoulés depuis la date d'insertion est supérieur à la valeur numérique du champ `expire`. 
Lorsque la date du champ `expire` est `NULL`, la valeur numérique par défaut est 3. 
Lorsque le décompte est positif, il insère la notification suivante dans la table `DB_NOTIFY\user` :

```

{"titre":"Ecritures bancaires non identifiées",
"sujet":"Des écritures bancaires sont en attente d'authentification et nécessite certaines précisions.",
"class":"Notify",
"function":"SetUnknownData",
"param":{
"ecriture":
  {
   "date":"",
   "label":"",
   "montant":abs(0),
   "operation""; // `débit` ou `credit`
   "origine":"",
  }
 }

```

__La fonction SetUnknownData()__


Cette fonction est une composante de la classe `Modify`. l'interface affiche le titre et le sujet et propose un liste d'options établie en fontion de param.ecriture.operation. Lorsque ce paramètre est `debit`, les options sont 
"une charge d'exploitation ou une acquisition d'immobilisation", => appelle la classe Depense()
"une dépense sans rapport avec les besoins de l'entreprise", Appelle la classe Depenses()
"une dépense enregistrée avec un mode de réglement érroné", => appelle la classe Extourne()
"Le versement d'un dépôt de garantie", => appelle la classe Caution()
"un prélèvement périodique",
"je ne sais pas".  

Lorsque la dépense est un prélèvement périodique, on affiche une liste pour préciser :
"Prime d'assurance",
"Echeance d'emprunt",
{Liste des cartes déclarées}
"Carte bancaire à débit différé" (si déclaré dans les instruments)
"Carte carburant à débit différé", (si déclaré dans les instruments
"Carte de crédit" (si déclaré dans les emprunts en type renouvellable),
"Prélèvement télépéage",
"Autre prélèvement"








 
 
 
