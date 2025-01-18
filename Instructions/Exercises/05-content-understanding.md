---
lab:
  title: "Analyser du contenu avec Compréhension de contenu Azure\_AI"
  module: Multimodal analysis with Content Understanding
---

# Analyser du contenu avec Compréhension de contenu Azure AI

Dans cet exercice, vous utilisez le portail Azure AI Foundry pour créer un projet Compréhension de contenu qui peut extraire des informations à partir de formulaires de police d’assurance voyage. Vous allez ensuite tester votre analyseur de contenu dans le portail Azure AI Foundry et l’utiliser via l’interface REST Compréhension de contenu.

Cet exercice prend environ **30** minutes.

## Créer un projet Compréhension de contenu

Commençons par utiliser le portail Azure AI Foundry pour créer un projet Compréhension de contenu.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

    La page d’accueil du portail Azure AI Foundry ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](../media/ai-foundry-portal.png)

1. Dans la section **Rechercher rapidement** de la page d’accueil, vers le bas, sélectionnez **Compréhension de contenu**.
1. Dans la page **Compréhension de contenu**, sélectionnez le bouton **Créer un projet Compréhension de contenu**.
1. À l’étape **Vue d’ensemble du projet**, définissez les propriétés suivantes pour votre projet, puis sélectionnez **Suivant** :
    - **Nom du projet** : `travel-insurance`
    - **Description** : `Insurance policy data extraction`
    - **Hub** : créez un hub.
1. Dans l’étape **Créer un hub**, définissez les propriétés suivantes, puis sélectionnez **Suivant** :
    - **Ressource hub Azure AI** : `content-understanding-hub`
    - **Abonnement Azure** : *sélectionnez votre abonnement Azure.*
    - **Groupe de ressources** : *créez un groupe de ressources avec un nom approprié*.
    - **Emplacement** : *Sélectionnez un des emplacements disponibles*
    - **Azure AI Services** : *créez une ressource Azure AI Services avec un nom approprié.*
1. À l’étape **Paramètres de stockage**, spécifiez un nouveau compte de stockage Hub IA, puis sélectionnez **Suivant**.
1. Sur la page **Vérifier**, sélectionnez **Créer un projet**. Attendez ensuite que le projet et ses ressources associées soient créées.

    Une fois le projet prêt, il s’ouvre dans la page **Définir le schéma**.

    ![Capture d’écran d’un nouveau projet Compréhension de contenu.](../media/content-understanding-project.png)

## Vérifier les ressources Azure

Lorsque vous avez créé le hub IA et le projet, différentes ressources ont été créées dans votre abonnement Azure pour prendre en charge le projet.

1. Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Accédez au groupe de ressources que vous avez créé pour votre hub et notez les ressources Azure qui ont été créées.

    ![Capture d’écran des ressources Azure.](../media/azure-resources.png)

## Définir un schéma personnalisé

Vous allez créer un analyseur qui peut extraire des informations des formulaires d’assurance voyage. Vous commencerez par définir un schéma basé sur un exemple de formulaire.

1. Téléchargez l’exemple de formulaire [train-form.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/train-form.pdf) depuis `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/train-form.pdf` et enregistrez-le dans un dossier local.
1. Revenez à l’onglet du navigateur contenant votre projet Compréhension de contenu et, dans la page **Définir le schéma**, chargez le fichier **train-form.pdf** que vous venez de télécharger.
1. Sélectionnez le modèle d’**analyse de document**, puis sélectionnez **Créer**.

    L’éditeur de schéma permet de définir les champs de données à extraire du formulaire, qui s’affiche à droite. Le formulaire ressemble à ceci :

    ![Capture d’écran d’un exemple de formulaire d’assurance.](../media/train-form.png)

    Les champs de données du formulaire se composent des éléments suivants :
    
    - Collection d’informations personnelles relatives au titulaire de police
    - Collection d’informations relatives au voyage pour lequel l’assurance est requise
    - Signature et date

    Nous allons commencer par ajouter un champ qui représente les informations personnelles sous la forme d’un tableau, dans lequel nous allons ensuite définir des sous-champs pour les détails individuels.

1. Sélectionnez **+Ajouter un champ** pour créer un champ avec les valeurs suivantes :
    - **Nom du champ** : `PersonalDetails`
    - **Description du champ** : `Policyholder information`
    - **Type de valeur** : tableau
1. Sélectionnez **Enregistrer les modifications** (&#10004;) et notez qu’un sous-champ est créé automatiquement.
1. Configurez le nouveau sous-champs avec les valeurs suivantes :
    - **Nom du champ** : `PolicyholderName`
    - **Description du champ** : `Policyholder name`
    - **Type de valeur** : chaîne
    - **Méthode** : extraire
1. Utilisez le bouton **+ Ajouter un sous-champ** pour ajouter les sous-champs supplémentaires suivants :

    | Nom du champ | Description du champ | Type de valeur | Method |
    |--|--|--|--|
    | `StreetAddress` | `Policyholder address` | Chaîne | Extract |
    | `City` | `Policyholder city` | Chaîne | Extract |
    | `PostalCode` | `Policyholder post code` | Chaîne | Extract |
    | `CountryRegion` | `Policyholder country or region` | Chaîne | Extract |
    | `DateOfBirth` | `Policyholder birth date` | Date | Extract |

1. Lorsque vous avez ajouté tous les sous-champs d’informations personnelles, utilisez le bouton **Précédent** pour revenir au niveau supérieur du schéma.
1. Ajoutez un *champ de tableau* nommé **`TripDetails`** pour représenter les détails du voyage assuré. Ajoutez-y ensuite les sous-champs suivants :

    | Nom du champ | Description du champ | Type de valeur | Method |
    |--|--|--|--|
    | `DestinationCity` | `Trip city` | Chaîne | Extract |
    | `DestinationCountry` | `Trip country or region` | Chaîne | Extract |
    | `DepartureDate` | `Date of departure` | Date | Extract |
    | `ReturnDate` | `Date of return` | Date | Extract |

1. Revenez au niveau supérieur du schéma et ajoutez les deux champs individuels suivants :

    | Nom du champ | Description du champ | Type de valeur | Method |
    |--|--|--|--|
    | `Signature` | `Policyholder signature` | Chaîne | Extract |
    | `Date` | `Date of signature` | Date | Extract |

1. Vérifiez que votre schéma terminé ressemble à ceci, puis enregistrez-le.

    ![Capture d’écran du schéma d’un document.](../media/completed-schema.png)

1. Dans la page **Analyseur de test**, si l’analyse ne démarre pas automatiquement, sélectionnez **Exécuter l’analyse**. Attendez ensuite que l’analyse se termine et examinez les valeurs de texte du formulaire identifiées comme correspondant aux champs du schéma.

    ![Capture d’écran des résultats des tests de l’analyseur.](../media/test-analyzer.png)

    Le service Compréhension de contenu doit avoir correctement identifié le texte correspondant aux champs du schéma. Si ce n’était pas le cas, vous pouvez utiliser la page **Étiqueter les données** pour charger un autre exemple de formulaire et identifier explicitement le texte correct pour chaque champ.

## Générer et tester un analyseur

Maintenant que vous avez entraîné un modèle pour extraire des champs des formulaires d’assurance, vous pouvez créer un analyseur à utiliser avec des formulaires similaires.

1. Dans le volet de navigation de gauche, sélectionnez la page **Créer un analyseur**.
1. Sélectionnez **+ Créer un analyseur** et créez un analyseur avec les propriétés suivantes (tapées exactement comme indiqué ici) :
    - **Nom :** `travel-insurance-analyzer`
    - **Description** : `Insurance form analyzer`
1. Attendez que l’analyseur soit prêt (utilisez le bouton **Actualiser** pour vérifier).
1. Téléchargez le fichier [test-form.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/test-form.pdf) depuis `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/test-form.pdf` et enregistrez-le dans un dossier local.
1. Revenez à la page **Créer un analyseur** et sélectionnez le lien **travel-insurance-analyzer**. Les champs définis dans le schéma de l’analyseur s’affichent.
1. Dans la page **travel-insurance-analyzer**, sélectionnez **Tester**.
1. Utilisez le bouton **+ Charger des fichiers de test** pour charger le fichier **test-form.pdf** et exécutez l’analyse pour extraire les données de champ du formulaire de test.

    ![Capture d’écran des résultats de l’analyse de formulaire de test.](../media/test-form-results.png)

1. Affichez l’onglet **Résultats** pour afficher les résultats au format JSON retournés par l’analyseur. Dans la tâche suivante, vous allez utiliser l’API REST Compréhension de contenu pour envoyer un formulaire à votre analyseur et retourner les résultats dans ce format.
1. Fermez la page **travel-insurance-analyzer**.

## Utiliser l’API REST Compréhension de contenu

Maintenant que vous avez créé un analyseur, vous pouvez l’utiliser à partir d’une application cliente via l’API REST Compréhension de contenu.

1. Basculez vers l’onglet du navigateur contenant le portail Azure (ou ouvrez `https://portal.azure.com` dans un nouvel onglet si vous l’avez fermé).
1. Dans le groupe de ressources de votre hub Compréhension de contenu, ouvrez la ressource **Azure AI Services**.
1. Dans la page **Vue d’ensemble**, dans la section **Clés et point de terminaison**, affichez l’onglet **Compréhension de contenu**.

    ![Capture d’écran des clés et du point de terminaison de Compréhension de contenu.](../media/keys-and-endpoint.png)

    Vous aurez besoin du point de terminaison Compréhension de contenu et de l’une des clés pour vous connecter à votre analyseur à partir d’une application cliente.

1. Cliquez sur le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell***. Cloud Shell fournit une interface de ligne de commande dans un volet situé en bas du portail Azure, comme illustré ici :

    ![Capture d’écran du portail Azure avec un volet Cloud Shell.](../media/cloud-shell.png)

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Notez que vous pouvez redimensionner Cloud Shell en faisant glisser la barre de séparation en haut du volet. Vous pouvez aussi utiliser les icônes **&#8212;**, **&#10530;** et **X** situées en haut à droite du volet pour réduire, agrandir et fermer ce dernier. Pour plus d’informations sur l’utilisation d’Azure Cloud Shell, consultez la [documentation Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).
1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

1. Dans le volet PowerShell, entrez les commandes suivantes pour cloner le référentiel GitHub pour cet exercice :

    ```
    rm -r mslearn-ai-doc -f
    git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

1. Une fois le référentiel cloné, accédez au dossier **mslearn-ai-doc/Labfiles/05-content-understanding/code** :

    ```
    cd mslearn-ai-doc/Labfiles/05-content-understanding/code
    ```

1. Entrez la commande suivante pour modifier le fichier de code Python **analyze_doc.py** fourni :

    ```
    code analyze_doc.py
    ```
    Le fichier de code Python est ouvert dans un éditeur de code :

    ![Capture d’écran d’un éditeur de code avec du code Python.](../media/code-editor.png)

1. Dans le fichier de code, remplacez l’espace réservé **\<CONTENT_UNDERSTANDING_ENDPOINT\>** par votre point de terminaison Compréhension de contenu et l’espace réservé **\<CONTENT_UNDERSTANDING_KEY\>** par l’une des clés de votre ressource Azure AI Services.

    > **Conseil** : vous devez redimensionner ou réduire la fenêtre Cloud Shell pour copier le point de terminaison et la clé de la page de ressources Azure AI Services dans le portail Azure. Veillez à *ne pas fermer* Cloud Shell (ou vous devrez répéter les étapes ci-dessus).

1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **Ctrl+S** pour enregistrer vos modifications, puis passez en revue le code terminé, qui :
    - Envoie une requête HTTP POST à votre point de terminaison Compréhension de contenu, en demandant à **travel-insurance-analyzer** d’analyser un formulaire en fonction de son URL.
    - Vérifie la réponse de l’opération POST pour récupérer un ID pour l’opération d’analyse.
    - Envoie à plusieurs reprises une requête HTTP GET à votre service Compréhension de contenu jusqu’à ce que l’opération ne s’exécute plus.
    - Si l’opération a réussi, affiche la réponse JSON.
1. Utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.
1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour installer la bibliothèque de **requêtes** Python (qui est utilisée dans le code) :

    ```
    pip install requests
    ```

1. Une fois la bibliothèque installée, dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour exécuter le code Python :

    ```
    python analyze_doc.py
    ```

1. Passez en revue la sortie du programme, qui inclut les résultats JSON de l’analyse de document.

    > **Conseil** : la mémoire tampon d’écran dans la console Cloud Shell peut ne pas être suffisamment grande pour afficher la sortie entière. Si vous souhaitez passer en revue l’intégralité de la sortie, exécutez le programme à l’aide de la commande `python analyze_doc.py > output.txt`. Ensuite, une fois le programme terminé, utilisez la commande `code output.txt` pour ouvrir la sortie dans un éditeur de code.

## Nettoyage

Si vous avez terminé d’utiliser le service Compréhension de contenu, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Dans le portail Azure AI Foundry, accédez au projet **travel-insurance** et supprimez-le.
1. Dans le portail Azure, supprimez le groupe de ressources que vous avez créé pour cet exercice.

