---
lab:
  title: Extraire les données de formulaires
  module: Module 6 - Document Intelligence
---

# Extraire les données de formulaires

Supposons qu’une entreprise demande actuellement à ses employés d’acheter manuellement des feuilles de commande et de saisir les données dans une base de données. Ils souhaitent utiliser des services IA pour améliorer le processus d’entrée de données. Vous décidez de créer un modèle Machine Learning qui lit le formulaire et produit des données structurées qui peuvent être utilisées pour mettre à jour automatiquement une base de données.

**Azure AI Intelligence documentaire** est un service Azure AI qui permet aux utilisateurs de générer un logiciel de traitement de données automatisé. Ce logiciel peut extraire du texte, des paires clé/valeur et des tables à partir de documents de formulaire à l’aide de la reconnaissance optique de caractères (OCR). Azure AI Intelligence documentaire propose des modèles prédéfinis pour reconnaître les factures, les reçus et les carte d’entreprise. Le service offre également la possibilité d’entraîner des modèles personnalisés. Dans cet exercice, nous allons nous concentrer sur la création de modèles personnalisés.

## Préparer le développement d’une application dans Visual Studio Code

Nous allons maintenant utiliser le Kit de développement logiciel (SDK) du service pour développer une application à l’aide de Visual Studio Code. Les fichiers de code de votre application ont été fournis dans un référentiel GitHub.

> **Conseil** : si vous avez déjà cloné le **référentiel mslearn-ai-document-intelligence**, ouvrez-le dans Visual Studio Code. Dans le cas contraire, procédez comme suit pour le cloner dans votre environnement de développement.

1. Démarrez Visual Studio Code.
1. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` vers un dossier local (peu importe quel dossier).
1. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.

    > **Remarque** : Si Visual Studio Code affiche un message contextuel qui vous invite à approuver le code que vous ouvrez, cliquez sur l’option **Oui, je fais confiance aux auteurs** dans la fenêtre contextuelle.

1. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant). Si vous recevez une invitation par le biais du message *Détection d'un projet Azure Function dans le dossier*, vous pouvez le fermer en toute sécurité.

## Créer une ressource Azure AI Intelligence documentaire

Pour utiliser le service Azure AI Intelligence documentaire, vous avez besoin d’une ressource Azure AI Intelligence documentaire ou Azure AI Services dans votre abonnement Azure. Vous utilisez le Portail Azure pour créer une ressource.

1. Dans un onglet de navigateur, ouvrez le Portail Azure à l’adresse `https://portal.azure.com` et connectez-vous avec le compte Microsoft associé à votre abonnement Azure.
1. Sur la page d’accueil du Portail Azure, accédez à la zone de recherche supérieure, tapez **Intelligence documentaire**, puis appuyez sur **Entrée**.
1. Sur la page **Intelligence documentaire**, sélectionnez **Créer**.
1. Sur la page **Créer Intelligence documentaire**, utilisez les éléments suivants pour configurer votre ressource :
    - **Abonnement** : votre abonnement Azure.
    - **Groupe de ressources :** sélectionnez ou créez un groupe de ressources avec un nom unique tel que *DocIntelligenceResources*.
    - **Région** : sélectionnez une région proche de votre emplacement.
    - **Nom** : entrez un nom globalement unique.
    - **Niveau tarifaire** : sélectionnez **F0 gratuit** (si vous n’avez pas de niveau Gratuit disponible, sélectionnez **S0 standard**).
1. Ensuite, sélectionnez **Vérifier + créer**, puis **Créer**. Azure déploie la nouvelle ressource Azure AI Intelligence documentaire.
1. Le déploiement terminé, sélectionnez **Accéder à la ressource** pour afficher la page **Vue d’ensemble** de la ressource. 

## Collecter des documents pour l’entraînement

Vous allez utiliser des échantillons de formulaires comme celui-ci pour effectuer l’apprentissage d’un modèle de test : 

![Image d’une facture utilisée dans ce projet.](../media/Form_1.jpg)

1. Revenez à **Visual Studio Code**. Dans le volet *Explorateur*, ouvrez le dossier **Labfiles/02-custom-document-intelligence** et développez le dossier **sample-forms**. Notez que les fichiers se terminent par **.json** et **.jpg** dans le dossier.

    Vous utiliserez les fichiers **.jpg** pour entraîner votre modèle.  

    Les fichiers **.json** ont été générés pour vous et contiennent des informations d’étiquette. Les fichiers seront chargés dans votre conteneur de stockage d’objets blob en même temps que les formulaires.

    Vous pouvez afficher les images que nous utilisons dans le dossier *sample-forms* en les sélectionnant dans Visual Studio Code.

1. Revenez au **Portail Azure** et accédez à la page **Vue d’ensemble** de votre ressource si vous n’y êtes pas déjà. Sous la section *Essentials*, affichez le **groupe de ressources** dans lequel vous avez créé la ressource Intelligence documentaire.

1. Sur la page **Vue d’ensemble** de votre groupe de ressources, notez l’**ID d’abonnement** et l’**Emplacement**. Vous aurez besoin de ces valeurs, ainsi que du nom de votre **groupe de ressources** dans les étapes suivantes.

    ![Un exemple de la page de groupe de ressources.](../media/resource_group_variables.png)

1. Dans Visual Studio Code, dans le volet *Explorateur*, accédez au dossier **Labfiles/02-custom-document-intelligence** et développez le dossier **CSharp** ou **Python** en fonction de votre préférence de langage. Chaque dossier contient les fichiers propres au langage d’une application dans laquelle vous allez intégrer des fonctionnalités Azure OpenAI.

1. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code, puis sélectionnez **Ouvrir un terminal intégré**. 

1. Dans le terminal, exécutez la commande suivante pour vous connecter à Azure. La commande **az login** ouvre le navigateur Microsoft Edge. Connectez-vous avec le même compte que celui que vous avez utilisé pour créer la ressource Azure AI Intelligence documentaire. Une fois connecté, fermez la fenêtre du navigateur.

    ```powershell
    az login
    ```

1. Revenez à Visual Studio Code. Dans le volet du terminal, exécutez la commande suivante pour répertorier les emplacements Azure.

    ```powershell
    az account list-locations -o table
    ```

1. Dans la sortie, recherchez la valeur **Nom** qui correspond à l’emplacement de votre groupe de ressources (par exemple, pour *East US*, le nom correspondant est *eastus*).

    > **Important** : Enregistrez la valeur **Nom** et utilisez-la à l’étape 11.

1. Dans Visual Studio Code, dans le dossier **Labfiles/02-custom-document-intelligence**, sélectionnez **setup.cmd**. Vous allez utiliser ce script pour exécuter les commandes d’interface de ligne de commande (CLI) Azure requises pour créer les autres ressources Azure dont vous avez besoin.

1. Dans le script **setup.cmd**, passez en revue les commandes. Le programme va :
    - Créer un compte de stockage dans votre groupe de ressources Azure
    - Télécharger des fichiers de votre dossier *sampleforms* local vers un conteneur appelé *sampleforms* dans le compte de stockage
    - Imprimer un URI de signature d’accès partagé

1. Modifiez les déclarations de variable **subscription_id**, **resource_group** et **location** avec les valeurs appropriées pour le nom de l’abonnement, du groupe de ressources et de l’emplacement où vous avez déployé la ressource Intelligence documentaire.
Ensuite, **enregistrez** vos modifications.

    Laissez la variable **expiry_date** telle qu’elle est pour l’exercice. Cette variable est utilisée lors de la génération de l’URI SAP (Shared Access Signature). Dans la pratique, vous devez définir une date d’expiration appropriée pour votre SAP. Vous en découvrirez davantage sur les SAP [ici](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).  

1. Dans le terminal du dossier **Labfiles/02-custom-document-intelligence**, entrez la commande suivante pour exécuter le script :

    ```PowerShell
    ./setup.cmd
    ```

1. Une fois le script terminé, passez en revue la sortie affichée.

1. Dans le Portail Azure, actualisez votre groupe de ressources et vérifiez qu’il contient le compte Stockage Azure qui vient d’être créé. Ouvrez le compte de stockage et, dans le volet de gauche, sélectionnez **Navigateur de stockage **. Ensuite, dans le navigateur de stockage, développez **Conteneurs blob ** et sélectionnez le conteneur **sampleforms** pour vérifier que les fichiers ont été chargés à partir de votre dossier local **02-custom-document-intelligence/sample-forms**.

## Effectuer l’apprentissage du modèle à l’aide de Studio Intelligence documentaire

Vous allez maintenant effectuer l’apprentissage du modèle à l’aide des fichiers chargés sur le compte de stockage.

1. Dans votre navigateur, accédez à Studio Intelligence documentaire à l’adresse `https://documentintelligence.ai.azure.com/studio`.
1. Faites défiler vers le bas jusqu’à la section **Modèles personnalisés** et sélectionnez la vignette **Modèle d’extraction personnalisé**.
1. Si vous êtes invité à vous connecter à votre compte, utilisez vos informations d’identification Azure.
1. Si vous êtes invité à choisir la ressource Azure AI Intelligence documentaire à utiliser, sélectionnez l’abonnement et le nom de la ressource que vous avez utilisés lors de la création de la ressource Azure AI Intelligence documentaire.
1. Sous **Mes projets**, sélectionnez **Créer un projet**. Utilisez les configurations suivantes :

    - **Projet** : entrez un nom unique.
        - Sélectionnez *Continuer*.
    - **Configurer la ressource de service** : sélectionnez l’abonnement, le groupe de ressources et la ressource Intelligence documentaire que vous avez créée précédemment dans ce labo. Cochez la case *Définir la valeur par défaut*. Conservez la version de l’API par défaut. 
        - Sélectionnez *Continuer*.
    - **Connecter source de données d’apprentissage** : sélectionnez l’abonnement, le groupe de ressources et le compte de stockage créés par le script de configuration. Cochez la case *Définir la valeur par défaut*. Sélectionnez le conteneur d’objets blob `sampleforms` et laissez le chemin d’accès au dossier vide.
        - Sélectionnez *Continuer*.
    - Sélectionnez *Créer un projet*

1. Une fois votre projet créé, en haut à droite de l’écran, sélectionnez **Effectuer l’apprentissage** pour entraîner votre modèle. Utilisez les configurations suivantes :
    - **ID de modèle** : *indiquez un nom global unique (vous aurez besoin du nom de l’ID de modèle à l’étape suivante)*. 
    - **Mode de génération** : modèle.
1. Cliquez sur **Accéder aux modèles**.
1. L’apprentissage peut prendre un certain temps. Une notification s’affiche une fois l’opération terminée.

## Tester votre modèle Intelligence documentaire personnalisé

1. Revenez à Visual Studio Code. Dans le terminal, installez le package Intelligence documentaire en exécutant la commande appropriée pour votre préférence de langage :

    **C# :**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python** :

    ```powershell
    pip install azure-ai-formrecognizer==3.3.3
    ```

1. Dans Visual Studio Code, dans le dossier **Labfiles/02-custom-document-intelligence**, sélectionnez le langage que vous utilisez. Modifiez le fichier de configuration (**appsettings.json** ou **.env**, selon votre préférence de langage) avec les valeurs suivantes :
    - Votre point de terminaison Intelligence documentaire.
    - Votre clé Document Intelligence.
    - L’ID de modèle généré que vous avez fourni lors de la formation de votre modèle. Vous pouvez le trouver sur la page **Modèles** de Studio Intelligence documentaire. **Enregistrez** les changements apportés.

1. Dans Visual Studio Code, ouvrez le fichier de code de votre application cliente (*Program.cs* pour C#, *test-model.py* pour Python) et examinez le code qu’il contient, en particulier que l’image dans l’URL fait référence au fichier dans ce référentiel GitHub sur le Web.

1. Retournez au terminal et entrez la commande suivante pour exécuter le programme :

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. Affichez la sortie et observez comment la sortie du modèle fournit des noms de champs tels que `Merchant` et `CompanyPhoneNumber`.

## Nettoyage

Si vous en avez terminé avec votre ressource Azure, n’oubliez pas de la supprimer dans le [Portail Azure](https://portal.azure.com/?azure-portal=true) pour éviter les frais supplémentaires.

## Plus d’informations

Pour plus d’informations sur le service Intelligence documentaire, consultez la documentation [Intelligence documentaire](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
