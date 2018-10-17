---
title: 'Kurz: Vytvoření aplikace Windows pro službu Custom Vision Service s C#'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, vytrénujte svůj projekt a vytvořte předpověď pomocí výchozího koncového bodu.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 9e5ed71d4620f7ffeac8acb15f90d67964a86870
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366637"
---
# <a name="tutorial-use-the-custom-vision-service-from-a-c-application"></a>Kurz: Použití služby Custom Vision Service z aplikace v jazyce C#

Naučte se používat službu Custom Vision Service z aplikace v jazyce C#. Po jeho vytvoření můžete přidat značky, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento opensourcový příklad použijte jako šablonu pro vytvoření vlastní aplikace pro Windows pomocí rozhraní Custom Vision Service API.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady Visual Studio 2017 pro Windows

## <a name="get-the-custom-vision-sdk-and-samples"></a>Získání sady Custom Vision SDK a ukázek
Abyste mohli tento příklad sestavit, potřebujete balíčky NuGet sady Custom Vision SDK:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Obrázky si můžete stáhnout společně s [ukázkami jazyka C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Získání tréninkového klíče a klíče předpovědi

Klíče používané v tomto příkladu získáte tak, že přejdete na [webovou stránku služby Custom Vision](https://customvision.ai) a vyberete __ikonu ozubeného kola__ v pravém horním rohu. V části __Účty__ zkopírujte hodnoty z polí pro __tréninkový klíč__ a __klíč předpovědi__.

![Obrázek uživatelského rozhraní klíčů](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Vysvětlení kódu

V sadě Visual Studio otevřete projekt umístěný v adresáři `Samples/CustomVision.Sample/` projektu sady SDK.

Tato aplikace používá dříve získaný tréninkový klíč k vytvoření nového projektu s názvem __My New Project__. Potom nahraje obrázky k trénování a testování klasifikátoru. Klasifikátor identifikuje, jestli je rostlina __bolehlav__ nebo __japonská třešeň__.

Následující fragmenty kódu implementují primární funkci tohoto příkladu:

* __Vytvoření nového projektu služby Custom Vision Service__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Vytvoření značek v projektu__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Nahrání a označení obrázků__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Vytrénování klasifikátoru__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Nastavení výchozí iterace pro koncový bod předpovědi__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Vytvoření koncového bodu předpovědi__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Odeslání obrázku do koncového bodu předpovědi__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Spuštění aplikace

1. Proveďte následující změny pro přidání tréninkového klíče a klíče předpovědi do aplikace:

    * __Tréninkový klíč__ přidejte na následující řádek:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * __Klíč předpovědi__ přidejte na následující řádek:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Spusťte aplikaci. Když je aplikace spuštěná, do konzoly se zapisuje následující výstup:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Aplikaci ukončete stisknutím libovolné klávesy.
