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

    > **Remarque** : Si Visual Studio Code affiche un message contextuel qui vous invite à approuver le code que vous ouvrez, cliquez sur l’option **Oui, je fais confiance aux auteurs** dans la fenêtre contextuelle.
    
    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant). S’il y a d’autres fenêtres contextuelles de Visual Studio Code, vous pouvez les ignorer en toute sécurité.

1. Développez le dossier **Labfiles** dans le volet de gauche, puis cliquez avec le bouton droit sur le répertoire **03-composed-model**. Sélectionnez l’option à ouvrir dans le terminal intégré, puis exécutez le script suivant :

    ```powershell
    az login --output none
    ```

    > **Remarque** : Si vous obtenez une erreur précisant qu’aucun abonnement n’est actif et que l’authentification multifacteur est activée, vous devrez peut-être vous connecter d’abord au Portail Azure à l’adresse `https://portal.azure.com`, puis réexécuter la commande `az login`.

1. Lorsque vous y êtes invité, connectez-vous à votre abonnement Azure. Revenez ensuite à Visual Studio Code et attendez la fin du processus de connexion.
1. Dans le terminal intégré, exécutez la commande suivante pour configurer les ressources :

   ```powershell
   ./setup.ps1
   ```

   > **IMPORTANT** : La dernière ressource créée par le script est votre service Azure AI Intelligence documentaire. Si cette commande échoue en raison d’une ressource de niveau F0 que vous avez déjà, utilisez cette ressource pour ce labo ou créez-en une manuellement en utilisant le niveau S0 dans le Portail Azure.

## Créer le modèle personnalisé Formulaires 1040

Pour créer un modèle composé, nous devons d’abord créer deux modèles personnalisés ou plus. Pour créer le premier modèle personnalisé :

1. Dans un nouvel onglet du navigateur, démarrez le **Studio Azure AI Intelligence documentaire** à l’adresse `https://documentintelligence.ai.azure.com/studio`
1. Faites défiler vers le bas, puis sous **Modèles personnalisés**, sélectionnez **Modèle d’extraction personnalisé**.
1. Si vous êtes invité à vous connecter à votre compte, utilisez vos informations d’identification Azure.
1. Si vous êtes invité à choisir la ressource Azure AI Intelligence documentaire à utiliser, sélectionnez l’abonnement et le nom de la ressource que vous avez utilisés lors de la création de la ressource Azure AI Intelligence documentaire.
1. Sous **Mes projets**, sélectionnez **+ Créer un projet**.
1. Dans la zone de texte **Nom du projet**, tapez **Formulaires 1040**, puis sélectionnez **Continuer**.
1. Dans la page **Configurer la ressource de service**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez le **DocumentIntelligenceResources&lt;xxxx&gt;** créé pour vous.
1. Dans la liste déroulante **Ressource Azure AI Intelligence documentaire ou Azure AI Services**, sélectionnez **DocumentIntelligence&lt;xxxx&gt;**.
1. Dans la liste déroulante **Version de l’API**, vérifiez que **2023-10-31-préversion** est sélectionné, puis sélectionnez **Continuer**.
1. Dans la page **Se connecter à la source de données de formation**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le seul compte de stockage listé. Si vous avez plusieurs comptes de stockage dans votre abonnement, choisissez celui qui commence par *docintelstorage*
1. Dans la liste déroulante **Conteneur d’objets blob**, sélectionnez **1040examples**, puis sélectionnez **Continuer**.
1. Dans la page **Vérifier et créer**, sélectionnez **Créer un projet**.
1. Sélectionnez **Exécuter le layout** dans la fenêtre contextuelle *Démarrer l’étiquetage maintenant*, puis attendez que l’analyse se termine.

## Étiqueter le modèle personnalisé Formulaires 1040

Maintenant, étiquetons les champs dans les exemples de formulaires :

1. Dans la page **Données d’étiquette**, en haut à droite, sélectionnez **+ Ajouter un champs**, puis sélectionnez **Champ**.
1. Tapez **Prénom**, puis appuyez sur *Entrée*.
1. Sélectionnez le document appelé **f1040_1.pdf** dans la liste de gauche, ensuite **John** et enfin **FirstName**.
1. En haut à droite de la page, sélectionnez **+ Ajouter un champ**, puis **Champ**.
1. Tapez **Nom**, puis appuyez sur *Entrée*.
1. Dans le document, sélectionnez **Doe**, puis **Nom**.
1. En haut à droite de la page, sélectionnez **+ Ajouter un champ**, puis **Champ**.
1. Tapez **Ville**, puis appuyez sur *Entrée*.
1. Dans le document, sélectionnez **Los Angeles**, puis **Ville**.
1. En haut à droite de la page, sélectionnez **+ Ajouter un champ**, puis **Champ**.
1. Tapez **État**, puis appuyez sur *Entrée*.
1. Dans le document, sélectionnez **CA**, puis **État**.
1. Répétez le processus d’étiquetage pour les formulaires restants dans la liste de gauche, en utilisant les étiquettes que vous avez créées. Étiquetez les quatre mêmes champs : *Prénom*, *Nom*, *Ville* et *État*.

> **IMPORTANT** Dans le cadre de cet exercice, nous utilisons seulement cinq exemples de formulaires et étiquetons seulement quatre champs. Dans vos modèles réels, vous devez utiliser autant d’exemples que possible pour optimiser la justesse et la confiance de vos prédictions. Vous devez également étiqueter tous les champs disponibles dans les formulaires, au lieu de seulement quatre champs.

## Entraîner le modèle personnalisé Formulaires 1040

Les exemples de formulaires étant étiquetés, nous pouvons entraîner le premier modèle personnalisé :

1. Dans Azure AI Document Intelligence Studio, sélectionnez **Entraîner**.
1. Dans la boîte de dialogue **Entraîner un nouveau modèle**, dans la zone de texte **ID de modèle**, tapez **1040FormsModel**.
1. Dans la liste déroulante **Mode de génération**, sélectionnez **Modèle**, puis **Entraîner**. 
1. Dans la boîte de dialogue **Entraînement en cours**, sélectionnez **Accéder aux modèles**.

## Créer le modèle personnalisé Formulaires 1099

À présent, vous devez créer un deuxième modèle, que vous allez former sur les exemples de formulaires fiscaux 1099 :

1. Dans le studio Azure AI Intelligence documentaire, sélectionnez **Modèle d’extraction personnalisé**.
1. Sous **Mes projets**, sélectionnez **+ Créer un projet**.
1. Dans la zone de texte **Nom du projet**, tapez **Formulaires 1099**, puis sélectionnez **Continuer**.
1. Dans la page **Configurer la ressource de service**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Dans la liste déroulante **Ressource Azure AI Intelligence documentaire ou Azure AI Services**, sélectionnez **DocumentIntelligence&lt;xxxx&gt;**.
1. Dans la liste déroulante **Version de l’API**, vérifiez que **2023-10-31-préversion** est sélectionné, puis sélectionnez **Continuer**.
1. Dans la page **Se connecter à la source de données de formation**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Azure.
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le seul compte de stockage listé.
1. Dans la liste déroulante **Conteneur d’objets blob**, sélectionnez **1099examples**, puis sélectionnez **Continuer**.
1. Dans la page **Vérifier et créer**, sélectionnez **Créer un projet**.
1. Sélectionnez **Exécuter le layout** dans la fenêtre contextuelle *Démarrer l’étiquetage maintenant*, puis attendez que l’analyse se termine.

## Étiqueter le modèle personnalisé Formulaires 1099

À présent, étiquetez les exemples de formulaires avec certains champs :

1. Dans la page **Données d’étiquette**, en haut à droite, sélectionnez **+ Ajouter un champs**, puis sélectionnez **Champ**.
1. Tapez **Prénom**, puis appuyez sur *Entrée*.
1. Sélectionnez le document appelé **f1099msc_payer.pdf** dans la liste de gauche, ensuite **John** et enfin **FirstName**.
1. En haut à droite de la page, sélectionnez **+ Ajouter un champ**, puis **Champ**.
1. Tapez **Nom**, puis appuyez sur *Entrée*.
1. Dans le document, sélectionnez **Doe**, puis **Nom**.
1. En haut à droite de la page, sélectionnez **+ Ajouter un champ**, puis **Champ**.
1. Tapez **Ville**, puis appuyez sur *Entrée*.
1. Dans le document, sélectionnez **New Haven**, puis **Ville**.
1. En haut à droite de la page, sélectionnez **+ Ajouter un champ**, puis **Champ**.
1. Tapez **État**, puis appuyez sur *Entrée*.
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
1. Dans la boîte de dialogue **Composer un nouveau modèle**, dans la zone de texte **ID de modèle**, tapez **TaxFormsModel**, puis sélectionnez **Composer**. Azure AI Intelligence documentaire crée le modèle composé et l’affiche dans la liste des modèles personnalisés :

## Utiliser le modèle composé

Le modèle composé étant terminé, nous allons le tester avec un exemple de formulaire :

1. Dans le studio Azure AI Intelligence documentaire, sélectionnez la page **Test**, puis sélectionnez **TaxFormsModel** dans la liste déroulante.
1. Sélectionnez **Parcourir les fichiers**, puis accédez à l’emplacement où vous avez cloné le référentiel.
1. Sélectionnez **03-composed-model/trainingdata/TestDoc/f1040_7.pdf**, puis sélectionnez **Ouvrir**.
1. Cliquez sur **Exécuter l’analyse**. Azure AI Intelligence documentaire analyse le formulaire à l’aide du modèle composé.
1. Le document que vous avez analysé est un exemple de formulaire fiscal 1040. Vérifiez la propriété **DocType** pour voir si le modèle personnalisé approprié a été utilisé. Vérifiez également les valeurs **Prénom**, **Nom**, **Ville** et **État** identifiées par le modèle.

## Nettoyer les ressources

Maintenant que vous avez vu comment fonctionnent les modèles composés, nous allons supprimer les ressources que vous avez créées dans votre abonnement Azure.

1. Dans le **Portail Azure** à l’adresse `https://portal.azure.com/`, sélectionnez **Groupes de ressources**.
1. Dans la liste des **Groupes de ressources**, sélectionnez le **DocumentIntelligenceResources&lt;xxxx&gt;** que vous avez créé, puis sélectionnez **Supprimer le groupe de ressources**.
1. Dans la zone de texte **TAPEZ LE NOM DU GROUPE DE RESSOURCES**, tapez le nom du groupe de ressources, puis sélectionnez **Supprimer** pour supprimer la ressource Intelligence documentaire et le compte de stockage.

## En savoir plus

- [Composer des modèles personnalisés](/azure/ai-services/document-intelligence/concept-composed-models)
