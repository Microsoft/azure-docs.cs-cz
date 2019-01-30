---
title: 'Rychlý start: Vytvoření projektu klasifikace obrázků se vlastní Vision SDK pro Javu'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a vytvořte předpověď pomocí sady Java SDK.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 6a05620d35ddf1a382260fd730251f1cdcedb910
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213510"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Rychlý start: Vytvoření projektu klasifikace obrázků se vlastní Vision SDK pro Javu

Tento článek obsahuje informace a vzorový kód, které vám pomůžou začít s vytvořením modelu klasifikace obrázků pomocí sady Custom Vision Java SDK. Po jeho vytvoření můžete přidat značky, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v Javě. Pokud chcete procesem vytvoření a používání modelu klasifikace projít _bez_ kódu, přečtěte si místo toho [pokyny s využitím prohlížeče](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Požadavky
- Libovolné prostředí Java IDE
- Nainstalovaná sada [JDK 7 nebo 8](https://aka.ms/azure-jdks)
- Nainstalovaný Maven


## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získání sady Custom Vision SDK a vzorového kódu
K napsání aplikace v Javě, která využívá službu Custom Vision, budete potřebovat balíčky maven pro službu Custom Vision. Tyto balíčky jsou součástí ukázkového projektu, který si stáhnete, ale tady je můžete získat samostatně.

Sadu Custom Vision SDK můžete nainstalovat z centrálního úložiště Mavenu:
* [Sada SDK pro trénování](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Sada SDK pro předpověď](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v sadě Java SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Přejděte do složky **Vision/CustomVision/**.

Tento projekt Javy vytvoří nový projekt klasifikace obrázků pomocí služby Custom Vision s názvem __Sample Java Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu je účelem klasifikátoru určit, jestli je strom __jedlovec__ nebo __sakura__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Program je nakonfigurovaný tak, aby ukládal klíčová data do proměnných prostředí. Tyto proměnné můžete nastavit tak, že v PowerShellu přejdete do složky **Vision/CustomVision**. Potom zadejte tyto příkazy:

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Vysvětlení kódu

Ve svém prostředí Java IDE načtěte projekt `Vision/CustomVision` a otevřete soubor _CustomVisionSamples.java_. Vyhledejte metodu **runSample** a odkomentujte volání metody **ObjectDetection_Sample** &mdash; tím se spustí scénář detekce objektů, kterým se tato příručka nezabývá. Metoda **ImageClassification_Sample** implementuje primární funkce tohoto příkladu. Přejděte k její definici a prozkoumejte kód. 

### <a name="create-a-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision Service

Tato první část kódu vytvoří projekt klasifikace obrázků. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=57-63)]

### <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=65-74)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Ukázkové obrázky se nacházejí ve složce **src/main/resources** projektu. Odtud se načtou a s odpovídajícími značkami se nahrají do služby.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=76-87)]

Předchozí fragment kódu používá dvě pomocné funkce, které obrázky načítají jako streamy prostředků a nahrávají je do služby.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-classifier"></a>Trénování klasifikátoru

Tento kód vytvoří první iteraci v projektu a označí ji jako výchozí iteraci. Výchozí iterace odráží verzi modelu, který bude odpovídat na požadavky na předpověď. Při každém přetrénování modelu byste ji měli aktualizovat.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=89-99)]

### <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Koncový bod předpovědi, který zde představuje objekt `predictor`, je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace. V této ukázce se `predictor` definuje někde jinde s využitím proměnné prostředí s klíčem předpovědi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=108-120)]

## <a name="run-the-application"></a>Spuštění aplikace

Pokud chcete řešení zkompilovat a spustit pomocí nástroje Maven, spusťte v PowerShellu v adresáři projektu následující příkaz:

```PowerShell
mvn compile exec:java
```

Výstup aplikace v konzole by měl být podobný následujícímu textu:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Potom můžete ověřit, jestli je testovací předpověď (několik posledních řádků výstupu) správná.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další postup

Právě jste viděli, jak se dají jednotlivé kroky procesu klasifikace obrázků provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
