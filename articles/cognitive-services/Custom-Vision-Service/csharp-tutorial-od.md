---
title: 'Quickstart: Create an object detection project with the SDK for C# - Custom Vision'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a detekujte objekty pomocí sady .NET SDK a jazyka C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7baa3e2de00997496edb5b445f8426d899ac65a4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383720"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Rychlý start: Vytvoření projektu detekce objektů pomocí sady Custom Vision .NET SDK

Tento článek obsahuje informace a vzorový kód, které vám pomůžou začít s vytvořením modelu detekce objektů pomocí sady Custom Vision SDK a jazyka C#. Po jeho vytvoření můžete přidat označené oblasti, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v .NET. 

## <a name="prerequisites"></a>Předpoklady

- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získání sady Custom Vision SDK a vzorového kódu

K napsání aplikace v .NET, která využívá službu Custom Vision, budete potřebovat balíčky NuGet pro službu Custom Vision. These packages are included in the sample project you will download, but you can access them individually here.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navigate to the **CustomVision/ObjectDetection** folder and open _ObjectDetection.csproj_ in Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování modelu detekce objektů. V tomto projektu se model trénuje k detekování vidliček a nůžek na obrázcích.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Vysvětlení kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. [Create environment variables](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) for your training and prediction keys named `CUSTOM_VISION_TRAINING_KEY` and `CUSTOM_VISION_PREDICTION_KEY`, respectively. The script will look for these.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Also, get your Endpoint URL from the Settings page of the Custom Vision website. Save it to an environment variable called `CUSTOM_VISION_ENDPOINT`. The script saves a reference to it at the root of your class.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision Service

Tato další část kódu vytvoří projekt detekce objektů. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. See the [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) method to specify other options when you create your project (explained in the [Build a detector](get-started-build-detector.md) web portal guide).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Přidání značek do projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Při označování obrázků v projektech detekce obrázků je potřeba zadat oblast každého označeného objektu pomocí normalizovaných souřadnic. Následující kód přidruží jednotlivé ukázkové obrázky k odpovídající označené oblasti.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Potom se pomocí této mapy přidružení nahrají jednotlivé ukázkové obrázky s odpovídajícími souřadnicemi oblasti. You can upload up to 64 images in a single batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

V tuto chvíli jsou všechny ukázkové obrázky nahrané a všechny mají značku (**fork** (vidlička) nebo **scissors** (nůžky)) a přidružený obdélník pixelů pro danou značku.

### <a name="train-the-project"></a>Trénování projektu

Tento kód vytvoří první iteraci trénování v projektu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publish the current iteration

The name given to the published iteration can be used to send prediction requests. An iteration is not available in the prediction endpoint until it is published.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Vytvoření koncového bodu předpovědi

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Tato část skriptu nahraje testovací obrázek, odešle dotaz do koncového bodu modelu a vypíše data předpovědi do konzoly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Spuštění aplikace

Když je aplikace spuštěná, mělo by se otevřít okno konzoly s následujícím výstupem:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Pak můžete ověřit správné označení testovacího obrázku (ve složce **Images/Test/** ) a správnost oblasti detekce. V tuto chvíli můžete aplikaci ukončit stisknutím libovolné klávesy.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Další kroky

Právě jste viděli, jak se dají jednotlivé kroky procesu detekce objektů provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
