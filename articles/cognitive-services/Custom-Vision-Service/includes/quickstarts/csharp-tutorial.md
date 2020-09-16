---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 28bcaa898bbf6621295bc5096a924d39073e727e
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604953"
---
Tato příručka poskytuje pokyny a ukázkový kód, který vám pomůže začít používat Custom Vision klientské knihovny pro jazyk C# k sestavení modelu klasifikace imagí. Vytvoříte projekt, přidáte značky, provedete projekt a použijete adresu URL koncového bodu předpovědi projektu pro programové testování. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace pro rozpoznávání imagí.

> [!NOTE]
> Pokud chcete sestavit model klasifikace _bez_ psaní kódu, prostudujte si místo toho doprovodné materiály pro [prohlížeč](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Instalace klientské knihovny Custom Vision

K napsání aplikace pro analýzu obrázků pomocí Custom Vision pro .NET budete potřebovat balíčky Custom Vision NuGet. Tyto balíčky jsou součástí ukázkového projektu, který budete stahovat, ale můžete k nim přistupovat jednotlivě.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Přejděte do složky **CustomVision/ImageClassification** a otevřete soubor _ImageClassification.csproj_ v sadě Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu je účelem klasifikátoru určit, jestli je strom __jedlovec__ nebo __sakura__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>Kontrola kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. [Vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro vaše školicí a předpovědní klíče s názvem `CUSTOM_VISION_TRAINING_KEY` a v `CUSTOM_VISION_PREDICTION_KEY` uvedeném pořadí. Skript bude hledat tyto proměnné.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Adresu URL koncového bodu si také můžete stáhnout ze stránky nastavení na webu Custom Vision. Uložte ji do proměnné prostředí s názvem `CUSTOM_VISION_ENDPOINT`. Skript uloží odkaz na něj v kořenovém adresáři vaší třídy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Následující řádky kódu provádějí primární funkce projektu.

## <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision

Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. Chcete-li určit další možnosti při vytváření projektu, viz metoda [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) (vysvětlení najdete v průvodci [vytvořením](../../getting-started-build-a-classifier.md) webového portálu třídění).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Součástí tohoto projektu jsou i obrázky. Odkazuje se na ně v metodě **LoadImagesFromDisk** v souboru _Program.cs_. Do jedné dávky můžete nahrát až 64 imagí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

## <a name="train-the-classifier-and-publish"></a>Výuka třídění a publikování

Tento kód vytvoří první iteraci modelu předpovědi a pak tuto iteraci publikuje do koncového bodu předpovědi. Můžete použít název iterace k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

## <a name="set-the-prediction-endpoint"></a>Nastavení koncového bodu předpovědi

Koncový bod předpovědi je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

## <a name="test-the-prediction-endpoint"></a>Testování koncového bodu předpovědi

V tomto skriptu se v metodě **LoadImagesFromDisk** načte testovací obrázek a v konzole se zobrazí výstup předpovědi modelu. Hodnota `publishedModelName` proměnné by měla odpovídat hodnotě Publikováno jako na kartě **výkon** webu Custom Vision. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění aplikace by měl otevřít okno konzoly a zapsat následující výstup:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Pak můžete ověřit správné označení testovacího obrázku (ve složce **Images/Test/**). Aplikaci ukončete stisknutím libovolné klávesy. Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste provedli všechny kroky procesu klasifikace obrázků v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)

* [Co je Custom Vision?](../../overview.md)
* [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)