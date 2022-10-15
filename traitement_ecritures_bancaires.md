# Traitement des écritures bancaires

## Etapes
1. category_id ecriture -> /var/auto/categories_ecritures_bancaires.json -> /var/auto/scripts/setEcritureRemarquable.php
2. bank_description ecriture -> DB_USERS\users\user_bank_recurrence -> /var/auto/scripts/setEcritureRemarquable.php
3. amount ecriture -> DB_RAPPROCHEMENTS -> /var/auto/scripts/checkRapprochements.php -> DB_NOTIFY\user
4. La cloche de notification s'affiche sur l'application, donnant accès à la classe Notify.
5. La classe Notify liste les éléments récupérés dans DB_NOTIFY\user. Chaque élément de la liste est un lien vers une seconde interface
6. La seconde interface liste les choix de réponses (Donner une catégorie + ajout d'un justificatif + date du justificatif / dépense non déductible / Abonnement ou échéance (appel d'une trosième interface. 
7. affiche les types d'échéance, la date ...
8. Traite l'écriture avec les informations et ajoute l'élément dans user_bank_recurrence.
## Catégorisation

Les écritures bancaires sont récupérés depuis la table `DB_BANK\user` en filtrant les occurrences dont le champ `done` est _false_.

l'écriture récupérée intègre une variable `category_id` soumise au fichier /var/auto/categories_ecritures_bancaires.json. 
Ce fichier contient un tableau d'objet dont chaque objet est référencé par le même code que celui de BridgeAPi et contient tous les paramètres de traitement.

En cas de correspondance, le script `var\auto\scripts\setEcritureRemarquable.php est appelé. ce script génère une écriture bancaire ainsi qu'un fichier blank. ces éléments sont insérés respectivement dans `DB_ECRITURES\user` et `DB_JUSTIFICATIFS\user`.

En cas d'absence de correspondance, le libellé de l'écriture bancaire est soumis au fichier `user_bank_recurrence` stockés dans la table `DB_USERS\users`. 
Ce fichier est constitué progressivement par l'utilisateur. Il faut appel à la variable `bank_description` qui correspond au libellé banacire brut. 
On constitue une référence unique à partir de cette variable en supprimant les caractères numériques, les caractères spéciaux, les accents et les espaces. La variable est exprimée en minuscule. 

En cas de correspondance, le script `var\auto\scripts\setEcritureRemarquable.php est appelé. ce script génère une écriture bancaire ainsi qu'un fichier blank. ces éléments sont insérés respectivement dans `DB_ECRITURES\user` et `DB_JUSTIFICATIFS\user`.

En cas d'absence de correspondance, le montant de l'écriture est soumis à la table `DB_RAPPROCHEMENTS\user`. 

En cas de correspondance, lorsque le champ `non_comptabilise` de l'occurrence correspondante est _false_, on génère une écriture équilibrée en utilisant les informations de l'écriture correspondante, puis on supprime cette écriture.

En cas de correspondance, lorsque le champ `non_comptabilise` de l'occurrence correspondante est _true_, on génère la même écriture que précédent ainsi que l'écriture initiale et un fichier blank. 

ces éléments sont insérés respectivement dans les tables `DB_ECRITURES\user` et `DB_JUSTIFICATIFS\user`. 

En cas d'absence de correspondance, la ligne est insérée dans la table `DB_RAPPROCHEMENTS\user` avec le champ `non_comptabilise` à _true_. 

## Absence de rapprochement

La table `DB_RAPPROCHEMENTS\user` contient un champ `expire`. Lorsque ce champ est NULL, on lui donne la valeur 3. Il s'agit du nombre de jours au-delà duquel l'écriture sera considérée comme inconnue et devra être clarifiée par l'utilisateur. Ces écritures sont ajoutés à la table `DB_NOTIFY\user` par le script `/var/auto/scripts/checkRapprochement.php`. 

La cloche de notification s'affiche sur l'application lorque la table `DB_NOTIFY\user` contient des données dont le champ `done` est false. 
Ces données 





[
  {
  "id":"79":
  "label":"Frais bancaires",
  "journalCode":"OD",
  "JournalLib":"Opérations diverses",
  "compte":[
    {
      "CompteNum":"661",
      "CompteLib"":"Frais bancaires",
      "EcritureLib":'Frais bancaires",
      "Sens":"debit",
      "fixe_a_deduire":0,
      "pourcentage_a_calculer":0
    },{
      "CompteNum":"512",
      "CompteLib"":"Banque",
      "EcritureLib":'Frais bancaires",
      "Sens":"credit",
      "fixe_a_deduire":0,
      "pourcentage_a_calculer":0
    }
  ]
 },
  {
  "id":"306":
  "label":"Services Bancaires",
  "journalCode":"OD",
  "JournalLib":"Opérations diverses",
  "compte":[
    {
      "CompteNum":"627",
      "CompteLib"":"Frais bancaires",
      "EcritureLib":'Frais bancaires",
      "Sens":"debit",
      "fixe_a_deduire":0,
      "pourcentage_a_calculer":0
    },{
      "CompteNum":"512",
      "CompteLib"":"Banque",
      "EcritureLib":'Frais bancaires",
      "Sens":"credit",
      "fixe_a_deduire":0,
      "pourcentage_a_calculer":0
    }
  ]
 },
  
  
  306 Services Bancaires
        756587  Incidents de paiement
        78  Virements
        85  Retraits
        88  Chèques
        326 Virements internes


### COMPRENDRE LE TRAITEMENT DES ECRITURES COMPTABLES

Lors de la configuration de votre compte, vous avez autorisé notre prestataire Bridge API à collecter les écritures bancaires de vos comptes professionnels et à nous les transmettre. Nous sollicition ce partenaire une fois par jour au moyen d'un script autonome pour obtenir vos nouvelles écritures. celles-ci sont sockées dans une base de données qui vous est dédiée. Chaque jour, un autre script autonome récupère les écritures nouvelles et tente de les faire correspondre. les écritures de retrait ou de dépôt d'espèces ainsi que les écriture de services et de charges bancaires ou encore les erreurs bancaires et autres types d'écritures sont reconnues et comptabilsiées sans intervention humaine.  Les écritures que vous aviez auparavent définies comme récurrentes (abonnements, échéances, loyers etc.) sont reconnues et traitées sans intervention humaine, sauf dans le cas où vous aveiez préciser vouloir les confirmer vous-même. Les autres écritures sont confrontées à vos ssaisies deen attente ou à des écritures qui 

