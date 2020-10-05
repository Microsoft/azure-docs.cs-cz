---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: f4b64c25bff93683c79aa1aa3eb556988c798cb0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90604949"
---
Tato příručka poskytuje pokyny a ukázkový kód, který vám pomůže začít používat Custom Vision klientské knihovny pro jazyk Java k sestavení modelu klasifikace imagí. Vytvoříte projekt, přidáte značky, provedete projekt a použijete adresu URL koncového bodu předpovědi projektu pro programové testování. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace pro rozpoznávání imagí.

> [!NOTE]
> Pokud chcete sestavit model klasifikace _bez_ psaní kódu, prostudujte si místo toho doprovodné materiály pro [prohlížeč](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Předpoklady

- Libovolné prostředí Java IDE
- Nainstalovaná sada [JDK 7 nebo 8](https://aka.ms/azure-jdks)
- [Maven](https://maven.apache.org/) nainstalované
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>Získání klientské knihovny Custom Vision

K napsání aplikace pro analýzu obrázků pomocí Custom Vision pro jazyk Java budete potřebovat balíčky Custom Vision Maven. Tyto balíčky jsou součástí ukázkového projektu, který budete stahovat, ale můžete k nim přistupovat jednotlivě.

Knihovnu klienta Custom Vision můžete najít v centrálním úložišti Maven:

- [Sada SDK pro trénování](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Sada SDK pro předpověď](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v sadě Java SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Přejděte do složky **Vision/CustomVision/**.

Tento projekt Javy vytvoří nový projekt klasifikace obrázků pomocí služby Custom Vision s názvem __Sample Java Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu je účelem klasifikátoru určit, jestli je strom __jedlovec__ nebo __sakura__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Program je nakonfigurován tak, aby odkazoval na data klíče jako proměnné prostředí. Přejděte do složky **Vision/CustomVision** a zadejte následující příkazy PowerShellu pro nastavení proměnných prostředí. 

> [!NOTE]
> Pokud používáte jiný operační systém než Windows, přečtěte si téma [konfigurace proměnných prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) pro pokyny.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>Kontrola kódu

Ve svém prostředí Java IDE načtěte projekt `Vision/CustomVision` a otevřete soubor _CustomVisionSamples.java_. Vyhledejte metodu **runSample** a odkomentujte **ObjectDetection_Sample** metoda volání &mdash; této metody provádí scénář detekce objektu, který není popsaný v této příručce. Metoda **ImageClassification_Sample** implementuje primární funkce tohoto příkladu. Přejděte k její definici a prozkoumejte kód.

## <a name="create-a-custom-vision-project"></a>Vytvoření projektu Custom Vision

Tato první část kódu vytvoří projekt klasifikace obrázků. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. Pokud vytvoříte projekt (vysvětlení najdete v průvodci [vytvořením](../../getting-started-build-a-classifier.md) webového portálu třídění), podívejte se na přetížení metod [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) a určete další možnosti.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Ukázkové obrázky se nacházejí ve složce **src/main/resources** projektu. Odtud se načtou a s odpovídajícími značkami se nahrají do služby.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Předchozí fragment kódu dělá použití dvou pomocných funkcí, které načítají obrázky jako proudy prostředků a odesílají je do služby (do jedné dávky můžete nahrát až 64 imagí).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-and-publish-the-project"></a>Školení a publikování projektu

Tento kód vytvoří první iteraci modelu předpovědi a pak tuto iteraci publikuje do koncového bodu předpovědi. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

## <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Koncový bod předpovědi, který zde představuje objekt `predictor`, je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace. V této ukázce se `predictor` definuje někde jinde s využitím proměnné prostředí s klíčem předpovědi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Spuštění aplikace

Pro zkompilování a spuštění řešení pomocí Maven přejděte do adresáře projektu (**Vision/CustomVision**) na příkazovém řádku a spusťte příkaz run:

```bash
mvn compile exec:java
```

Výstup aplikace v konzole by měl být podobný následujícímu textu:

```console
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

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste viděli, jak se každý krok procesu detekce objektů dá provést v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)

* [Co je Custom Vision?](../../overview.md)
* * [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)