---
title: 'Úvodní příručka: Klientská knihovna počítačového vidění pro soubor Node.js'
description: Začínáme s klientskou knihovnou Počítačové vidění pro soubor Node.js s tímto rychlým startem
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 234e2402287fd76bdfd96cb6884c3fd20e805d06
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136018"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [knihovny](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) [Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [souboru Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure pro počítačové zpracování obrazu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro počítačové zpracování pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/).

Po získání klíče z vašeho zkušebního předplatného nebo prostředku [vytvořte proměnné](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) prostředí `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` pro adresu URL klíče a koncového bodu s názvem a příslušně.

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci `package.json` uzlu se souborem.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ms-rest-azure` `@azure/cognitiveservices-computervision` balíčky a balíčky NPM:

```console
npm install @azure/cognitiveservices-computervision
```

`package.json` Soubor aplikace bude aktualizován závislostmi.

### <a name="prepare-the-nodejs-script"></a>Příprava skriptu Node.js

Vytvořte nový soubor *index.js*a otevřete jej v textovém editoru. Přidejte následující příkazy importu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Potom definujte `computerVision` funkci a deklarujte asynchronní řadu s primární funkcí a funkcí zpětného volání. Přidáte kód rychlého startu do primární funkce `computerVision` a volání v dolní části skriptu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady SDK node.js modulu Computer Vision.

|Name (Název)|Popis|
|---|---|
| [Klient ComputerVision](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Tato třída je potřebná pro všechny funkce počítačového vidění. Můžete vytvořit konkretizovat s informacemi o předplatném a použít jej k většině operací image.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Tento výčet definuje různé typy analýzy obrázků, které lze provést ve standardní operaci Analyzovat. V závislosti na vašich potřebách zadáte sadu hodnot **VisualFeatureTypes.** |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Počítačové vidění pro soubor Node.js:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Čtení tištěného a ručně psaného textu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) s klíčem a koncovým bodem a použijte jej k vytvoření objektu [ComputerVisionClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analýza obrázku

Kód v této části analyzuje vzdálené obrázky extrahovat různé vizuální funkce. Tyto operace můžete provést jako součást **metody analyzeImage** objektu klienta, nebo je můžete volat pomocí jednotlivých metod. Podrobnosti naleznete v [referenční dokumentaci.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

> [!NOTE]
> Můžete také analyzovat místní obrázek. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) pro scénáře zahrnující místní image.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další podrobnosti [najdete v tématu Popis obrázků.](../../concept-describing-images.md)

Nejprve definujte adresu URL obrázku k analýze:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Pak přidejte následující kód, abyste získali popis obrázku a vytiskli jej do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázků

Následující kód získá zjištěnou kategorii obrázku. Další [podrobnosti najdete v tématu Kategorizace obrázků.](../../concept-categorizing-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definujte pomocnou `formatCategories`funkci :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Získat značky obrázků

Následující kód získá sadu zjištěných značek v obraze. Další podrobnosti najdete v [tématu Značky obsahu.](../../concept-tagging-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definujte pomocnou `formatTags`funkci :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detekce objektů

Následující kód detekuje běžné objekty v obraze a vytiskne je do konzoly. Další [podrobnosti najdete v tématu Detekce objektů.](../../concept-object-detection.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definujte pomocnou `formatRectObjects`funkci :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detekce značek

Následující kód detekuje podnikové značky a loga v obrázku a vytiskne je do konzoly. Další [podrobnosti najdete v tématu Detekce značek.](../../concept-brand-detection.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plochy v obraze s jejich obdélníkovými souřadnicemi a vybranými atributy plochy. Další [podrobnosti najdete v tématu Detekce obličejů.](../../concept-detecting-faces.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definujte pomocnou `formatRectFaces`funkci :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detekujte obsah pro dospělé, pikantní nebo krvavý

Následující kód vytiskne zjištěnou přítomnost obsahu pouze pro dospělé v obrázku. Další podrobnosti najdete [v tématu Dospělý, pikantní, krvavý obsah.](../../concept-detecting-adult-content.md)

Definujte adresu URL obrázku, který chcete použít:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Pak přidejte následující kód pro detekci obsahu pouze pro dospělé a vytisknout výsledky do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné barevné atributy v obraze, například dominantní barvy a barvy zvýraznění. Další podrobnosti najdete [v části Barevná schémata.](../../concept-detecting-color-schemes.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definujte pomocnou `printColorScheme` funkci pro tisk podrobností barevného schématu do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové vidění lze použít specializovaný model k další analýze obrázků. Další podrobnosti najdete [v tématu Obsah specifický pro doménu.](../../concept-detecting-domain-content.md)

Nejprve definujte adresu URL obrázku k analýze:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Následující kód analyzuje data o zjištěných orientačních bodů v obraze.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definujte pomocnou `formatRectDomain` funkci pro analýzu lokalizačních dat zjištěných orientačních bodů.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Získání typu obrázku

Následující kód vytiskne informace&mdash;o typu obrázku, ať už se jedná o kresbu klipartem nebo čarou.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definujte pomocnou `describeType`funkci :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Čtení tištěného a ručně psaného textu

Počítačové vidění může číst viditelný text v obraze a převést jej na datový proud znaků.

> [!NOTE]
> Můžete také číst text z místního obrázku. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) pro scénáře zahrnující místní image.

### <a name="set-up-test-images"></a>Nastavení testovacích obrázků

Uložte odkaz na adresu URL obrázků, ze kterých chcete extrahovat text.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Volání rozhraní API pro rozpoznávání

Přidejte níže uvedený kód, který volá `recognizeText` funkci pro dané obrázky.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definujte `recognizeText` funkci. To volá **recognizeText** metoda na objekt u klienta, který vrátí ID operace a spustí asynchronní proces ke čtení obsahu obrazu. Potom používá ID operace ke kontrole operace v intervalech jedné sekundy, dokud nejsou vráceny výsledky. Potom vrátí extrahované výsledky.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Potom definujte pomocnou `printRecText`funkci , která vytiskne výsledky operace Rozpoznat do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node` v souboru quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Reference rozhraní API pro počítačové zpracování obrazu (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Co je počítačové zpracování obrazu?](../../Home.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
