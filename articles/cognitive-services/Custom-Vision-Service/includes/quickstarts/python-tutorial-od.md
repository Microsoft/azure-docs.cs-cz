---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 0072f0ae862e6484649179626a34e6b28da7d3bd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94341028"
---
Začněte s knihovnou klienta Custom Vision pro Python. Pomocí těchto kroků nainstalujte balíček a vyzkoušejte ukázkový kód pro vytvoření modelu detekce objektu. Vytvoříte projekt, přidáte značky, provedete projekt a použijete adresu URL koncového bodu předpovědi projektu pro programové testování. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace pro rozpoznávání imagí.

> [!NOTE]
> Pokud chcete sestavit a vytvořit model detekce objektu _bez_ psaní kódu, Projděte si [pokyny na základě prohlížeče](../../get-started-build-detector.md) .

Použití klientské knihovny Custom Vision pro Python pro:

* Vytvoření nového projektu Custom Vision
* Přidat do projektu značky
* Nahrávání a označování obrázků
* Výuka projektu
* Publikovat aktuální iteraci
* Testování koncového bodu předpovědi

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Balíček (PyPi)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Ukázky](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision&languages=python)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" vytvořte prostředek Custom Vision vytvoření prostředku "  target="_blank"> Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste vytvořili prostředek pro školení a předpověď a získali své klíče a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k Custom Vision budete potřebovat klíč a koncový bod z prostředků, které vytvoříte. Vaše klíče a koncový bod budete vkládat do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

K napsání aplikace pro analýzu obrázků pomocí Custom Vision pro Python budete potřebovat klientskou knihovnu Custom Vision. Po instalaci Pythonu spusťte následující příkaz v PowerShellu nebo okně konzoly:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud Custom Vision prostředky, které jste vytvořili v části **předpoklady** , se úspěšně nasadily, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíče a koncový bod můžete najít na stránce **klíčů a koncových bodů** prostředků v části **Správa prostředků**. Budete muset získat jak klíče pro školení, tak pro předpověď.
>
> Hodnotu ID prostředku předpovědi najdete na kartě **Přehled** prostředku, která je uvedená jako **ID předplatného**.
>
> Nezapomeňte odebrat klíče z kódu, až budete hotovi, a nikdy je nezveřejňujte. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

## <a name="object-model"></a>Objektový model

|Název|Popis|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | Tato třída zpracovává vytváření, školení a publikování vašich modelů. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| Tato třída zpracovává dotazování vašich modelů pro detekci objektů předpovědi.|
|[ImagePrediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| Tato třída definuje jednu předpověď objektu na jednom obrázku. Obsahuje vlastnosti pro ID objektu a název, umístění ohraničovacího rámečku objektu a hodnocení spolehlivosti.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak pomocí Custom Vision klientské knihovny pro Python provést následující akce:

* [Ověření klienta](#authenticate-the-client)
* [Vytvoření nového projektu Custom Vision](#create-a-new-custom-vision-project)
* [Přidat do projektu značky](#add-tags-to-the-project)
* [Nahrávání a označování obrázků](#upload-and-tag-images)
* [Výuka projektu](#train-the-project)
* [Publikovat aktuální iteraci](#publish-the-current-iteration)
* [Testování koncového bodu předpovědi](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci školení a předpovědi klienta s vaším koncovým bodem a klíči. Vytvořte objekty **ApiKeyServiceClientCredentials** pomocí vašich klíčů a použijte je u svého koncového bodu k vytvoření objektu [CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) a [CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Vytvoření nového projektu Custom Vision

Přidáním následujícího kódu do svého skriptu vytvořte nový projekt služby Custom Vision. 

Chcete-li určit další možnosti při vytváření projektu, podívejte se do metody [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) (vysvětleno v průvodci [vytvořením webového portálu detektoru](../../get-started-build-detector.md) ).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Přidat do projektu značky

Chcete-li v projektu vytvořit značky objektů, přidejte následující kód:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Nejdřív Stáhněte ukázkové image pro tento projekt. Uložte obsah [složky Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) do místního zařízení.

Když označíte obrázky v projektech detekce objektů, je nutné zadat oblast každého tagovaného objektu pomocí normalizovaných souřadnic. Následující kód spojí každý z ukázkových imagí se svou označenou oblastí. Oblasti určují ohraničující rámeček s normalizovanými souřadnicemi, které jsou v tomto pořadí: vlevo, nahoře, šířka, výška.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Pokud nemáte k označení souřadnic oblastí k dispozici nástroj pro kliknutí a přetažení, můžete použít webové uživatelské rozhraní na adrese [Customvision.AI](https://www.customvision.ai/). V tomto příkladu jsou souřadnice již poskytovány.

Pak použijte tuto mapu přidružení k nahrání jednotlivých ukázkových imagí s souřadnicemi oblastí (do jedné dávky můžete nahrát až 64 obrázků). Přidejte následující kód.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Budete muset změnit cestu k obrázkům na základě místa, kde jste dříve stáhli sadu Cognitive Services Python SDK Samples úložiště.

## <a name="train-the-project"></a>Výuka projektu

Tento kód vytvoří první iteraci modelu předpovědi. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Výuka s vybranými značkami
>
> Volitelně můžete naučit pouze podmnožinu použitých značek. Můžete to chtít udělat, pokud jste ještě nepoužili dostatek určitých značek, ale máte dost dalších. Ve **[train_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** volání nastavte volitelný parametr *selected_tags* na seznam řetězců ID značek, které chcete použít. Model bude vlakem rozpoznávat pouze značky v tomto seznamu.

## <a name="publish-the-current-iteration"></a>Publikovat aktuální iteraci

Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována. Následující kód provede aktuální iteraci modelu, který je k dispozici pro dotazování. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Testování koncového bodu předpovědi

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru následující kód:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Spuštění aplikace

Spusťte *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

V konzole by se měl zobrazit výstup aplikace. Pak můžete ověřit, zda je testovací obrázek (nalezen v **<base_image_location>/images/test** ) správně označen a zda je oblast detekce správná. Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste provedli všechny kroky procesu detekce objektů v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)

* [Co je Custom Vision?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py) .
* [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)