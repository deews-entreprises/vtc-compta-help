les écritures bancaires à traiter sont celles de la table DB_BANK\user dont la valeur done est `false`. 

1. Par le libellé et l'origine, lorsque l'écriture bancaire est un mouvement d'espèces, on effectue une opération 512/53. 

2. Dans le cas contraire, le montant de l'écriture est soumis à la table rapprochement. 

- En cas de correspondance on vérifie la mention "non_comptabilise".

  - Si elle est `false`, on génère l'écriture comptable et on génère un lettrage commun aux écritures comptables antérieures ayant le même numéro de pièce. 
  - Si elle est `true`, on génère les deux écritures avec un lettrage et on créé une pièce justificative blank.
 
 Dans tous les cas, on supprime la ligne.

- En absence de correspondance, on soumet l'écriture au fichier `user_bank_recurrence`. ce fichier est un tableau d'objet. Chaque objet contient les informations 
nécessaires pour générer une écriture compteble et son écriture de rapprochement, et de les comptabliser. 
Les informations contenues dans chaque objet sont les suivantes :

```
  {
    "ref":"Le libellé brut, c'est à dire en minuscule, sans ponctuation, caractères spéciaux, accents et espace",
    "type_element":"Facturation / Abonnement / Echéancier",
    "categorie":"Assurance / Emprunt / Télépéage / Autre /",
    "date_due":"seulement si type === Facturation / date usuelle de la facturation",
    "PieceRef":"(sauf facturation) : Lorsqu'il s'agit d'un échéancier, l'utilisateur télécharge le document et sa référence est conservée.",
    "libelle":"Nom donné par l'utilisateur",
    "ask_for_check":"`true` indique que l'écriture doit être soumise à l'utilisateur avant d'être validée.",
    "comptes":[
      {
        "CompteNum":"",
        "CompteLib":"",
        "fixe_to_deduce":0,
        "percent_to_calculate":0.000
       },
       ...
     ]

```
