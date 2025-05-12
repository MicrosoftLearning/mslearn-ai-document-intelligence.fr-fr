---
lab:
  title: "Analyser du contenu avec Compréhension de contenu Azure\_AI"
  module: Multimodal analysis with Content Understanding
---

# Analyser du contenu avec Compréhension de contenu Azure AI

Dans cet exercice, vous utilisez le portail Azure AI Foundry pour créer un projet Compréhension de contenu qui peut extraire des informations à partir de factures. Vous allez ensuite tester votre analyseur de contenu dans le portail Azure AI Foundry et l’utiliser via l’interface REST Compréhension de contenu.

Cet exercice prend environ **30** minutes.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](../media/ai-foundry-portal.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, saisissez un nom valide pour votre projet et, si un hub existant est suggéré, choisissez l’option permettant d’en créer un nouveau. Passez ensuite en revue les ressources Azure qui seront créées automatiquement pour prendre en charge votre hub et votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *un nom valide pour votre hub*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez ou sélectionnez un groupe de ressources*
    - **Emplacement** : sélectionnez l’une des régions suivantes\*
        - USA Ouest
        - Suède Centre
        - Australie Est
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une nouvelle ressource AI Services*
    - **Connecter Recherche Azure AI** : *créer une ressource Recherche Azure AI avec un nom unique*

    > \*Au moment de l’écriture, la compréhension du contenu d’IA Azure n’est disponible que dans ces régions.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](../media/ai-foundry-project.png)

## Créer un analyseur Compréhension de contenu

Vous allez générer un analyseur qui peut extraire des informations de factures. Vous commencerez par définir un schéma basé sur un exemple de facture.

1. Dans un nouvel onglet de navigateur, téléchargez le formulaire d’exemple [invoice-1234.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf) depuis `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf` et enregistrez-le dans un dossier local.
1. Revenez à l’onglet contenant la page d’accueil de votre projet Azure AI Foundry, puis, dans le volet de navigation gauche, sélectionnez **Compréhension de contenu**.
1. Sur la page **Compréhension de contenu**, sélectionnez l’onglet **Analyseur personnalisé** en haut de la page.
1. Sur la page de l’analyseur personnalisé Compréhension de contenu, sélectionnez **+ Créer**, puis créez une tâche avec les paramètres suivants :
    - **Nom de la tâche** : analyse de facture
    - **Description** : extraire les données d’une facture
    - **Connexion aux services Azure AI** : *la ressource Azure AI Services de votre hub Azure AI Foundry*
    - **Compte de stockage Azure Blob** : *le compte de stockage par défaut de votre hub Azure AI Foundry*
1. Attendez que la tâche soit créée.

    > **Conseil** : en cas d’erreur d’accès au stockage, attendez une minute, puis réessayez.

1. Sur la page **Définir le schéma**, chargez le fichier **invoice-1234.pdf** que vous venez de télécharger.
1. Sélectionnez le modèle d’**analyse de facture**, puis sélectionnez **Créer**.

    Le modèle d’*analyse de facture* inclut les champs les plus courants que l’on trouve dans les factures. Vous pouvez utiliser l’éditeur de schéma pour supprimer les champs suggérés qui ne sont pas nécessaires et ajouter ceux dont vous avez besoin.

1. Dans la liste des champs suggérés, sélectionnez **BillingAddress**. Ce champ ne correspond pas au format de facture que vous avez téléversé : utilisez l’icône **Supprimer le champ** (**&#128465;**) qui s’affiche pour le supprimer.
1. Supprimez maintenant les champs suggérés suivants, qui ne sont pas nécessaires :
    - BillingAddressRecipient
    - CustomerAddressRecipient
    - CustomerId
    - CustomerTaxId
    - DueDate
    - InvoiceTotal
    - PaymentTerm
    - PreviousUnpaidBalance
    - PurchaseOrder
    - RemittanceAddress
    - RemittanceAddressRecipient
    - ServiceAddress
    - ServiceAddressRecipient
    - ShippingAddress
    - ShippingAddressRecipient
    - TotalDiscount
    - VendorAddressRecipient
    - VendorTaxId
    - TaxDetails
1. Utilisez le bouton **+ Ajouter un champ** pour ajouter les champs suivants :

    | Nom du champ | Description du champ | Type de valeur | Method |
    |--|--|--|--|
    | `VendorPhone` | `Vendor telephone number` | Chaîne | Extraire |
    | `ShippingFee` | `Fee for shipping` | Number | Extraire |

1. Vérifiez que votre schéma final correspond à celui-ci, puis sélectionnez **Enregistrer**.
    ![Capture d’écran d’un schéma pour une facture.](../media/invoice-schema.png)

1. Dans la page **Analyseur de test**, si l’analyse ne démarre pas automatiquement, sélectionnez **Exécuter l’analyse**. Attendez ensuite que l’analyse soit terminée, puis examinez les valeurs textuelles extraites de la facture et identifiées comme correspondant aux champs du schéma.
1. Passez en revue les résultats de l’analyse, qui devraient ressembler à ce qui suit :

    ![Capture d’écran des résultats des tests de l’analyseur.](../media/analysis-results.png)

1. Affichez les détails des champs identifiés dans le volet **Champs**, puis accédez à l’onglet **Résultat** pour consulter la représentation JSON.

## Générer et tester un analyseur

Maintenant que vous avez entraîné un modèle à extraire des champs à partir de factures, vous pouvez créer un analyseur à utiliser avec des formulaires similaires.

1. Accédez à la page **Générer un analyseur**, puis sélectionnez **+ Générer un analyseur** et générez un nouvel analyseur avec les propriétés suivantes (saisies exactement comme indiquées ici) :
    - **Nom :** `contoso-invoice-analyzer`
    - **Description** : `Contoso invoice analyzer`
1. Attendez que l’analyseur soit prêt (utilisez le bouton **Actualiser** pour vérifier).
1. Téléchargez le fichier [invoice-1235.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf) depuis `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf` et enregistrez-le dans un dossier local.
1. Revenez à la page **Générer un analyseur** et sélectionnez le lien **contoso-invoice-analyzer**. Les champs définis dans le schéma de l’analyseur s’affichent.
1. Sur la page **contoso-invoice-analyzer**, sélectionnez l’onglet **Tester**.
1. Utilisez le bouton **+ Importer des fichiers de test** pour charger **invoice-1235.pdf**, puis lancez l’analyse afin d’extraire les données de champs à partir du formulaire de test.
1. Passez en revue les résultats du test et vérifiez que l’analyseur a bien extrait les champs attendus de la facture de test.
1. Fermez la page **contoso-invoice-analyzer***.

## Utiliser l’API REST Compréhension de contenu

Maintenant que vous avez créé un analyseur, vous pouvez l’utiliser à partir d’une application cliente via l’API REST Compréhension de contenu.

1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

    Fermez les notifications de bienvenue pour afficher la page d’accueil du portail Azure.

1. Utilisez le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell*** avec aucun stockage dans votre abonnement.

    Cloud Shell fournit une interface de ligne de commande via un volet situé en bas du portail Azure. Vous pouvez redimensionner ou agrandir ce volet pour faciliter le travail.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    **<font color="red">Assurez-vous d’avoir basculé vers la version classique du Cloud Shell avant de continuer.</font>**

1. Dans le volet Cloud Shell, saisissez les commandes suivantes pour cloner le référentiel GitHub contenant les fichiers de code pour cet exercice (saisissez la commande, ou copiez-la dans le presse-papiers puis effectuez un clic droit dans la ligne de commande pour la coller en texte brut) :

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

    > **Conseil** : lorsque vous saisissez des commandes dans le Cloud Shell, la sortie peut occuper une grande partie de la mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

1. Une fois le dépôt cloné, accédez au dossier contenant les fichiers de code de votre application :

    ```
   cd mslearn-ai-doc/Labfiles/05-content-understanding/code
   ls -a -l
    ```

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous allez utiliser :

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install dotenv azure-identity azure-ai-projects
    ```

1. Saisissez la commande suivante pour modifier le fichier de configuration fourni :

    ```
   code .env
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Dans le fichier de code, remplacez l’espace réservé **YOUR_PROJECT_CONNECTION_STRING** par la chaîne de connexion de votre projet (copiée depuis la page **Vue d’ensemble** du portail Azure AI Foundry), et assurez-vous que la valeur de **ANALYSEUR** correspond au nom que vous avez attribué à votre analyseur (qui devrait être *contoso-invoice-analyzer*).
1. Une fois les espaces réservés remplacés, utilisez la commande **CTRL+S** dans l’éditeur de code pour enregistrer vos modifications, puis la commande **CTRL+Q** pour fermer l’éditeur tout en gardant la ligne de commande Cloud Shell ouverte.

1. Dans la ligne de commande Cloud Shell, saisissez la commande suivante pour modifier le fichier Python **analyze_invoice.py** qui vous a été fourni :

    ```
    code analyze_invoice.py
    ```
    Le fichier de code Python est ouvert dans un éditeur de code :

1. Passez en revue le code, qui :
    - Identifie le fichier de facture à analyser, avec **invoice-1236.pdf** comme valeur par défaut.
    - Récupère le point de terminaison et la clé de votre ressource Azure AI Services à partir du projet.
    - Envoie une requête HTTP POST à votre point de terminaison Compréhension de contenu, demandant l’analyse de l’image.
    - Vérifie la réponse de l’opération POST pour récupérer un ID pour l’opération d’analyse.
    - Envoie à plusieurs reprises une requête HTTP GET à votre service Compréhension de contenu jusqu’à ce que l’opération ne s’exécute plus.
    - Si l’opération a réussi, le programme analyse la réponse JSON et affiche les valeurs extraites.
1. Utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.
1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour exécuter le code Python :

    ```
    python analyze_invoice.py invoice-1236.pdf
    ```

1. Passez en revue la sortie générée par le programme.
1. Utilisez la commande suivante pour exécuter le programme avec une autre facture :

    ```
    python analyze_invoice.py invoice-1235.pdf
    ```

    > **Conseil** : trois fichiers de facture sont disponibles dans le dossier du code : invoice-1234.pdf, invoice-1235.pdf et invoice-1236.pdf. 

## Nettoyage

Si vous avez terminé d’utiliser le service Compréhension de contenu, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Dans le portail Azure AI Foundry, accédez au projet **travel-insurance** et supprimez-le.
1. Dans le portail Azure, supprimez le groupe de ressources que vous avez créé pour cet exercice.

