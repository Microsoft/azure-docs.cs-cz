---
title: Použít vlastní vize službu z jazyka C# aplikace - kognitivní služby Azure | Microsoft Docs
description: Prozkoumejte základní C# aplikaci, která používá rozhraní API vize vlastní v kognitivní služby společnosti Microsoft. Vytvoření projektu, se přidat značky, odesílání bitové kopie, cvičení projektu a předpovědím pomocí výchozí koncový bod.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342860"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Použít službu vlastní vize z a C&#35; aplikace

Zjistěte, jak používat službu vlastní vize z aplikace v jazyce C#. Po vytvoření, můžete přidat značky, nahrajte obrázky, cvičení projektu, získat adresu URL koncového bodu projektu výchozí předpovědi a použijte koncový bod pro programové testování bitovou kopii. Použijte tento příklad open-source jako šablonu pro vytvoření vlastní aplikace pro systém Windows pomocí rozhraní API služby vize vlastní.

## <a name="prerequisites"></a>Požadavky

* Všechny edice Visual Studio 2015 nebo 2017 pro systém Windows.

* [Sady SDK služby vlastní vize](http://github.com/Microsoft/Cognitive-CustomVision-Windows/). To zahrnuje ukázkový a obrázků použitých v tomto dokumentu.

## <a name="get-the-training-and-prediction-keys"></a>Získání klíčů školení a předpovědi

Ke klíčů používaných v tomto příkladu najdete [vize vlastní webové stránky](https://customvision.ai) a vyberte __ozubené kolečko ikonu__ v pravém horním rohu. V __účty__ část, zkopírujte hodnoty z __školení klíč__ a __předpovědi klíč__ pole.

![Obrázek klíče uživatelského rozhraní](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Vysvětlení kódu

V sadě Visual Studio, otevřete projekt v `Samples/CustomVision.Sample/` adresáři projektu sady SDK.

Tato aplikace používá školení klíč, který jste získali dříve k vytvoření nového projektu s názvem __Moje nový projekt__. Pak odešle bitové kopie a natrénuje a otestuje třídění. Třídění určuje, jestli je stromu __Hemlock__ nebo __japonské funkce__.

Následující fragmenty kódu implementovat primární funkce tohoto příkladu:

* __Vytvoření nového projektu služby vize vlastní__:

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

* __Nahrání a označit bitové kopie__:

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

* __Cvičení třídění__:

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

* __Nastavit výchozí iteraci pro koncový bod předpovědi__:

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
 
* __Odeslat bitovou kopii na koncový bod předpovědi__:

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

1. Proveďte následující změny k přidání klíčů, školení a předpovědi k aplikaci:

    * Přidání vaší __školení klíč__ na následující řádek:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Přidání vaší __předpovědi klíč__ na následující řádek:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Spusťte aplikaci. Jak je aplikace spuštěná, je následující výstup zapsán do konzoly:

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
