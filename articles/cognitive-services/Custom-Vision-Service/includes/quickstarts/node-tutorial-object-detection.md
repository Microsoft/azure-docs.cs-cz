---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 02e374e94e3d710ff70a89846c2a077f8dad2343
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106046"
---
Tato příručka poskytuje pokyny a ukázkový kód, který vám pomůže začít používat Custom Vision klientské knihovny pro Node.js sestavení modelu detekce objektu. Vytvoříte projekt, přidáte značky, provedete projekt a použijete adresu URL koncového bodu předpovědi projektu pro programové testování. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace pro rozpoznávání imagí.

> [!NOTE]
> Pokud chcete sestavit a vytvořit model detekce objektu _bez_ psaní kódu, Projděte si [pokyny na základě prohlížeče](../../get-started-build-detector.md) .

Pomocí klientské knihovny Custom Vision pro .NET:

* Vytvoření nového projektu Custom Vision
* Přidat do projektu značky
* Nahrávání a označování obrázků
* Výuka projektu
* Publikovat aktuální iteraci
* Testování koncového bodu předpovědi

Referenční dokumentace [(školení)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(předpověď)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Zdrojový kód knihovny [(školení)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(předpověď)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Ukázky balíčku (npm) ( [školení](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) ) [(předpovědi)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze [Node.js](https://nodejs.org/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" vytvořte prostředek Custom Vision vytvoření prostředku "  target="_blank"> Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste vytvořili prostředek pro školení a předpověď a získali své klíče a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k Custom Vision budete potřebovat klíč a koncový bod z prostředků, které vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

K napsání aplikace pro analýzu obrázků pomocí Custom Vision pro Node.js budete potřebovat balíčky Custom Vision NPM. Pokud je chcete nainstalovat, spusťte v PowerShellu následující příkaz:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

Vytvořte soubor s názvem `index.js` a importujte následující knihovny:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte proměnné pro koncový bod a klíče Azure prostředku. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud Custom Vision prostředky, které jste vytvořili v části **předpoklady** , se úspěšně nasadily, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíče a koncový bod můžete najít na stránce **klíčů a koncových bodů** prostředků v části **Správa prostředků**. Společně s koncovým bodem školicích prostředků budete muset získat jak školicí, tak i předpověď.
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../../cognitive-services/cognitive-services-security.md) Cognitive Services.

Pro asynchronní volání přidejte také pole pro název projektu a parametr Timeout.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Objektový model

|Název|Description|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Tato třída zpracovává vytváření, školení a publikování vašich modelů. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Tato třída zpracovává dotazování vašich modelů pro detekci objektů předpovědi.|
|[Předpovědi](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Toto rozhraní definuje jednu předpověď na jednom obrázku. Obsahuje vlastnosti pro ID a název objektu a hodnocení spolehlivosti.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Custom Vision klientské knihovny pro JavaScript:

* [Ověření klienta](#authenticate-the-client)
* [Vytvoření nového projektu Custom Vision](#create-a-new-custom-vision-project)
* [Přidat do projektu značky](#add-tags-to-the-project)
* [Nahrávání a označování obrázků](#upload-and-tag-images)
* [Výuka projektu](#train-the-project)
* [Publikovat aktuální iteraci](#publish-the-current-iteration)
* [Testování koncového bodu předpovědi](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci objektů klienta s vaším koncovým bodem a klíčem. Vytvořte objekt **ApiKeyCredentials** s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) a [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Přidat pomocnou funkci

Přidejte následující funkci, která usnadňuje více asynchronních volání. Použijete ho později v.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Vytvoření nového projektu Custom Vision

Spusťte novou funkci, která bude obsahovat všechna vaše volání funkcí Custom Vision. Přidejte následující kód k vytvoření nového projektu Custom Vision služby.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Nejdřív Stáhněte ukázkové image pro tento projekt. Uložte obsah [složky Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) do místního zařízení.

> [!NOTE]
> Potřebujete k dokončení školení širší sadu imagí? Studnicí, projekt garáže společnosti Microsoft, umožňuje shromažďovat a kupovat sady imagí pro účely školení. Po shromáždění imagí si je můžete stáhnout a pak je importovat do projektu Custom Vision obvyklým způsobem. Další informace najdete na [stránce studnicí](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) .

Ukázkové obrázky do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Tento kód nahraje jednotlivé obrázky s odpovídající značkou. Když označíte obrázky v projektech detekce objektů, je nutné zadat oblast každého tagovaného objektu pomocí normalizovaných souřadnic. Pro tento kurz jsou oblasti pevně zakódované vložené s kódem. Oblasti určují ohraničující rámeček s normalizovanými souřadnicemi, které jsou v tomto pořadí: vlevo, nahoře, šířka, výška. Do jedné dávky můžete nahrát až 64 imagí.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Budete muset změnit cestu k obrázkům () na základě toho, `sampleDataRoot` kam jste stáhli úložiště ukázek sady Cognitive Services Python SDK.

> [!NOTE]
> Pokud nemáte k označení souřadnic oblastí k dispozici nástroj pro kliknutí a přetažení, můžete použít webové uživatelské rozhraní na adrese [Customvision.AI](https://www.customvision.ai/). V tomto příkladu jsou souřadnice již poskytovány.


## <a name="train-the-project"></a>Výuka projektu

Tento kód vytvoří první iteraci modelu předpovědi. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Publikovat aktuální iteraci

Tento kód publikuje vyškolenou iteraci na koncový bod předpovědi. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testování koncového bodu předpovědi

Chcete-li odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte do funkce následující kód. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Potom zavřete funkci Custom Vision a zavolejte ji.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```shell
node index.js
```

V konzole by se měl zobrazit výstup aplikace. Pak můžete ověřit, zda je testovací obrázek (nalezen v **<sampleDataRoot> /test/**) správně označen a zda je oblast detekce správná. Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste provedli všechny kroky procesu detekce objektů v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)

* [Co je Custom Vision?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) .
* [Referenční dokumentace k sadě SDK (školení)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [Referenční dokumentace k sadě SDK (předpovědi)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
