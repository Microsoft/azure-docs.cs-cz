---
title: 'Rychlý start: Vytvoření projektu zjišťování objektu se vlastní Vision SDK pro Javu'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a detekujte objekty pomocí sady Java SDK.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 8012dd484e769041ea235c33c334bfbbf8580800
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207645"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Rychlý start: Vytvoření projektu zjišťování objektu se vlastní Vision SDK pro Javu

Tento článek obsahuje informace a vzorový kód, které vám pomůžou začít s vytvořením modelu detekce objektů pomocí sady Custom Vision SDK a Javy. Po jeho vytvoření můžete přidat označené oblasti, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v Javě. 

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

Tento projekt Javy vytvoří nový projekt detekce objektů pomocí služby Custom Vision s názvem __Sample Java OD Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu je účelem klasifikátoru určit, jestli je strom __jedlovec__ nebo __sakura__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Program je nakonfigurovaný tak, aby ukládal klíčová data do proměnných prostředí. Tyto proměnné můžete nastavit tak, že v PowerShellu přejdete do složky **Vision/CustomVision**. Potom zadejte tyto příkazy:

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Vysvětlení kódu

Ve svém prostředí Java IDE načtěte projekt `Vision/CustomVision` a otevřete soubor _CustomVisionSamples.java_. Vyhledejte metodu **runSample** a odkomentujte volání metody **ImageClassification_Sample** &mdash; tím se spustí scénář klasifikace obrázků, kterým se tato příručka nezabývá. Metoda **ObjectDetection_Sample** implementuje primární funkce tohoto rychlého startu. Přejděte k její definici a prozkoumejte kód. 

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision Service

Přejděte k bloku kódu, který vytvoří klienta trénování a projekt detekce objektů. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Přidání značek do projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Při označování obrázků v projektech detekce obrázků je potřeba zadat oblast každého označeného objektu pomocí normalizovaných souřadnic. Přejděte k definici mapy `regionMap`. Tento kód přidruží jednotlivé ukázkové obrázky k odpovídající označené oblasti.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Potom přeskočte k bloku kódu, který přidá obrázky do projektu. Obrázky se načtou ze složky **src/main/resources** projektu a s odpovídajícími značkami a souřadnicemi oblastí se nahrají do služby.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

Předchozí fragment kódu používá dvě pomocné funkce, které obrázky načítají jako streamy prostředků a nahrávají je do služby.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project"></a>Trénování projektu

Tento kód vytvoří první iteraci v projektu a označí ji jako výchozí iteraci. Výchozí iterace odráží verzi modelu, který bude odpovídat na požadavky na předpověď. Při každém přetrénování modelu byste ji měli aktualizovat.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Koncový bod předpovědi, který zde představuje objekt `predictor`, je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace. V této ukázce se `predictor` definuje někde jinde s využitím proměnné prostředí s klíčem předpovědi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Spuštění aplikace

Pokud chcete řešení zkompilovat a spustit pomocí nástroje Maven, spusťte v PowerShellu v adresáři projektu následující příkaz:

```PowerShell
mvn compile exec:java
```

Ve výstupu konzoly zobrazte výsledky protokolování a předpovědi. Pak můžete ověřit správné označení testovacího obrázku a správnost oblasti detekce.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Další postup

Právě jste viděli, jak se dají jednotlivé kroky procesu detekce objektů provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
