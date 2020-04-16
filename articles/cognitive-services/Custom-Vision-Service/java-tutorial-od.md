---
title: 'Rychlý start: Vytvoření projektu detekce objektů pomocí sady Custom Vision SDK pro Javu'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a detekujte objekty pomocí sady Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 086e67d1058443a6f4afd615f2d2725aaf19a259
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403828"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Rychlý start: Vytvoření projektu detekce objektů pomocí sady Custom Vision SDK pro Javu

Tento článek ukazuje, jak začít používat vlastní vision SDK s Javou k vytvoření modelu detekce objektů. Po vytvoření můžete přidat tagované oblasti, nahrát obrázky, naučit projekt, získat výchozí adresu URL koncového bodu předpovědi projektu a použít koncový bod pro programové testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v Javě.

## <a name="prerequisites"></a>Požadavky

- Libovolné prostředí Java IDE
- Nainstalovaná sada [JDK 7 nebo 8](https://aka.ms/azure-jdks)
- [Maven](https://maven.apache.org/) nainstalován
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získat Custom Vision SDK a ukázkový kód

K napsání aplikace v Javě, která využívá službu Custom Vision, budete potřebovat balíčky maven pro službu Custom Vision. Tyto balíčky jsou zahrnuty v ukázkovém projektu, který si stáhnete, ale můžete k nim přistupovat jednotlivě zde.

Vlastní sada SDK Vision najdete v centrálním úložišti maven:
- [Sada SDK pro trénování](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Sada SDK pro předpověď](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v sadě Java SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Přejděte do složky **Vision/CustomVision/**.

Tento projekt Javy vytvoří nový projekt detekce objektů pomocí služby Custom Vision s názvem __Sample Java OD Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu třídění je určena k určení, zda objekt je **rozsvoz** nebo **nůžky**.

[!INCLUDE [get-keys](includes/get-keys.md)]

Program je nakonfigurován tak, aby odkazoval na klíčová data jako proměnné prostředí. Přejděte do složky **Vision/CustomVision** a zadejte následující příkazy prostředí PowerShell pro nastavení proměnných prostředí. 

> [!NOTE]
> Pokud používáte operační systém, který není windows, přečtěte si pokyny [v tématu Konfigurace proměnných prostředí.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication)

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Vysvětlení kódu

Ve svém prostředí Java IDE načtěte projekt `Vision/CustomVision` a otevřete soubor _CustomVisionSamples.java_. Najít **runSample** metoda a **ImageClassification_Sample** komentář z&mdash;ImageClassification_Sample volání metody tato metoda provede scénář klasifikace bitových obrázků, který není zahrnutv této příručce. Metoda **ObjectDetection_Sample** implementuje primární funkce tohoto rychlého startu. Přejděte k její definici a prozkoumejte kód. 

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu Custom Vision Service

Přejděte k bloku kódu, který vytvoří klienta trénování a projekt detekce objektů. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. Viz [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) přetížení metody určit další možnosti při vytváření projektu (vysvětleno v [sestavení detektoru](get-started-build-detector.md) webového portálu průvodce).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Přidání značek do projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Když označíte obrázky v projektech detekce objektů, je nutné zadat oblast každého tagovaného objektu pomocí normalizovaných souřadnic. Přejděte k definici mapy `regionMap`. Tento kód přidruží jednotlivé ukázkové obrázky k odpovídající označené oblasti.

> [!NOTE]
> Pokud nemáte nástroj pro klepnutí a přetažení, který by označoval souřadnice oblastí, můžete použít webové uživatelské uživatelské tlačítko na [Customvision.ai](https://www.customvision.ai/). V tomto příkladu jsou již k dispozici souřadnice.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Potom přeskočte k bloku kódu, který přidá obrázky do projektu. Obrázky se načtou ze složky **src/main/resources** projektu a s odpovídajícími značkami a souřadnicemi oblastí se nahrají do služby.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Předchozí fragment kódu využívá dvě pomocné funkce, které načítají obrázky jako datové proudy prostředků a nahrávají je do služby (můžete nahrát až 64 obrázků v jedné dávce).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Trénování projektu a publikování

Tento kód vytvoří první iteraci modelu předpověď a potom publikuje tuto iteraci do koncového bodu předpověď. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není k dispozici v koncovém bodu předpověď, dokud je publikován.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Koncový bod předpovědi, který zde představuje objekt `predictor`, je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace. V této ukázce se `predictor` definuje někde jinde s využitím proměnné prostředí s klíčem předpovědi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li zkompilovat a spustit řešení pomocí maven, přejděte do adresáře projektu (**Vision/CustomVision**) v příkazovém řádku a spusťte příkaz spustit:

```bash
mvn compile exec:java
```

Ve výstupu konzoly zobrazte výsledky protokolování a předpovědi. Pak můžete ověřit správné označení testovacího obrázku a správnost oblasti detekce.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste viděli, jak lze provést každý krok procesu detekce objektů v kódu. Tato ukázka provede jednu trénovací iteraci, ale často budete muset trénovat a testovat model vícekrát, aby byl přesnější. Následující průvodce školeníse zabývá klasifikací obrázků, ale jeho principy jsou podobné detekci objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
