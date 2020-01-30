---
title: 'Rychlý Start: Počítačové zpracování obrazu klientskou knihovnu pro přejít'
titleSuffix: Azure Cognitive Services
description: Začněte s tímto rychlým startem pomocí Počítačové zpracování obrazu klientské knihovny pro.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e4ac76f83ad4bedb420c52606598095c66747f2d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765297"
---
# <a name="quickstart-computer-vision-client-library-for-go"></a>Rychlý Start: Počítačové zpracování obrazu klientskou knihovnu pro přejít

Začínáme s klientskou knihovnou Počítačové zpracování obrazu pro přejít. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům pro zpracování imagí a vracení informací.

Pomocí klientské knihovny Počítačové zpracování obrazu můžete přejít na:

* Analyzujte obrázek pro značky, text Description, obličeje, obsah pro dospělé a další.
* Vyrozpoznávají vytištěné a ručně psaný text pomocí rozhraní Batch Read API.

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) |  | [balíčku](https://github.com/Azure/azure-sdk-for-go) [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure Počítačové zpracování obrazu

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Počítačové zpracování obrazu pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Až dostanete klíč ze zkušebního předplatného nebo prostředku, [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíčovou adresu URL klíče a koncového bodu s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT`v uvedeném pořadí.

### <a name="create-a-go-project-directory"></a>Vytvořit adresář projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt přejít s názvem `my-app`a přejděte do něj.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Váš pracovní prostor bude obsahovat tři složky:

* **Src** – tento adresář bude obsahovat zdrojový kód a balíčky. V tomto adresáři se budou nacházet všechny balíčky nainstalované pomocí příkazu `go get`.
* **pkg** – tento adresář bude obsahovat kompilované objekty balíčku pro přechod. Všechny tyto soubory mají rozšíření `.a`.
* **bin** – tento adresář bude obsahovat binární spustitelné soubory, které se vytvoří při spuštění `go install`.

> [!TIP]
> Další informace o struktuře pracovního prostoru přejděte v [dokumentaci jazyka přejděte](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT`.

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

Dále vytvořte soubor v adresáři **Src** s názvem `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Otevřete `sample-app.go` v upřednostňovaném integrovaném vývojovém prostředí (IDE) nebo textovém editoru. Pak přidejte název balíčku a importujte následující knihovny:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Deklarujte také kontext v kořenovém adresáři vašeho skriptu. Tento objekt budete potřebovat ke spuštění většiny Počítačové zpracování obrazu volání funkcí:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Dále začnete přidávat kód pro provádění různých Počítačové zpracování obrazuch operací.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Počítačové zpracování obrazu jít SDK.

|Name (Název)|Popis|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu, jako je například analýza obrázku a čtení textu. Vytvoříte jeho instanci s informacemi o předplatném a použijete ho k provádění většiny operací s imagí.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Tento typ obsahuje výsledky volání funkce **AnalyzeImage** . Pro každou funkci konkrétní kategorie existují podobné typy.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Tento typ obsahuje výsledky operace čtení dávky. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Tento typ definuje různé druhy analýz obrázků, které je možné provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Počítačové zpracování obrazu klientské knihovny pro přejít:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto kroku se předpokládá, že jste pro svůj Počítačové zpracování obrazu klíč a koncový bod [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT`.

Vytvořte funkci `main` a přidejte do ní následující kód, který vytvoří instanci klienta s vaším koncovým bodem a klíčem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód používá objekt klienta k analýze vzdálené image a k vytištění výsledků do konzoly. Můžete získat textový popis, kategorizaci, seznam značek, zjištěných objektů, zjištěných značek, zjištěné plošky, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

### <a name="set-up-test-image"></a>Nastavit testovací image

Nejdřív uložte odkaz na adresu URL obrázku, který chcete analyzovat. Dejte ho do funkce `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Můžete také analyzovat místní bitovou kopii. Scénáře týkající se místních imagí najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) .

### <a name="specify-visual-features"></a>Zadat vizuální funkce

Následující volání funkce extrahují různé vizuální funkce z ukázkové image. Tyto funkce definujete v následujících oddílech.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Získat popis obrázku

Následující funkce získá seznam generovaných titulků pro obrázek. Další informace o popisu obrázku najdete v tématu [popisujícím obrázky](../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázku

Následující funkce získá zjištěnou kategorii obrázku. Další informace najdete v tématu [kategorizace imagí](../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Získat značky obrázku

Následující funkce získá sadu zjištěných značek v obrázku. Další informace najdete v tématu [značky obsahu](../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detekovat objekty

Následující funkce detekuje běžné objekty v imagi a tiskne je do konzoly. Další informace najdete v tématu [detekce objektů](../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detekovat značky

Následující kód detekuje firemní značky a loga v imagi a vytiskne je do konzoly. Další informace najdete v [rozpoznávání značek](../concept-brand-detection.md).

Nejprve deklarujte odkaz na nový obrázek v rámci funkce `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Následující kód definuje funkci detekce značky.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující funkce vrátí zjištěné plošky v obrázku s jejich souřadnicemi obdélníku a určitými atributy obličeje. Další informace najdete v tématu [rozpoznávání tváře](../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující funkce vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další informace najdete v článku [obsah pro dospělé, pikantní a gorie](../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující funkce vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další informace najdete v tématu [Barevná schémata](../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové zpracování obrazu může použít specializované modely k dalšímu analýze imagí. Další informace najdete v tématu [obsah specifický pro doménu](../concept-detecting-domain-content.md). 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující funkce vytiskne informace o typu obrázku&mdash;, zda se jedná o Klipart nebo kreslení čáry.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Počítačové zpracování obrazu může číst zobrazený text v obrázku a převést jej na datový proud znaků. Kód v této části definuje funkci, `RecognizeTextReadAPIRemoteImage`, která používá objekt klienta k detekci a extrakci vytištěného nebo rukopisného textu v obrázku.

Do funkce `main` přidejte odkaz na ukázkovou image a volání funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Můžete také extrahovat text z místní image. Scénáře týkající se místních imagí najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) .

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Definujte novou funkci pro čtení textu `RecognizeTextReadAPIRemoteImage`. Přidejte následující kód, který volá metodu **BatchReadFile** pro daný obrázek. Tato metoda vrátí ID operace a spustí asynchronní proces pro čtení obsahu obrázku.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Získat výsledky čtení

Potom Získejte ID operace vrácené voláním **BatchReadFile** a použijte ji s metodou **GetReadOperationResult** pro dotazování služby na výsledky operace. Následující kód kontroluje operaci v intervalu 1 sekund, dokud nebudou vráceny výsledky. Pak zobrazí extrahovaná textová data do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Zobrazit výsledky čtení

Přidejte následující kód pro analýzu a zobrazení načtených textových dat a dokončete definici funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí příkazu `go run`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro počítačové zpracování obrazu (Přejít)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Co je Počítačové zpracování obrazu?](../Home.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)
