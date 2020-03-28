---
title: 'Úvodní příručka: Klientská knihovna počítačového vidění pro Go'
titleSuffix: Azure Cognitive Services
description: Začněte s klientskou knihovnou Počítačové vize pro Přejít s tímto rychlým startem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136011"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Balíček zdrojového kódu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [knihovny](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Nejnovější verze [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure pro počítačové zpracování obrazu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro počítačové zpracování pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/).

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT`adresu URL klíče a koncového bodu s názvem a , resp.

### <a name="create-a-go-project-directory"></a>Vytvoření adresáře projektu Go

V okně konzoly (cmd, PowerShell, Terminál, Bash) vytvořte nový `my-app`pracovní prostor pro projekt Go s názvem a přejděte na něj.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Pracovní prostor bude obsahovat tři složky:

* **src** - Tento adresář bude obsahovat zdrojový kód a balíčky. Všechny balíčky `go get` nainstalované s příkazem budou v tomto adresáři.
* **pkg** - Tento adresář bude obsahovat zkompilované objekty balíčku Go. Všechny tyto soubory `.a` mají příponu.
* **bin** - Tento adresář bude obsahovat binární spustitelné `go install`soubory, které jsou vytvořeny při spuštění .

> [!TIP]
> Další informace o struktuře pracovního prostoru Go najdete v [dokumentaci k jazykům Go](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje `$GOPATH` `$GOROOT`informace pro nastavení a .

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro Přejít

Dále nainstalujte klientskou knihovnu pro Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

nebo pokud používáte dep, v rámci vašeho repo běhu:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Vytvoření aplikace Go

Dále vytvořte soubor v adresáři `sample-app.go` **src** s názvem :

```bash
cd src
touch sample-app.go
```

Otevřete `sample-app.go` v upřednostňovaném rozhraní IDE nebo textovém editoru. Potom přidejte název balíčku a importujte následující knihovny:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Také deklarovat kontext v kořenovém adresáři skriptu. Tento objekt budete potřebovat ke spuštění většiny volání funkce počítačového vidění:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Dále začnete přidávat kód k provádění různých operací počítačového vidění.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Computer Vision Go SDK.

|Name (Název)|Popis|
|---|---|
| [Základní klient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Tato třída je potřebná pro všechny funkce počítačového vidění, jako je například analýza obrázků a čtení textu. Můžete vytvořit konkretizovat s informacemi o předplatném a použít jej k většině operací image.|
|[Imageanalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Tento typ obsahuje výsledky volání funkce **AnalyzeImage.** Existují podobné typy pro každou z funkcí specifických pro kategorii.|
|[Výsledek operace ReadOperation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Tento typ obsahuje výsledky operace dávkového čtení. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Tento typ definuje různé druhy analýzy obrazu, které lze provést ve standardní operaci Analyzovat. V závislosti na vašich potřebách zadáte sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Počítačové vidění pro Přejít:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Čtení tištěného a ručně psaného textu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento krok předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` počítačového vidění a koncový bod s názvem a v uvedeném pořadí.

Vytvořte `main` funkci a přidejte do ní následující kód pro vytvoření instance klienta pomocí koncového bodu a klíče.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód používá objekt klienta k analýze vzdálenébitové kopie a tisku výsledků do konzoly. Můžete získat textový popis, kategorizaci, seznam značek, zjištěné objekty, zjištěné značky, zjištěné tváře, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

### <a name="set-up-test-image"></a>Nastavit testovací bitovou kopii

Nejprve uložte odkaz na adresu URL obrázku, který chcete analyzovat. Vložte to `main` do své funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Můžete také analyzovat místní obrázek. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) pro scénáře zahrnující místní image.

### <a name="specify-visual-features"></a>Určení vizuálních prvků

Následující volání funkce extrahovat různé vizuální prvky z ukázkového obrazu. Tyto funkce definujete v následujících částech.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Získat popis obrázku

Následující funkce získá seznam generovaných titulků pro obrázek. Další informace o popisu obrázku naleznete v [tématu Describe images](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázků

Následující funkce získá zjištěnou kategorii obrázku. Další informace naleznete v [tématu Kategorizace obrázků](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Získat značky obrázků

Následující funkce získá sadu zjištěných značek v obraze. Další informace naleznete v [tématu Content tags](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detekce objektů

Následující funkce detekuje běžné objekty v obraze a vytiskne je do konzoly. Další informace naleznete v [tématu Detekce objektů](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detekce značek

Následující kód detekuje podnikové značky a loga v obrázku a vytiskne je do konzoly. Další informace naleznete v [označení Detekce značky](../../concept-brand-detection.md).

Nejprve deklarujte odkaz na `main` nový obrázek v rámci funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Následující kód definuje funkci detekce značky.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující funkce vrátí zjištěné plochy v obraze s jejich obdélníkovými souřadnicemi a určitými atributy plochy. Další informace naleznete v tématu [Detekce obličeje](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detekujte obsah pro dospělé, pikantní nebo krvavý

Následující funkce vytiskne zjištěnou přítomnost obsahu pouze pro dospělé v obrázku. Další informace naleznete [v tématu Dospělý, pikantní, krvavý obsah](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující funkce vytiskne zjištěné atributy barev v obraze, například dominantní barvy a barvy zvýraznění. Další informace naleznete v [tématu Barevná schémata](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové vidění může používat specializované modely k další analýze obrázků. Další informace naleznete [v tématu Obsah specifický pro doménu](../../concept-detecting-domain-content.md). 

Následující kód analyzuje data o zjištěných celebrit v obraze.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních bodů v obraze.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získání typu obrázku

Následující funkce vytiskne informace&mdash;o typu obrázku, ať už se jedná o klipart nebo kresbu čar.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Čtení tištěného a ručně psaného textu

Počítačové vidění může číst viditelný text v obraze a převést jej na datový proud znaků. Kód v této části definuje `RecognizeTextReadAPIRemoteImage`funkci , která používá objekt klienta k detekci a extrahování vytištěného nebo ručně psaného textu v obraze.

Přidejte do funkce `main` odkaz na ukázkový obrázek a volání funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Můžete také extrahovat text z místního obrazu. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) pro scénáře zahrnující místní image.

### <a name="call-the-read-api"></a>Volání rozhraní READ API

Definujte novou funkci pro `RecognizeTextReadAPIRemoteImage`čtení textu, . Přidejte níže uvedený kód, který volá metodu **BatchReadFile** pro daný obrázek. Tato metoda vrátí ID operace a spustí asynchronní proces ke čtení obsahu obrazu.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Získat výsledky čtení

Dále získat ID operace vrácené z **BatchReadFile** volání a použít jej s **GetReadOperationResult** metoda dotaz služby pro výsledky operace. Následující kód kontroluje operaci v intervalech jedné sekundy, dokud nejsou vráceny výsledky. Potom vytiskne extrahovaná textová data do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Zobrazit výsledky čtení

Přidejte následující kód, chcete-li analyzovat a zobrazit načtená textová data a dokončit definici funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z `go run` adresáře aplikace pomocí příkazu.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Referenční příručka API pro počítačové zpracování obrazu (Přejí)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Co je počítačové zpracování obrazu?](../../Home.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
