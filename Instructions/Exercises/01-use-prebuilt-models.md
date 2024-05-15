---
lab:
  title: Utiliser des modèles Intelligence documentaire prédéfinis
  module: Module 6 - Document Intelligence
---

# Utiliser des modèles Intelligence documentaire prédéfinis

Dans cet exercice, vous allez configurer une ressource Azure AI Intelligence documentaire dans votre abonnement Azure. Vous allez utiliser le studio Azure AI Intelligence documentaire et C# ou Python pour soumettre des formulaires à cette ressource pour analyse.

## Créer une ressource Azure AI Intelligence documentaire

Avant de pouvoir appeler le service Azure AI Intelligence documentaire, vous devez créer une ressource pour héberger ce service dans Azure :

1. Dans un onglet de navigateur, ouvrez le Portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com?azure-portal=true) et connectez-vous avec le compte Microsoft associé à votre abonnement Azure.
1. Sur la page d’accueil du Portail Azure, accédez à la zone de recherche supérieure, tapez **Intelligence documentaire**, puis appuyez sur **Entrée**.
1. Sur la page **Intelligence documentaire**, sélectionnez **Créer**.
1. Sur la page **Créer Intelligence documentaire**, utilisez les éléments suivants pour configurer votre ressource :
    - **Abonnement** : votre abonnement Azure.
    - **Groupe de ressources :** sélectionnez ou créez un groupe de ressources avec un nom unique tel que *DocIntelligenceResources*.
    - **Région** : sélectionnez une région proche de votre emplacement.
    - **Nom** : entrez un nom globalement unique.
    - **Niveau tarifaire** : sélectionnez **F0 gratuit** (si vous n’avez pas de niveau Gratuit disponible, sélectionnez **S0 standard**).
1. Ensuite, sélectionnez **Vérifier + créer**, puis **Créer**. Azure déploie la nouvelle ressource Azure AI Intelligence documentaire.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**. Gardez cette page ouverte pour le reste de cet exercice.

## Utiliser le modèle Lecture

Commençons par utiliser **Azure AI Document Intelligence Studio** et le modèle Lecture pour analyser un document avec plusieurs langues. Vous allez connecter Azure AI Document Intelligence Studio à la ressource que vous venez de créer pour effectuer l’analyse :

1. Ouvrez un nouvel onglet de navigateur, puis accédez au **studio Azure AI Intelligence documentaire** sur [https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio).
1. Sous **Analyse de document**, sélectionnez la vignette **Lecture**.
1. Si vous êtes invité à vous connecter à votre compte, utilisez vos informations d’identification Azure.
1. Si vous êtes invité à choisir la ressource Azure AI Intelligence documentaire à utiliser, sélectionnez l’abonnement et le nom de la ressource que vous avez utilisés lors de la création de la ressource Azure AI Intelligence documentaire.
1. Dans la liste des documents de gauche, sélectionnez **read-german.pdf**.

    ![Capture d’écran montrant la page Lecture dans Azure AI Document Intelligence Studio.](../media/read-german-sample.png#lightbox)

1. En haut à gauche, sélectionnez **Analyser les options**, puis activez la case à cocher **Langage** (sous **Détection facultative**) dans le volet **Analyser les options**, puis cliquez sur **Enregistrer**. 
1. En haut à gauche, sélectionnez **Exécuter l’analyse**.
1. Une fois l’analyse terminée, le texte extrait de l’image s’affiche à droite sous l’onglet **Contenu**. Passez en revue ce texte et comparez sa justesse par rapport au texte de l’image d’origine.
1. Sélectionnez l’onglet **Résultat**. Cet onglet affiche le code JSON extrait. 
1. Faites défiler jusqu’en bas du code JSON sous l’onglet **Résultat**. Notez que le modèle Lecture a détecté la langue de chaque partie du texte. La plupart des étendues sont en Allemand (code de langue `de`), mais vous pouvez trouver d’autres codes de langue dans les étendues (par exemple, Anglais – code de langue `en` – dans l’une des dernières étendues).

    ![Capture d’écran montrant la détection de la langue pour deux étendues dans les résultats du modèle de lecture dans Azure AI Document Intelligence Studio.](../media/language-detection.png#lightbox)

## Préparer le développement d’une application dans Visual Studio Code

Examinons maintenant l’application qui utilise le Kit de développement logiciel (SDK) du service Azure Intelligence documentaire. Vous allez développer votre application à l’aide de Visual Studio Code. Les fichiers de code de votre application ont été fournis dans un référentiel GitHub.

> **Conseil** : si vous avez déjà cloné le **référentiel mslearn-ai-document-intelligence**, ouvrez-le dans Visual Studio Code. Dans le cas contraire, procédez comme suit pour le cloner dans votre environnement de développement.

1. Démarrez Visual Studio Code.
1. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` vers un dossier local (peu importe quel dossier).
1. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.
1. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant). Si vous recevez une invitation par le biais du message *Détection d'un projet Azure Function dans le dossier*, vous pouvez le fermer en toute sécurité.

## Configuration de votre application

Des applications pour C# et Python sont fournies, ainsi qu’un échantillon de fichier PDF que vous utiliserez pour tester Intelligence documentaire. Les deux applications présentent les mêmes fonctionnalités. Premièrement, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure Intelligence documentaire.

1. Examinez la facture suivante et notez certains de ses champs et valeurs. Il s’agit de la facture que votre code va analyser.

    ![Capture d’écran montrant un échantillon de document de facture.](../media/sample-invoice.png#lightbox)

1. Dans Visual Studio Code, dans le volet **Explorateur**, accédez au dossier **Labfiles/01-prebuild-models** et développez le dossier **CSharp** ou **Python** en fonction de votre préférence de langage. Chaque dossier contient les fichiers propres au langage d’une application dans laquelle vous allez intégrer des fonctionnalités Azure Intelligence documentaire.

1. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code, puis sélectionnez **Ouvrir un terminal intégré**. Installez le package du Kit de développement logiciel (SDK) Azure Form Recognizer (l’ancien nom d’Intelligence documentaire) en exécutant la commande appropriée pour votre préférence de langage :

    **C# :**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python** :

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

## Ajouter du code pour utiliser le service Azure Intelligence documentaire

Vous êtes maintenant prêt à utiliser le Kit de développement logiciel (SDK) pour évaluer le fichier pdf.

1. Passez à l’onglet de navigateur qui affiche la vue d’ensemble d’Azure AI Intelligence documentaire dans le portail Azure. Dans le volet gauche, sous *Gestion des ressources*, sélectionnez **Clés et point de terminaison**. À droite de la valeur de **Point de terminaison**, cliquez sur le bouton **Copier dans le Presse-papiers**.
1. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de code correspondant à votre langage préféré, puis remplacez `<Endpoint URL>` par la chaîne que vous venez de copier :

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python** : ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. Passez à l’onglet de navigateur qui affiche les **clés et le point de terminaison** d’Azure AI Intelligence documentaire dans le Portail Azure. À droite de la valeur de **CLÉ 1**, cliquez sur le bouton *Copier dans le Presse-papiers*.
1. Dans le fichier de code de Visual Studio Code, recherchez cette ligne et remplacez `<API Key>` par la chaîne que vous venez de copier :

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. Recherchez le commentaire `Create the client`. Après cela, sur les nouvelles lignes, entrez le code suivant :

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. Recherchez le commentaire `Analyze the invoice`. Après cela, sur les nouvelles lignes, entrez le code suivant :

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. Recherchez le commentaire `Display invoice information to the user`. Après cela, sur de nouvelles lignes, entrez le code suivant :

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > Vous avez ajouté du code pour afficher le nom du fournisseur. Le projet de démarrage inclut également du code pour afficher le *nom du client* et le *total de la facture*.

1. Enregistrez les modifications apportées au fichier de code.

1. Dans le volet de terminal interactif, vérifiez que le contexte du dossier est le dossier correspondant à votre langue préférée. Exécutez ensuite la commande suivante pour exécuter l’application.

1. ***Pour C# uniquement***, pour générer votre projet, entrez cette commande :

    **C# :**

    ```powershell
    dotnet build
    ```

1. Pour exécuter votre code, entrez cette commande :

    **C# :**

    ```powershell
    dotnet run
    ```

    **Python** :

    ```powershell
    python document-analysis.py
    ```

Le programme affiche le nom du fournisseur, le nom du client et le total de la facture avec des niveaux de confiance. Comparez les valeurs qu’il indique avec l’exemple de facture que vous avez ouvert au début de cette section.

## Nettoyage

Si vous en avez terminé avec votre ressource Azure, n’oubliez pas de la supprimer dans le [Portail Azure](https://portal.azure.com/?azure-portal=true) pour éviter les frais supplémentaires.

## Plus d’informations

Pour plus d’informations sur le service Intelligence documentaire, consultez la documentation [Intelligence documentaire](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
