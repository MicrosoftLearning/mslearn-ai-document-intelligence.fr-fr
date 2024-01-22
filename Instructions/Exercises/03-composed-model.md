---
lab:
  title: Créer un modèle Intelligence documentaire composé
  module: Module 6 - Document Intelligence
---

# Créer un modèle Intelligence documentaire composé

Dans cet exercice, vous allez créer et entraîner deux modèles personnalisés qui analysent différents formulaires fiscaux. Ensuite, vous allez créer un modèle composé qui inclut ces deux modèles personnalisés. Vous allez tester le modèle en envoyant un formulaire et vous vérifierez qu’il reconnaît correctement le type de document et les champs étiquetés.

## Configurer les ressources

Nous allons utiliser un script pour créer la ressource Azure AI Intelligence documentaire, un compte de stockage avec des exemples de formulaires et un groupe de ressources :

1. Démarrez Visual Studio Code.
1. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` vers un dossier local (peu importe quel dossier).
1. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.
1. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant).

1. Cliquez avec le bouton droit sur le répertoire **03-composed-model**, ouvrez le terminal intégré, puis exécutez le script d’installation :

   ``` bash
   bash setup.sh
   ```

## Créer le modèle personnalisé Formulaires 1040

Pour créer un modèle composé, nous devons d’abord créer deux modèles personnalisés ou plus. Pour créer le premier modèle personnalisé :

1. Dans un nouvel onglet de navigateur, démarrez [Azure AI Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio).
1. Faites défiler vers le bas, puis sous **Modèle personnalisé**, sélectionnez **Modèle personnalisé**.
1. Si vous êtes invité à vous connecter à votre compte, utilisez vos informations d’identification Azure.
1. Si vous êtes invité à choisir la ressource Azure AI Intelligence documentaire à utiliser, sélectionnez l’abonnement et le nom de la ressource que vous avez utilisés lors de la création de la ressource Azure AI Intelligence documentaire.
1. Sous **Mes projets**, sélectionnez **+ Créer un projet**.
1. Dans la zone de texte **Nom du projet**, tapez **Formulaires 1040**, puis sélectionnez **Continuer**.
1. Dans la page **Configurer la ressource de service**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources**.
1. Dans la liste déroulante **Ressource Azure AI Intelligence documentaire ou Azure AI Service**, sélectionnez **DocumentIntelligence**.
1. Dans la liste déroulante **Version de l’API**, vérifiez que **2022-06-30-preview** est sélectionné, puis sélectionnez **Continuer**.

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="Capture d’écran montrant la page de configuration des ressources de service dans l’Assistant Modèle personnalisé Azure AI Document Intelligence Studio." lightbox="../media/4-configure-service-resources.png":::

1. Dans la page **Configurer la source de données d’entraînement**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le seul compte de stockage listé.
1. Dans la liste déroulante **Conteneur d’objets blob**, sélectionnez **1040examples**, puis sélectionnez **Continuer**.

    :::image type="content" source="../media/4-connect-training-data-source.png" alt-text="Capture d’écran montrant la page Configurer la source de données d’entraînement dans l’Assistant Modèle personnalisé Azure AI Document Intelligence Studio." lightbox="../media/4-connect-training-data-source.png":::

1. Dans la page **Vérifier et créer**, sélectionnez **Créer un projet**.

## Étiqueter le modèle personnalisé Formulaires 1040

Maintenant, étiquetons les champs dans les exemples de formulaires :

1. Dans la page **Données d’étiquette**, en haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.

    :::image type="content" source="../media/4-add-label.png" alt-text="Capture d’écran montrant comment ajouter une nouvelle étiquette dans Azure AI Document Intelligence Studio." lightbox="../media/4-add-label.png":::

1. Tapez **Prénom**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **John**, puis **Prénom**.

    :::image type="content" source="../media/4-label-first-name.png" alt-text="Capture d’écran montrant comment compléter une nouvelle étiquette dans Azure AI Document Intelligence Studio." lightbox="../media/4-label-first-name.png":::

1. En haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **Nom**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **Doe**, puis **Nom**.
1. En haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **Ville**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **Los Angeles**, puis **Ville**.
1. En haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **État**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **CA**, puis **État**.
1. Répétez le processus d’étiquetage pour les formulaires restants dans la liste située à gauche. Étiquetez les quatre mêmes champs : *Prénom*, *Nom*, *Ville* et *État*.

> [!IMPORTANT]
> Dans le cadre de cet exercice, nous utilisons seulement cinq exemples de formulaires et étiquetons seulement quatre champs. Dans vos modèles réels, vous devez utiliser autant d’exemples que possible pour optimiser la justesse et la confiance de vos prédictions. Vous devez également étiqueter tous les champs disponibles dans les formulaires, au lieu de seulement quatre champs.

## Entraîner le modèle personnalisé Formulaires 1040

Les exemples de formulaires étant étiquetés, nous pouvons entraîner le premier modèle personnalisé :

1. Dans Azure AI Document Intelligence Studio, sélectionnez **Entraîner**.
1. Dans la boîte de dialogue **Entraîner un nouveau modèle**, dans la zone de texte **ID de modèle**, tapez **1040FormsModel**.
1. Dans la liste déroulante **Mode de génération**, sélectionnez **Modèle**, puis **Entraîner**. 
1. Dans la boîte de dialogue **Entraînement en cours**, sélectionnez **Accéder aux modèles**.

## Créer le modèle personnalisé Formulaires 1099

À présent, vous devez créer un deuxième modèle, que vous allez former sur les exemples de formulaires fiscaux 1099 :

1. Dans Azure AI Document Intelligence Studio, sélectionnez **Modèle personnalisé**.
1. Sous **Mes projets**, sélectionnez **+ Créer un projet**.
1. Dans la zone de texte **Nom du projet**, tapez **Formulaires 1099**, puis sélectionnez **Continuer**.
1. Dans la page **Configurer la ressource de service**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources**.
1. Dans la liste déroulante **Ressource Azure AI Intelligence documentaire ou Azure AI Service**, sélectionnez **DocumentIntelligence**.
1. Dans la liste déroulante **Version de l’API**, vérifiez que **2022-06-30-preview** est sélectionné, puis sélectionnez **Continuer**.

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="Capture d’écran montrant la page de configuration des ressources de service dans l’Assistant Modèle personnalisé Azure AI Document Intelligence Studio." lightbox="../media/4-configure-service-resources.png":::

1. Dans la page **Configurer la source de données d’entraînement**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le seul compte de stockage listé.
1. Dans la liste déroulante **Conteneur d’objets blob**, sélectionnez **1099examples**, puis sélectionnez **Continuer**.
1. Dans la page **Vérifier et créer**, sélectionnez **Créer un projet**.

## Étiqueter le modèle personnalisé Formulaires 1099

À présent, étiquetez les exemples de formulaires avec certains champs :

1. Dans la page **Données d’étiquette**, en haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **Prénom**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **John**, puis **Prénom**.
1. En haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **Nom**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **Doe**, puis **Nom**.
1. En haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **Ville**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **New Haven**, puis **Ville**.
1. En haut à droite de la page, sélectionnez **+**, puis sélectionnez **Champ**.
1. Tapez **État**, puis appuyez sur <kbd>Entrée</kbd>.
1. Dans le document, sélectionnez **CT**, puis **État**.
1. Répétez le processus d’étiquetage pour les formulaires restants dans la liste située à gauche. Étiquetez les quatre mêmes champs : *Prénom*, *Nom*, *Ville* et *État*.

## Entraîner le modèle personnalisé Formulaires 1099

Vous pouvez maintenant entraîner le deuxième modèle personnalisé :

1. Dans Azure AI Document Intelligence Studio, sélectionnez **Entraîner**.
1. Dans la boîte de dialogue **Entraîner un nouveau modèle**, dans la zone de texte **ID de modèle**, tapez **1099FormsModel**.
1. Dans la liste déroulante **Mode de génération**, sélectionnez **Modèle**, puis **Entraîner**. 
1. Dans la boîte de dialogue **Entraînement en cours**, sélectionnez **Accéder aux modèles**.
1. Le processus d'apprentissage peut prendre plusieurs minutes. Actualisez le navigateur de temps en temps jusqu’à ce que les deux modèles affichent l’état **réussi**.

## Créer et assembler un modèle composé

Les deux modèles personnalisés, qui analysent les formulaires fiscaux 1040 et 1099, sont maintenant terminés. Vous pouvez passer à la création du modèle composé :

1. Sur la page Modèles d’Azure AI Intelligence documentaire, sélectionnez **1040FormsModel** et **1099FormsModel**.
1. En haut de la liste des modèles, sélectionnez **Composer**.

    :::image type="content" source="../media/4-start-compose-model.png" alt-text="Capture d’écran montrant comment commencer à composer un modèle dans Azure AI Document Intelligence Studio." lightbox="../media/4-start-compose-model.png":::

1. Dans la boîte de dialogue **Composer un nouveau modèle**, dans la zone de texte **ID de modèle**, tapez **TaxFormsModel**, puis sélectionnez **Composer**. Azure AI Intelligence documentaire crée le modèle composé et l’affiche dans la liste des modèles personnalisés :

## Utiliser le modèle composé

Le modèle composé étant terminé, nous allons le tester avec un exemple de formulaire :

1. Dans le [Portail Azure](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true), sélectionnez **Toutes les ressources**, puis sélectionnez le compte de stockage **formsrecstorage&lt;xxxxx&gt;**, où &lt;xxxxx&gt; est un nombre aléatoire.
1. Sous **Stockage de données**, sélectionnez **Conteneurs**, puis **TestDoc**.
1. À droite de **f1040_7.pdf**, sélectionnez **...**, puis sélectionnez **Télécharger**.
1. Enregistrez le document PDF sur votre ordinateur local et notez l’emplacement enregistré.
1. Dans Azure AI Document Intelligence Studio, sélectionnez **TaxFormsModel**, puis **Tester**.
1. Sélectionnez **+ Ajouter**, puis accédez à l’emplacement où vous avez enregistré le document PDF.
1. Sélectionnez **f1040_7.pdf**, puis **Ouvrir**.
1. Sélectionnez **Analyser**. Azure AI Intelligence documentaire analyse le formulaire à l’aide du modèle composé.

    :::image type="content" source="../media/4-composed-model-analysis.png" alt-text="Capture d’écran montrant comment utiliser un modèle composé dans Azure AI Document Intelligence Studio." lightbox="../media/4-composed-model-analysis.png":::

1. Le document que vous avez analysé est un exemple de formulaire fiscal 1040. Vérifiez la propriété **DocType** pour voir si le modèle personnalisé approprié a été utilisé. Vérifiez également les valeurs **Prénom**, **Nom**, **Ville** et **État** identifiées par le modèle.

## Nettoyer les ressources de l’exercice

Maintenant que vous avez vu comment fonctionnent les modèles composés, nous allons supprimer les ressources que vous avez créées dans votre abonnement Azure.

1. Dans le [portail Azure](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true), sélectionnez **Groupes de ressources**.
1. Dans la liste des **groupes de ressources**, sélectionnez **DocumentIntelligenceResources**, puis sélectionnez **Supprimer le groupe de ressources**. 
1. Dans la zone **de texte TAPEZ LE NOM DU GROUPE DE RESSOURCES**, tapez **DocumentIntelligenceResources**, puis sélectionnez **Supprimer** pour supprimer la ressource Intelligence documentaire et le compte de stockage.

## En savoir plus

- [Composer des modèles personnalisés](/azure/ai-services/document-intelligence/concept-composed-models)
- [Créer votre jeu de données d’apprentissage pour un modèle personnalisé](/azure/applied-ai-services/form-recognizer/how-to-guides/build-custom-model-v3)
