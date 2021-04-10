---
title: 'Rychlý Start: Klientská knihovna pro analýzu obrázků pro Java'
description: V tomto rychlém startu začněte s knihovnou klienta pro analýzu imagí pro Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 9349ac5c0b207d0dffb71295117f35849ab5caba
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073703"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny pro analýzu obrázků můžete analyzovat obrázek pro značky, text popisu, obličeje, obsah pro dospělé a další.

[Referenční dokumentace](/java/api/overview/azure/cognitiveservices/client/computervision)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision)  | [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)  |  [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts*, který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V tomto rychlém startu se používá správce závislostí Gradle. Knihovnu klienta a informace pro jiné správce závislostí najdete v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do následující konfigurace sestavení. Tato konfigurace definuje projekt jako aplikaci Java, jejíž vstupní bod je třídou **ComputerVisionQuickstarts**. Importuje knihovnu Počítačové zpracování obrazu.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Vytvoření souboru Java

V pracovním adresáři spusťte následující příkaz, který vytvoří zdrojovou složku projektu:

```console
mkdir -p src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *ComputerVisionQuickstarts. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující `import` příkazy:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), který obsahuje příklady kódu v tomto rychlém startu.

Definujte třídu **ComputerVisionQuickstarts**.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_1)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_2)]

V rámci třídy **ComputerVisionQuickstarts** vytvořte proměnné pro klíč a koncový bod prostředku.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek Počítačové zpracování obrazu, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

V metodě **Main** aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Později je budete definovat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzeinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]


> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí sady SDK pro analýzu obrázků v sadě Java SDK.

|Název|Description|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)| Tato třída pochází z objektu klienta a přímo zpracovává všechny operace obrázků, jako je analýza obrázků, detekce textu a generování miniatur.|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro analýzu imagí pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci objektu [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) s vaším koncovým bodem a klíčem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#analyze-an-image) [narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód definuje metodu, `AnalyzeLocalImage` která používá objekt klienta k analýze místní image a vytištění výsledků. Metoda vrátí textový popis, kategorizaci, seznam značek, zjištěné plošky, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

> [!TIP]
> Můžete také analyzovat vzdálenou bitovou kopii pomocí adresy URL. Podívejte se na metody [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) , jako je například **AnalyzeImage**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) , kde najdete scénáře týkající se vzdálených imagí.

### <a name="set-up-test-image"></a>Nastavit testovací image

Nejprve vytvořte **prostředky/** složky v **Src/Main/** složce projektu a přidejte obrázek, který chcete analyzovat. Pak přidejte následující definici metody do třídy **ComputerVisionQuickstarts** . Změňte hodnotu `pathToLocalImage` tak, aby odpovídala vašemu souboru obrázku. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Zadat vizuální funkce

Dále určete, které vizuální funkce chcete ve své analýze extrahovat. Úplný seznam najdete v [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes) výčtu.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analyzovat
Tento blok vytiskne podrobné výsledky do konzoly pro každý obor analýzy obrázků. Metoda **analyzeImageInStream** vrací objekt **ImageAnalysis** , který obsahuje všechny extrahované informace.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

Následující části ukazují, jak podrobně analyzovat tyto informace.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další informace najdete v tématu [popisujícím obrázky](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Získat kategorii obrázku

Následující kód získá zjištěnou kategorii obrázku. Další informace najdete v tématu [kategorizace imagí](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Získat značky obrázku

Následující kód získá sadu zjištěných značek v obrázku. Další informace najdete v tématu [značky obsahu](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plošky v obrázku s jejich souřadnicemi obdélníku a vybere atributy obličeje. Další informace najdete v tématu [rozpoznávání tváře](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-objects"></a>Detekovat objekty

Následující kód vrátí zjištěné objekty v obrázku s jejich souřadnicemi. Další informace najdete v tématu [detekce objektů](../../concept-object-detection.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_objects)]


### <a name="detect-brands"></a>Detekovat značky

Následující kód vrátí zjištěná loga značky v obrázku s jejich souřadnicemi. Další informace najdete v tématu [rozpoznávání značek](../../concept-brand-detection.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_brands)]



### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující kód vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další informace najdete v článku [obsah pro dospělé, pikantní a gorie](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další informace najdete v tématu [Barevná schémata](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Analýza obrázků může pomocí specializovaného modelu provádět další analýzu imagí. Další informace najdete v tématu [obsah specifický pro doménu](../../concept-detecting-domain-content.md). 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující kód Vytiskne informace o typu obrázku &mdash; , ať už se jedná o Klipart nebo kreslení čáry.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [Analyzoval jsem obrázek](?success=analyze-image#run-the-application) , který [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=analyze-image)

### <a name="close-out-the-method"></a>Zavřít metodu

Dokončete blok try/catch a zavřete metodu.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyze_catch)]


## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci pomocí `gradle run` příkazu:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nainstalovat knihovnu klienta pro analýzu imagí a provést základní volání analýzy obrázků. V dalším kroku se dozvíte víc o funkcích rozhraní API pro analýzu.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro analýzu](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Přehled analýzy obrázků](../../overview-image-analysis.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
