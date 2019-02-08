---
title: 'Rychlý start: Vytvoření projektu zjišťování objektů s vlastní Vision SDK proC#'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a detekujte objekty pomocí sady .NET SDK a jazyka C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 9d8340d505308753855fa0fcd286949e80d3ecaa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879271"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Rychlý start: Vytvoření projektu zjišťování objektů s využitím Custom Vision .NET SDK

Tento článek obsahuje informace a vzorový kód, které vám pomůžou začít s vytvořením modelu detekce objektů pomocí sady Custom Vision SDK a jazyka C#. Po jeho vytvoření můžete přidat označené oblasti, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v .NET. 

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získání sady Custom Vision SDK a vzorového kódu
K napsání aplikace v .NET, která využívá službu Custom Vision, budete potřebovat balíčky NuGet pro službu Custom Vision. Tyto balíčky jsou součástí ukázkového projektu, který si stáhnete, ale tady je můžete získat samostatně.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Přejděte **CustomVision/ObjectDetection** složky a otevřete _ObjectDetection.csproj_ v sadě Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování modelu detekce objektů. V tomto projektu se model trénuje k detekování vidliček a nůžek na obrázcích.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Vysvětlení kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. Do odpovídajících definic v metodě **Main** vložte své klíče předplatného.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

Parametr koncový bod by měl odkazovat na oblasti, kde se skupina prostředků Azure obsahující prostředky Custom Vision vytvořil v. V tomto příkladu budeme předpokládat oblasti střed USA – jih a použít:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision Service

Tato další část kódu vytvoří projekt detekce objektů. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Přidání značek do projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Při označování obrázků v projektech detekce obrázků je potřeba zadat oblast každého označeného objektu pomocí normalizovaných souřadnic. Následující kód přidruží jednotlivé ukázkové obrázky k odpovídající označené oblasti.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Potom se pomocí této mapy přidružení nahrají jednotlivé ukázkové obrázky s odpovídajícími souřadnicemi oblasti.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

V tuto chvíli jsou všechny ukázkové obrázky nahrané a všechny mají značku (**fork** (vidlička) nebo **scissors** (nůžky)) a přidružený obdélník pixelů pro danou značku.

### <a name="train-the-project"></a>Trénování projektu

Tento kód vytvoří první iteraci trénování v projektu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="set-the-current-iteration-as-default"></a>Nastavení aktuální iterace jako výchozí

Tento kód označí aktuální iteraci jako výchozí iteraci. Výchozí iterace odráží verzi modelu, který bude odpovídat na požadavky na předpověď. Při každém přetrénování modelu byste ji měli aktualizovat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=119-124)]

### <a name="create-a-prediction-endpoint"></a>Vytvoření koncového bodu předpovědi

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=126-131)]

### <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Tato část skriptu nahraje testovací obrázek, odešle dotaz do koncového bodu modelu a vypíše data předpovědi do konzoly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=133-145)]

## <a name="run-the-application"></a>Spuštění aplikace

Když je aplikace spuštěná, mělo by se otevřít okno konzoly s následujícím výstupem:

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```
Pak můžete ověřit správné označení testovacího obrázku (ve složce **Images/Test/**) a správnost oblasti detekce. V tuto chvíli můžete aplikaci ukončit stisknutím libovolné klávesy.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Další postup

Právě jste viděli, jak se dají jednotlivé kroky procesu detekce objektů provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
