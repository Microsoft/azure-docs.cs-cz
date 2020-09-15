---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: 8af5d8ad2ed4d623d7128ffa5fba3c9e3647a057
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533223"
---
V tomto článku se dozvíte, jak začít používat Custom Vision klientské knihovny s jazykem C# k sestavení modelu detekce objektu. Po vytvoření můžete přidat tagované oblasti, nahrát obrázky, naučit projekt, získat výchozí adresu URL koncového bodu předpovědi projektu a použít koncový bod pro programové testování obrázku. Použijte tento příklad jako šablonu pro vytvoření vlastní aplikace .NET. 

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Získat Custom Vision klientskou knihovnu a ukázkový kód

K napsání aplikace .NET, která používá Custom Vision, budete potřebovat Custom Vision balíčky NuGet. Tyto balíčky jsou součástí ukázkového projektu, který budete stahovat, ale můžete k nim přistupovat jednotlivě.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Přejděte do složky **CustomVision/ObjectDetection** a otevřete _ObjectDetection. csproj_ v aplikaci Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování modelu detekce objektů. V tomto projektu se model trénuje k detekování vidliček a nůžek na obrázcích.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>Kontrola kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. [Vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro vaše školicí a předpovědní klíče s názvem `CUSTOM_VISION_TRAINING_KEY` a v `CUSTOM_VISION_PREDICTION_KEY` uvedeném pořadí. Skript bude hledat tyto proměnné.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Adresu URL koncového bodu si také můžete stáhnout ze stránky nastavení na webu Custom Vision. Uložte ji do proměnné prostředí s názvem `CUSTOM_VISION_ENDPOINT`. Skript uloží odkaz na něj v kořenovém adresáři vaší třídy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

## <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu Custom Vision Service

Tato další část kódu vytvoří projekt detekce objektů. Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. Chcete-li určit další možnosti při vytváření projektu, viz metoda [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) (vysvětlení najdete v průvodci [vytvořením webového portálu detektoru](../../get-started-build-detector.md) ).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Přidat do projektu značky

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Když označíte obrázky v projektech detekce objektů, je nutné zadat oblast každého tagovaného objektu pomocí normalizovaných souřadnic. Následující kód spojí každý z ukázkových imagí se svou označenou oblastí.

> [!NOTE]
> Pokud nemáte k označení souřadnic oblastí k dispozici nástroj pro kliknutí a přetažení, můžete použít webové uživatelské rozhraní na adrese [Customvision.AI](https://www.customvision.ai/). V tomto příkladu jsou souřadnice již poskytovány.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Potom se pomocí této mapy přidružení nahrají jednotlivé ukázkové obrázky s odpovídajícími souřadnicemi oblasti. Do jedné dávky můžete nahrát až 64 imagí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

V tomto okamžiku jste nahráli všechny snímky ukázek a označili je každou z nich (**rozvětvení** nebo **nůžky**) s přiřazeným obdélníkem pixelů.

## <a name="train-the-project"></a>Výuka projektu

Tento kód vytvoří první výukovou iteraci v projektu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Výuka s vybranými značkami
>
> Volitelně můžete naučit pouze podmnožinu použitých značek. Můžete to chtít udělat, pokud jste ještě nepoužili dostatek určitých značek, ale máte dost dalších. V volání [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) použijte parametr *trainingParameters* . Vytvořte [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true) a nastavte jeho vlastnost **SelectedTags** na seznam identifikátorů značek, které chcete použít. Model bude vlakem rozpoznávat pouze značky v tomto seznamu.

## <a name="publish-the-current-iteration"></a>Publikovat aktuální iteraci

Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

## <a name="create-a-prediction-endpoint"></a>Vytvoření koncového bodu předpovědi

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

## <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Tato část skriptu načte testovací image, provede dotaz na koncový bod modelu a výstupí data předpovědi do konzoly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění aplikace by měl otevřít okno konzoly a zapsat následující výstup:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Pak můžete ověřit, zda je testovací obrázek (nalezen v **obrázcích/testu/** ) správně označen a zda je oblast detekce správná. V tomto okamžiku můžete stisknutím libovolné klávesy ukončit aplikaci.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste viděli, jak provést všechny kroky procesu detekce objektů v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)
