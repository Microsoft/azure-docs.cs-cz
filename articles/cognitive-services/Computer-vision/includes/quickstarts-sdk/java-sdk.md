---
title: 'Rychlý Start: Počítačové zpracování obrazu Klientská knihovna pro Java'
description: V tomto rychlém startu Začínáme s klientskou knihovnou Počítačové zpracování obrazu pro Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 7057644207fae866e7fe789f951d2279b1c8e3eb
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698048"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny Počítačové zpracování obrazu můžete:

* Analyzujte obrázek pro značky, text Description, obličeje, obsah pro dospělé a další.
* Přečtěte si vytištěné a ručně psaný text pomocí rozhraní API pro čtení.

[Referenční dokumentace](/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision)  | [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)  |  [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
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

V třídě **ComputerVisionQuickstarts** aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek Počítačové zpracování obrazu, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

V metodě **Main** aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Později je budete definovat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_maincalls)]

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce Počítačové zpracování obrazu Java SDK.

|Název|Popis|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Tato třída pochází z objektu klienta a přímo zpracovává všechny operace obrázků, jako je analýza obrázků, detekce textu a generování miniatur.|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Počítačové zpracování obrazu klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci objektu [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) s vaším koncovým bodem a klíčem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#analyze-an-image) [narazil na problém](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód definuje metodu, `AnalyzeLocalImage` která používá objekt klienta k analýze místní image a vytištění výsledků. Metoda vrátí textový popis, kategorizaci, seznam značek, zjištěné plošky, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

> [!TIP]
> Můžete také analyzovat vzdálenou bitovou kopii pomocí adresy URL. Podívejte se na metody [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) , jako je například **AnalyzeImage**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) , kde najdete scénáře týkající se vzdálených imagí.

### <a name="set-up-test-image"></a>Nastavit testovací image

Nejprve vytvořte **prostředky/** složky v **Src/Main/** složce projektu a přidejte obrázek, který chcete analyzovat. Pak přidejte následující definici metody do třídy **ComputerVisionQuickstarts** . Změňte hodnotu `pathToLocalImage` tak, aby odpovídala vašemu souboru obrázku. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Zadat vizuální funkce

Dále určete, které vizuální funkce chcete ve své analýze extrahovat. Úplný seznam najdete v [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) výčtu.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analýza
Tato metoda vytiskne podrobné výsledky do konzoly pro každý obor analýzy obrázků. Doporučujeme obklopit toto volání metody v bloku try/catch. Metoda **analyzeImageInStream** vrací objekt **ImageAnalysis** , který obsahuje všechny extrahované informace.

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

### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující kód vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další informace najdete v článku [obsah pro dospělé, pikantní a gorie](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další informace najdete v tématu [Barevná schémata](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové zpracování obrazu může použít specializovaný model k další analýze imagí. Další informace najdete v tématu [obsah specifický pro doménu](../../concept-detecting-domain-content.md). 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující kód Vytiskne informace o typu obrázku &mdash; , ať už se jedná o Klipart nebo kreslení čáry.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [Analyzoval jsem obrázek](?success=analyze-image#read-printed-and-handwritten-text) , který [jsem narazil na problém](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Počítačové zpracování obrazu může číst zobrazený text v obrázku a převést jej na datový proud znaků. Tato část definuje metodu, `ReadFromFile` která přijímá místní cestu k souboru a tiskne text obrázku do konzoly.

> [!TIP]
> Můžete také číst text ve vzdálené imagi, na kterou odkazuje adresa URL. Podívejte se na metody [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) , jako je **čtení**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) , kde najdete scénáře týkající se vzdálených imagí.

### <a name="set-up-test-image"></a>Nastavit testovací image

Vytvořte **prostředky/** složky v **Src/Main/** složce projektu a přidejte do ní obrázek, ze kterého chcete text přečíst. Můžete si stáhnout [ukázkovou bitovou kopii](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) , kterou chcete použít.

Pak přidejte následující definici metody do třídy **ComputerVisionQuickstarts** . Změňte hodnotu `localFilePath` tak, aby odpovídala vašemu souboru obrázku. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Pak přidejte následující kód, který zavolá metodu **readInStreamWithServiceResponseAsync** pro daný obrázek.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

Následující blok kódu extrahuje ID operace z odpovědi volání metody Read. Toto ID používá pomocnou metodu pro vytištění výsledků čtení textu do konzoly. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Zavřete blok try/catch a definici metody.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Získat výsledky čtení

Pak přidejte definici pro pomocnou metodu. Tato metoda používá ID operace z předchozího kroku k dotazování operace čtení a získání výsledků optického rozpoznávání znaků, pokud jsou k dispozici.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

Zbytek metody analyzuje výsledky optického rozpoznávání znaků a vytiskne je do konzoly.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Nakonec přidejte další pomocnou metodu použitou výše, která extrahuje ID operace z počáteční odpovědi.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Načetl jsem text](?success=read-printed-handwritten-text#run-the-application) , [který jsem narazil na problém](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

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
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://www.research.net/r/7QYZKHL?issue=clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak provádět základní úlohy pomocí Počítačové zpracování obrazu knihovny Java. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
>[Odkaz na Počítačové zpracování obrazu (Java)](/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)


* [Co je počítačové zpracování obrazu?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).