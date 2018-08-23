---
title: Použijte službu Custom Vision Service z aplikace v C# – Azure Cognitive Services | Dokumentace Microsoftu
description: Prozkoumejte základní aplikaci jazyka C#, která používá vlastní rozhraní API pro zpracování obrazu ve službě Microsoft Cognitive Services. Vytvoření projektu, přidání značek, nahrávat obrázky, trénování váš projekt a předpovědím pomocí výchozí koncový bod.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d3c2ffb0fd9578458bd07241eed4a87cf70d3c3c
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617430"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Použijte službu Custom Vision Service z C&#35; aplikace

Informace o používání služby Custom Vision Service z aplikace v jazyce C#. Po jeho vytvoření, je můžete přidat značky, nahrajte obrázky, trénování projektu, získat adresu URL koncového bodu projektu výchozí předpovědi a použít koncový bod pro programové testování bitovou kopii. Použijte tento příklad open source jako šablonu pro vytvoření vlastní aplikace pro Windows s použitím vlastní rozhraní API služby pro zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady Visual Studio 2017 pro Windows.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Získejte Custom Vision SDK a ukázky
Sestavení tohoto příkladu, budete potřebovat vlastní balíčky NuGet sady SDK pro zpracování obrazu:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Můžete si stáhnout Image spolu s [ukázky jazyka C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Získání klíčů trénování a predikcí

Klíče používané v tomto příkladu najdete [vizi vlastní webovou stránku](https://customvision.ai) a vyberte __ikonu ozubeného kola__ v pravém horním rohu. V __účty__ tématu, zkopírujte hodnoty z __školení klíč__ a __předpovědi klíč__ pole.

![Obrázek klíče uživatelského rozhraní](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Vysvětlení kódu

V sadě Visual Studio, otevřete projekt v `Samples/CustomVision.Sample/` adresáře projektu sadu SDK.

Tato aplikace používá školení klíč, který jste získali dříve k vytvoření nového projektu s názvem __tento nový projekt__. Potom nahrává obrázky a natrénuje a otestuje třídění. Třídění označuje, zda je strom __Hemlock__ nebo __japonské výběru určitých položek__.

Následující fragmenty kódu implementovat primární funkce v tomto příkladu:

* __Vytvořte nový projekt služby Custom Vision Service__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Vytvoření značky v projektu__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Nahrání a opatřovat obrázky značkami__:

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

* __Trénování třídění__:

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

* __Nastavit výchozí iterace pro koncový bod předpovědi__:

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
 
* __Odeslat image do koncového bodu předpovědi__:

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

1. Proveďte následující změny k přidání klíčů, trénování a predikcí k aplikaci:

    * Přidat vaše __školení klíč__ na následující řádek:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Přidat vaše __předpovědi klíč__ na následující řádek:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Spusťte aplikaci. Jak je aplikace spuštěná, následující výstup je zapsán do konzoly:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Stisknutím libovolné klávesy ukončete aplikaci.
