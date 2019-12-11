---
title: 'Rychlý Start: vytvoření projektu pro detekci objektů pomocí sady C# SDK pro Custom Vision'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a detekujte objekty pomocí sady .NET SDK a jazyka C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: ed86a286051ba9240fe23951ef7cb72de1e96201
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74960566"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Rychlý start: Vytvoření projektu detekce objektů pomocí sady Custom Vision .NET SDK

V tomto článku se dozvíte, jak začít používat sadu Custom Vision SDK C# s nástrojem k sestavení modelu detekce objektu. Po jeho vytvoření můžete přidat označené oblasti, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v .NET. 

## <a name="prerequisites"></a>Předpoklady

- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získání sady Custom Vision SDK a vzorového kódu

K napsání aplikace v .NET, která využívá službu Custom Vision, budete potřebovat balíčky NuGet pro službu Custom Vision. Tyto balíčky jsou součástí ukázkového projektu, který budete stahovat, ale můžete k nim přistupovat jednotlivě.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Přejděte do složky **CustomVision/ObjectDetection** a otevřete _ObjectDetection. csproj_ v aplikaci Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování modelu detekce objektů. V tomto projektu se model trénuje k detekování vidliček a nůžek na obrázcích.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Vysvětlení kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. [Vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro vaše školicí a předpovědní klíče s názvem `CUSTOM_VISION_TRAINING_KEY` a `CUSTOM_VISION_PREDICTION_KEY`, v uvedeném pořadí. Skript bude hledat tyto proměnné.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Adresu URL koncového bodu si také můžete stáhnout ze stránky nastavení na webu Custom Vision. Uložte ji do proměnné prostředí s názvem `CUSTOM_VISION_ENDPOINT`. Skript uloží odkaz na něj v kořenovém adresáři vaší třídy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision Service

Tato další část kódu vytvoří projekt detekce objektů. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. Chcete-li určit další možnosti při vytváření projektu, viz metoda [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) (vysvětlení najdete v průvodci [vytvořením webového portálu detektoru](get-started-build-detector.md) ).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Přidání značek do projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Při označování obrázků v projektech detekce obrázků je potřeba zadat oblast každého označeného objektu pomocí normalizovaných souřadnic. Následující kód přidruží jednotlivé ukázkové obrázky k odpovídající označené oblasti.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Potom se pomocí této mapy přidružení nahrají jednotlivé ukázkové obrázky s odpovídajícími souřadnicemi oblasti. Do jedné dávky můžete nahrát až 64 imagí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

V tomto okamžiku jste nahráli všechny snímky ukázek a označili je každou z nich (**rozvětvení** nebo **nůžky**) s přiřazeným obdélníkem pixelů.

### <a name="train-the-project"></a>Trénování projektu

Tento kód vytvoří první iteraci trénování v projektu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publikovat aktuální iteraci

Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

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

Nyní jste viděli, jak provést všechny kroky procesu detekce objektů v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
