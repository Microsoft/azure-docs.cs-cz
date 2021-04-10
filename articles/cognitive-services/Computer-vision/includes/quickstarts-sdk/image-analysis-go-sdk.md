---
title: 'Rychlý Start: Klientská knihovna pro analýzu obrázků pro přejít'
titleSuffix: Azure Cognitive Services
description: Začněte s tímto rychlým startem v části Začínáme s klientskou knihovnou pro analýzu imagí.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 6a0d13dbc0beb90e0e276c5f594ff09c721f06f8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073696"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny pro analýzu obrázků můžete analyzovat obrázek pro značky, text popisu, obličeje, obsah pro dospělé a další.

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Balíček](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-go-project-directory"></a>Vytvořit adresář projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt na cestách s názvem `my-app` a přejděte na něj.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Váš pracovní prostor bude obsahovat tři složky:

* **Src** – tento adresář bude obsahovat zdrojový kód a balíčky. Všechny balíčky nainstalované pomocí `go get` příkazu se budou nacházet v tomto adresáři.
* **pkg** – tento adresář bude obsahovat kompilované objekty balíčku pro přechod. Všechny tyto soubory mají `.a` rozšíření.
* **bin** – tento adresář bude obsahovat binární spustitelné soubory, které se vytvoří při spuštění `go install` .

> [!TIP]
> Další informace o struktuře pracovního prostoru přejděte v [dokumentaci jazyka přejděte](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro přejít

Dále nainstalujte klientskou knihovnu pro přejít:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

nebo pokud používáte DEP, v rámci vašeho úložiště úložišť:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Vytvoření aplikace v cestách 

Dále vytvořte soubor v adresáři **Src** s názvem `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Otevřete `sample-app.go` v upřednostňovaném integrovaném vývojovém prostředí (IDE) nebo textovém editoru. Pak přidejte název balíčku a importujte následující knihovny:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Deklarujte také kontext v kořenovém adresáři vašeho skriptu. K provedení většiny volání funkce analýzy obrázků budete potřebovat tento objekt:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Dále začnete přidávat kód pro provádění různých Počítačové zpracování obrazuch operací.

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady imagí pro analýzu obrázků.

|Název|Description|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu, jako je například analýza obrázku a čtení textu. Vytvoříte jeho instanci s informacemi o předplatném a použijete ho k provádění většiny operací s imagí.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Tento typ obsahuje výsledky volání funkce **AnalyzeImage** . Pro každou funkci konkrétní kategorie existují podobné typy.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Tento typ definuje různé druhy analýz obrázků, které je možné provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro analýzu imagí pro:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento krok předpokládá, že jste [vytvořili proměnné prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro svůj počítačové zpracování obrazu klíč a koncový bod s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` v uvedeném pořadí.

Vytvořte `main` funkci a přidejte do ní následující kód, který vytvoří instanci klienta s vaším koncovým bodem a klíčem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#analyze-an-image) [narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód používá objekt klienta k analýze vzdálené image a k vytištění výsledků do konzoly. Můžete získat textový popis, kategorizaci, seznam značek, zjištěných objektů, zjištěných značek, zjištěné plošky, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

### <a name="set-up-test-image"></a>Nastavit testovací image

Nejdřív uložte odkaz na adresu URL obrázku, který chcete analyzovat. Umístěte ho do `main` funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Můžete také analyzovat místní bitovou kopii. Podívejte se na metody [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) , jako je například **DescribeImageInStream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) , kde najdete scénáře týkající se místních imagí.

### <a name="specify-visual-features"></a>Zadat vizuální funkce

Následující volání funkce extrahují různé vizuální funkce z ukázkové image. Tyto funkce definujete v následujících oddílech.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Získat popis obrázku

Následující funkce získá seznam generovaných titulků pro obrázek. Další informace o popisu obrázku najdete v tématu [popisujícím obrázky](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázku

Následující funkce získá zjištěnou kategorii obrázku. Další informace najdete v tématu [kategorizace imagí](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Získat značky obrázku

Následující funkce získá sadu zjištěných značek v obrázku. Další informace najdete v tématu [značky obsahu](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detekovat objekty

Následující funkce detekuje běžné objekty v imagi a tiskne je do konzoly. Další informace najdete v tématu [detekce objektů](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detekovat značky

Následující kód detekuje firemní značky a loga v imagi a vytiskne je do konzoly. Další informace najdete v [rozpoznávání značek](../../concept-brand-detection.md).

Nejprve deklarujte odkaz na nový obrázek v rámci vaší `main` funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Následující kód definuje funkci detekce značky.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující funkce vrátí zjištěné plošky v obrázku s jejich souřadnicemi obdélníku a určitými atributy obličeje. Další informace najdete v tématu [rozpoznávání tváře](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující funkce vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další informace najdete v článku [obsah pro dospělé, pikantní a gorie](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující funkce vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další informace najdete v tématu [Barevná schémata](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Analýza obrázků může používat specializované modely k dalšímu analýze imagí. Další informace najdete v tématu [obsah specifický pro doménu](../../concept-detecting-domain-content.md). 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující funkce vytiskne informace o typu obrázku &mdash; , ať už se jedná o Klipart nebo kreslení čáry.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Analyzoval jsem obrázek](?success=analyze-image#run-the-application) , který [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)


## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí `go run` příkazu.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nainstalovat knihovnu klienta pro analýzu imagí a provést základní volání analýzy obrázků. V dalším kroku se dozvíte víc o funkcích rozhraní API pro analýzu.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro analýzu](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Přehled analýzy obrázků](../../overview-image-analysis.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

