# Traitement des écritures bancaires

# Catégorisation

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

# Absence de rapprochement

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
