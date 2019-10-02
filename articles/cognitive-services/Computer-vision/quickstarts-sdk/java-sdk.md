---
title: 'Rychlý Start: Počítačové zpracování obrazu Klientská knihovna pro Java'
titleSuffix: Azure Cognitive Services
description: Začněte s knihovnou klienta Počítačové zpracování obrazu pro Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: pafarley
ms.openlocfilehash: 9c73d28952a165f07711d1aace665bbe742f0124
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718470"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Rychlý Start: Počítačové zpracování obrazu Klientská knihovna pro Java

Začněte s knihovnou klienta Počítačové zpracování obrazu pro Java. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům pro zpracování imagí a vracení informací.

Pomocí klientské knihovny Počítačové zpracování obrazu pro jazyk Java:

* Analyzujte obrázek pro značky, text Description, obličeje, obsah pro dospělé a další.
* Vyrozpoznávají vytištěné a ručně psaný text pomocí rozhraní Batch Read API.

[Referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)@no__t[Maven (artefakty)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) -1

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure Počítačové zpracování obrazu

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Počítačové zpracování obrazu pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` v uvedeném pořadí.

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `gradle init` z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts*, který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.

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
```

V pracovním adresáři spusťte následující příkaz, který vytvoří zdrojovou složku projektu:

```console
mkdir -p src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *ComputerVisionQuickstarts. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující příkazy `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Pak přidejte definici třídy pro **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V tomto rychlém startu se používá správce závislostí Gradle. Knihovnu klienta a informace pro jiné správce závislostí najdete v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Do souboru *Build. Gradle. kts* vašeho projektu zahrňte klientskou knihovnu počítačové zpracování obrazu jako závislost.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce Počítačové zpracování obrazu Java SDK.

|Name (Název)|Popis|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Tato třída pochází z objektu klienta a přímo zpracovává všechny operace obrázků, jako je analýza obrázků, detekce textu a generování miniatur.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Počítačové zpracování obrazu klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro počítačové zpracování obrazu klíč s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Následující kód přidá do třídy metodu `main` a vytvoří proměnné pro koncový bod a klíč Azure prostředku. Budete muset zadat vlastní řetězec koncového bodu, který můžete najít zaškrtnutím části **přehled** Azure Portal. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Dále přidejte následující kód pro vytvoření objektu [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) a předává ho do jiných metod, které budete definovat později.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k proměnné.

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód definuje metodu `AnalyzeLocalImage`, která používá objekt klienta k analýze místní image a vytištění výsledků. Metoda vrátí textový popis, kategorizaci, seznam značek, zjištěné plošky, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

### <a name="set-up-test-image"></a>Nastavit testovací image

Nejprve vytvořte **prostředky/** složky v **Src/Main/** složce projektu a přidejte obrázek, který chcete analyzovat. Pak přidejte následující definici metody do třídy **ComputerVisionQuickstarts** . V případě potřeby změňte hodnotu `pathToLocalImage` tak, aby odpovídala vašemu souboru obrázku. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Můžete také analyzovat vzdálenou bitovou kopii pomocí adresy URL. Scénáře týkající se vzdálených imagí najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) .

### <a name="specify-visual-features"></a>Zadat vizuální funkce

Dále určete, které vizuální funkce chcete ve své analýze extrahovat. Úplný seznam najdete v [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) výčtu.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analýza
Tato metoda vytiskne podrobné výsledky do konzoly pro každý obor analýzy obrázků. Doporučujeme obklopit toto volání metody v bloku try/catch. Metoda **analyzeImageInStream** vrací objekt **ImageAnalysis** , který obsahuje všechny extrahované informace.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

Následující části ukazují, jak podrobně analyzovat tyto informace.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další podrobnosti najdete v tématu [popisujícím obrázky](../concept-describing-images.md) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Získat kategorii obrázku

Následující kód získá zjištěnou kategorii obrázku. Další podrobnosti najdete v tématu [kategorizace imagí](../concept-categorizing-images.md) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Získat značky obrázku

Následující kód získá sadu zjištěných značek v obrázku. Další podrobnosti najdete v tématu [značky obsahu](../concept-tagging-images.md) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plošky v obrázku s jejich souřadnicemi obdélníku a vyberte možnost atributy obličeje. Další podrobnosti najdete v tématu [rozpoznávání tváře](../concept-detecting-faces.md) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující kód vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další podrobnosti najdete v článku [obsah pro dospělé, pikantní a gorie](../concept-detecting-adult-content.md) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další podrobnosti najdete v tématu [Barevná schémata](../concept-detecting-color-schemes.md) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové zpracování obrazu může použít specializovaný model k další analýze imagí. Další podrobnosti najdete v tématu [obsah specifický pro doménu](../concept-detecting-domain-content.md) . 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující kód Vytiskne informace o typu obrázku @ no__t-0whether je Klipart nebo kreslení čáry.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Počítačové zpracování obrazu může číst zobrazený text v obrázku a převést jej na datový proud znaků.

> [!NOTE]
> Můžete také číst text ve vzdálené imagi pomocí jeho adresy URL. Scénáře týkající se vzdálených imagí najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) .

### <a name="call-the-recognize-api"></a>Volání rozhraní API pro rozpoznávání

Nejprve použijte následující kód k volání metody **recognizePrintedTextInStream** pro daný obrázek. Když přidáte tento kód do projektu, je třeba nahradit hodnotu `localTextImagePath` cestou k místní imagi. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

### <a name="print-recognize-results"></a>Tisk – rozpoznat výsledky

Následující blok kódu zpracovává vrácený text a analyzuje ho pro vytištění prvního slova na každém řádku. Tento kód můžete použít k rychlému pochopení struktury instance **OcrResult** .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Nakonec zavřete blok try/catch a definici metody.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci pomocí příkazu `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak provádět základní úlohy pomocí Počítačové zpracování obrazu knihovny Java. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
>[Odkaz na Počítačové zpracování obrazu (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Co je Počítačové zpracování obrazu?](../Home.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).