---
title: 'Úvodní příručka: Klientská knihovna Počítačové vidění pro Javu'
description: V tomto rychlém startu můžete začít s klientskou knihovnou Počítačové vidění pro Jazyk Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: dbe986145a223f1958f1945abfa189de90952f4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272792"
---
<a name="HOLTop"></a>

[Ukázky artefaktů z referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [(Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj pro sestavení Gradle](https://gradle.org/install/)nebo jiný správce závislostí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure pro počítačové zpracování obrazu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro počítačové zpracování pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/).

Potom [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec koncového `COMPUTER_VISION_SUBSCRIPTION_KEY` klíče `COMPUTER_VISION_ENDPOINT`a služby s názvem a , resp.

### <a name="create-a-new-gradle-project"></a>Vytvoření nového projektu Gradle

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *build.gradle.kts*, který se používá za běhu k vytvoření a konfiguraci aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **dsl**vyberte **možnost Kotlin**.

Vyhledejte *soubor build.gradle.kts* a otevřete jej pomocí upřednostňovaného rozhraní IDE nebo textového editoru. Potom zkopírujte v následující konfiguraci sestavení. Tato konfigurace definuje projekt jako java aplikaci, jejíž vstupníbod je třída **ComputerVisionQuickstarts**. Importuje knihovnu počítačového vidění.

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
```

Z pracovního adresáře vytvořte pomocí následujícího příkazu zdrojovou složku projektu:

```console
mkdir -p src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *ComputerVisionQuickstarts.java*. Otevřete ji v preferovaném editoru `import` nebo rozhraní IDE a přidejte následující příkazy:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Potom přidejte definici třídy pro **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Tento rychlý start používá správce závislostí Gradle. Klientskou knihovnu a informace pro ostatní správce závislostí najdete v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Do souboru *build.gradle.kts* projektu zahrňte klientskou knihovnu Počítačové vidění jako závislost.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Computer Vision Java SDK.

|Name (Název)|Popis|
|---|---|
| [Klient ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Tato třída je potřebná pro všechny funkce počítačového vidění. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd.|
|[Počítačové vidění](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Tato třída pochází z objektu klienta a přímo zpracovává všechny operace obrazu, jako je například analýza obrázků, detekce textu a generování miniatur.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Tento výčet definuje různé typy analýzy obrázků, které lze provést ve standardní operaci Analyzovat. V závislosti na vašich potřebách zadáte sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Počítačové vidění pro Jazyk Java:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Čtení tištěného a ručně psaného textu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnnou](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `COMPUTER_VISION_SUBSCRIPTION_KEY`prostředí pro klíč počítačového vidění s názvem .

Následující kód přidá `main` metodu do vaší třídy a vytvoří proměnné pro koncový bod azure vašeho prostředku a klíč. Budete muset zadat vlastní řetězec koncového bodu, který najdete kontrolou části **Přehled** na webu Azure Portal. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Dále přidejte následující kód k vytvoření objektu [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) a předá jej do jiných metod, které budete definovat později.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód definuje metodu `AnalyzeLocalImage`, která používá objekt klienta k analýze místního obrazu a tisku výsledků. Metoda vrátí textový popis, kategorizaci, seznam značek, zjištěné tváře, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

### <a name="set-up-test-image"></a>Nastavit testovací bitovou kopii

Nejprve vytvořte **zdroj /** složku ve **složce src / main /** vašeho projektu a přidejte obrázek, který chcete analyzovat. Potom přidejte do třídy **ComputerVisionQuickstarts** následující definici metody. V případě potřeby změňte `pathToLocalImage` hodnotu souboru obrázku tak, aby odpovídala vašemu obrazovému souboru. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Můžete také analyzovat vzdálený obrázek pomocí jeho adresy URL. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) pro scénáře zahrnující vzdálené obrázky.

### <a name="specify-visual-features"></a>Určení vizuálních prvků

Dále určete, které vizuální prvky chcete v analýze extrahovat. Úplný seznam naleznete v výčtu [VisualFeatureTypes.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analýza
Tato metoda vytiskne podrobné výsledky do konzoly pro každý rozsah analýzy obrazu. Doporučujeme obklopit tuto metodu volání v Try/Catch bloku. Metoda **analyzeImageInStream** vrátí objekt **ImageAnalysis,** který obsahuje všechny extrahované informace.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

Následující části ukazují, jak podrobně analyzovat tyto informace.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další informace naleznete v [tématu Describe images](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Získat kategorii obrázků

Následující kód získá zjištěnou kategorii obrázku. Další informace naleznete v [tématu Kategorizace obrázků](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Získat značky obrázků

Následující kód získá sadu zjištěných značek v obraze. Další informace naleznete v [tématu Content tags](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plochy v obraze s jejich obdélníkovými souřadnicemi a vybere atributy plochy. Další informace naleznete v tématu [Detekce obličeje](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detekujte obsah pro dospělé, pikantní nebo krvavý

Následující kód vytiskne zjištěnou přítomnost obsahu pouze pro dospělé v obrázku. Další informace naleznete [v tématu Dospělý, pikantní, krvavý obsah](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné barevné atributy v obraze, například dominantní barvy a barvy zvýraznění. Další informace naleznete v [tématu Barevná schémata](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové vidění lze použít specializovaný model k další analýze obrázků. Další informace naleznete [v tématu Obsah specifický pro doménu](../../concept-detecting-domain-content.md). 

Následující kód analyzuje data o zjištěných celebrit v obraze.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Následující kód analyzuje data o zjištěných orientačních bodů v obraze.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Získání typu obrázku

Následující kód vytiskne informace&mdash;o typu obrázku, ať už se jedná o kresbu klipartem nebo čarou.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Čtení tištěného a ručně psaného textu

Počítačové vidění může číst viditelný text v obraze a převést jej na datový proud znaků.

> [!NOTE]
> Můžete také číst text ve vzdáleném obrázku pomocí jeho adresy URL. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) pro scénáře zahrnující vzdálené obrázky.

### <a name="call-the-recognize-api"></a>Volání rozhraní API pro rozpoznávání

Nejprve použijte následující kód k volání metody **recognizePrintedTextInStream** pro daný obrázek. Přidáte-li tento kód do projektu, je třeba `localTextImagePath` nahradit hodnotu s cestou k místní image. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Tisk výsledků rozpoznávání

Následující blok kódu zpracuje vrácený text a analyzuje jej vytisknout první slovo v každém řádku. Tento kód můžete rychle pochopit strukturu instance **OcrResult.**

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Nakonec zavřete blok try/catch a definici metody.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci pomocí příkazu: `gradle run`

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat knihovnu Java počítačového vidění k úkolům. Dále prozkoumejte referenční dokumentaci, abyste se dozvěděli více o knihovně.

> [!div class="nextstepaction"]
>[Referenční příručka počítačového vidění (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Co je počítačové zpracování obrazu?](../../Home.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).