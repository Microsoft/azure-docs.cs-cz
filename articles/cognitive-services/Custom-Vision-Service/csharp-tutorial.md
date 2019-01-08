---
title: 'Rychlý start: Vytvoření projektu klasifikace obrázků s vlastní Vision SDK proC#'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a vytvořte předpověď pomocí sady .NET SDK a jazyka C#.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: anroth
ms.openlocfilehash: aaffd5e47c8465d44c2dbcdde3f40cf6e6995e55
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079081"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Rychlý start: Vytvoření projektu klasifikace obrázků s využitím Custom Vision .NET SDK

Tento článek obsahuje informace a vzorový kód, které vám pomůžou začít s vytvořením modelu klasifikace obrázků pomocí sady Custom Vision SDK a jazyka C#. Po jeho vytvoření můžete přidat značky, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v .NET. Pokud chcete procesem vytvoření a používání modelu klasifikace projít _bez_ kódu, přečtěte si místo toho [pokyny s využitím prohlížeče](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Požadavky
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)


## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získání sady Custom Vision SDK a vzorového kódu
K napsání aplikace v .NET, která využívá službu Custom Vision, budete potřebovat balíčky NuGet pro službu Custom Vision. Tyto balíčky jsou součástí ukázkového projektu, který si stáhnete, ale tady je můžete získat samostatně.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Přejděte do složky **CustomVision/ImageClassification** a otevřete soubor _ImageClassification.csproj_ v sadě Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu je účelem klasifikátoru určit, jestli je strom __jedlovec__ nebo __sakura__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Vysvětlení kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. Do odpovídajících definic v metodě **Main** vložte své klíče předplatného.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Parametr koncový bod by měl odkazovat na oblasti, kde se skupina prostředků Azure obsahující prostředky Custom Vision vytvořil v. V tomto příkladu budeme předpokládat oblasti střed USA – jih a použít:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

Následující řádky kódu provádějí primární funkce projektu.

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision

Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Součástí tohoto projektu jsou i obrázky. Odkazuje se na ně v metodě **LoadImagesFromDisk** v souboru _Program.cs_.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier"></a>Trénování klasifikátoru

Tento kód vytvoří první iteraci v projektu a označí ji jako výchozí iteraci. Výchozí iterace odráží verzi modelu, který bude odpovídat na požadavky na předpověď. Při každém přetrénování modelu byste ji měli aktualizovat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=57-73)]

### <a name="set-the-prediction-endpoint"></a>Nastavení koncového bodu předpovědi

Koncový bod předpovědi je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace.
 
[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=77-82)]
 
### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Odeslání obrázku do výchozího koncového bodu předpovědi

V tomto skriptu se v metodě **LoadImagesFromDisk** načte testovací obrázek a v konzole se zobrazí výstup předpovědi modelu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=84-92)]

## <a name="run-the-application"></a>Spuštění aplikace

Když je aplikace spuštěná, mělo by se otevřít okno konzoly s následujícím výstupem:

```
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Pak můžete ověřit správné označení testovacího obrázku (ve složce **Images/Test/**). Aplikaci ukončete stisknutím libovolné klávesy. Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další postup

Právě jste viděli, jak se dají jednotlivé kroky procesu klasifikace obrázků provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)