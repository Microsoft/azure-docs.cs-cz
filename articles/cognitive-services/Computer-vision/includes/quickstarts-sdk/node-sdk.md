---
title: 'Rychlý Start: Počítačové zpracování obrazu klientskou knihovnu pro Node.js'
description: Začínáme s klientskou knihovnou Počítačové zpracování obrazu pro Node.js s tímto rychlým startem
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: e0767e50ac6d88922281b10389b77c5cf0c481be
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96912162"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny Počítačové zpracování obrazu můžete:

* Analyzujte obrázek pro značky, text Description, obličeje, obsah pro dospělé a další.
* Přečtěte si vytištěné a ručně psaný text pomocí rozhraní API pro čtení.

[Referenční dokumentace](/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze [Node.js](https://nodejs.org/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
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

Nainstalujte `ms-rest-azure` balíček a `@azure/cognitiveservices-computervision` npm:

```console
npm install @azure/cognitiveservices-computervision
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

Vytvořte nový soubor *index.js* a otevřete ho v textovém editoru. Přidejte následující příkazy importu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud prostředek [název produktu], který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Počítačové zpracování obrazu Node.js SDK.

|Název|Description|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoříte jeho instanci s informacemi o předplatném a použijete ho k provádění většiny operací s imagí.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot **VisualFeatureTypes** . |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Počítačové zpracování obrazu klientské knihovny pro Node.js:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta


Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) s klíčem a koncovým bodem a použijte ho k vytvoření objektu [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Pak definujte funkci `computerVision` a deklarujte asynchronní řadu s primární funkcí a funkcí zpětného volání. Do primární funkce přidáte svůj kód pro rychlý Start a zavoláte `computerVision` se do dolní části skriptu. Zbytek kódu v tomto rychlém startu přechází dovnitř `computerVision` funkce.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#analyze-an-image) [narazil na problém](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analýza obrázku

Kód v této části analyzuje vzdálené image pro extrakci různých vizuálních funkcí. Tyto operace můžete provádět jako součást metody **analyzeImage** objektu klienta, nebo je můžete volat pomocí individuálních metod. Podrobnosti najdete v [referenční dokumentaci](/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) .

> [!NOTE]
> Můžete také analyzovat místní bitovou kopii. Podívejte se na metody [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) , jako je například **analyzeImageInStream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) , kde najdete scénáře týkající se místních imagí.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další podrobnosti najdete v tématu [popisujícím obrázky](../../concept-describing-images.md) .

Nejdřív definujte adresu URL obrázku, který se má analyzovat:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Pak přidejte následující kód, který získá popis obrázku a vytiskne ho do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázku

Následující kód získá zjištěnou kategorii obrázku. Další podrobnosti najdete v tématu [kategorizace imagí](../../concept-categorizing-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definujte pomocnou funkci `formatCategories` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Získat značky obrázku

Následující kód získá sadu zjištěných značek v obrázku. Další podrobnosti najdete v tématu [značky obsahu](../../concept-tagging-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definujte pomocnou funkci `formatTags` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detekovat objekty

Následující kód detekuje běžné objekty v imagi a vytiskne je do konzoly. Další podrobnosti najdete v tématu věnovaném [detekci objektů](../../concept-object-detection.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definujte pomocnou funkci `formatRectObjects` pro vrácení souřadnic horní, levý, dolní a pravé, spolu s šířkou a výškou.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detekovat značky

Následující kód detekuje firemní značky a loga v imagi a vytiskne je do konzoly. Další podrobnosti najdete v tématu [rozpoznávání značek](../../concept-brand-detection.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plošky v obrázku s jejich souřadnicemi obdélníku a vyberte možnost atributy obličeje. Další podrobnosti najdete v tématu [rozpoznávání tváře](../../concept-detecting-faces.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definujte pomocnou funkci `formatRectFaces` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující kód vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další podrobnosti najdete v článku [obsah pro dospělé, pikantní a gorie](../../concept-detecting-adult-content.md) .

Zadejte adresu URL obrázku, který se má použít:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Pak přidejte následující kód pro zjištění obsahu pro dospělé a vytiskněte výsledky do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další podrobnosti najdete v tématu [Barevná schémata](../../concept-detecting-color-schemes.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definujte pomocnou funkci `printColorScheme` pro tisk podrobností o barevném schématu do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové zpracování obrazu může použít specializovaný model k další analýze imagí. Další podrobnosti najdete v tématu [obsah specifický pro doménu](../../concept-detecting-domain-content.md) .

Nejdřív definujte adresu URL obrázku, který se má analyzovat:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definujte pomocnou funkci `formatRectDomain` pro analýzu dat umístění zjištěných orientačních bodů.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující kód Vytiskne informace o typu obrázku &mdash; , ať už se jedná o Klipart nebo kreslení čáry.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definujte pomocnou funkci `describeType` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Analyzoval jsem obrázek](?success=analyze-image#read-printed-and-handwritten-text) , který [jsem narazil na problém](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Počítačové zpracování obrazu může viditelný text extrahovat v obrázku a převést jej na datový proud. Tato ukázka používá operace čtení.

### <a name="set-up-test-images"></a>Nastavení testovacích imagí

Uložte odkaz na adresu URL obrázků, ze kterých chcete extrahovat text.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Můžete si také přečíst text z místní image. Podívejte se na metody [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) , jako je například **readInStream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) , kde najdete scénáře týkající se místních imagí.

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Definujte následující pole ve funkci, aby se načetly hodnoty stavu volání metody Read.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Přidejte následující kód, který volá `readTextFromURL` funkci pro dané obrázky.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definujte `readTextFromURL` funkci. Tato metoda volá metodu **Read** THES objektu klienta, která vrací ID operace a spustí asynchronní proces pro čtení obsahu obrázku. Pak pomocí ID operace zkontroluje stav operace, dokud nebudou vráceny výsledky. Vrátí extrahované výsledky.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Pak definujte pomocnou funkci `printRecText` , která vytiskne výsledky operací čtení do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Načetl jsem text](?success=read-printed-handwritten-text#run-the-application) , [který jsem narazil na problém](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://www.research.net/r/7QYZKHL?issue=clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Odkaz na rozhraní API pro počítačové zpracování obrazu (Node.js)](/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)


* [Co je počítačové zpracování obrazu?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).