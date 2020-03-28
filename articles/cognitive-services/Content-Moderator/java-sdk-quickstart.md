---
title: 'Úvodní příručka: Klientská knihovna Content Moderator pro Javu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou Azure Cognitive Services Content Moderator pro Jazyk Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5dd1c1bb2b321e617efc430ce48745c06a827305
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772452"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Úvodní příručka: Klientská knihovna Content Moderator pro Javu

Začínáme s klientskou knihovnou Content Moderator pro Jazyk Java. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Content Moderator je kognitivní služba, která kontroluje text, obrázek a video obsah pro materiál, který je potenciálně urážlivý, riskantní nebo jinak nežádoucí. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele.

Pomocí klientské knihovny Content Moderator pro Jazyk Java:

* Moderování obrázků pro dospělé nebo pikantní obsah, text nebo lidské tváře.

[Ukázky artefaktů z referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj pro sestavení Gradle](https://gradle.org/install/)nebo jiný správce závislostí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-content-moderator-azure-resource"></a>Vytvoření prostředku Azure moderátora obsahu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro Content Moderator pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/).

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Vytvoření nového projektu Gradle

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```
Spusťte `gradle init`. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *build.gradle.kts*, který se používá za běhu k vytvoření a konfiguraci aplikace. Spusťte tento příkaz z pracovního adresáře:

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru skriptu sestavení DSL vyberte **možnost Kotlin**.

Najděte *build.gradle.kts* a otevřete je pomocí preferovaného IDE nebo textového editoru. Potom zkopírujte v následující konfiguraci sestavení. Tato konfigurace definuje projekt jako aplikaci Java, jejíž vstupníbod je třída **ContentModeratorQuickstart**. Importuje content moderator sdk, stejně jako Gson sdk pro serializaci JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

Z pracovního adresáře vytvořte pomocí následujícího příkazu zdrojovou složku projektu.

```console
mkdir -p src/main/java
```

Poté vytvořte soubor s názvem *ContentModeratorQuickstart.java* v nové složce. Otevřete soubor v upřednostňovaném editoru nebo rozhraní IDE a nahoře importujte následující knihovny:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce sady Content Moderator Java SDK.

|Name (Název)|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Tato třída je potřebná pro všechny funkce Content Moderator. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd.|
|[Moderování obrázku](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní informace nebo lidské tváře.|
|[Moderování textu](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní informace.|
|[Recenze](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Tato třída poskytuje funkce rozhraní API pro kontrolu, včetně metod pro vytváření úloh, vlastních pracovních postupů a lidských recenzí.|


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly v klientské knihovně Content Moderator pro Jazyk Ovou:

* [Ověření klienta](#authenticate-the-client)
* [Střední obrázky](#moderate-images)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento krok předpokládá, že jste [vytvořili proměnnou](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) prostředí `AZURE_CONTENTMODERATOR_KEY`pro klíč Moderátor obsahu s názvem .

V `main` metodě aplikace vytvořte objekt [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) pomocí hodnoty koncového bodu předplatného a proměnné prostředí klíče předplatného. 

> [!NOTE]
> Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Střední obrázky

### <a name="get-images"></a>Získání obrázků

Ve **složce src/main/** projektu vytvořte složku **zdrojů** a přejděte na něj. Potom vytvořte nový textový soubor *ImageFiles.txt*. V tomto souboru přidáte adresy URL obrázků&mdash;a analyzujete jednu adresu URL na každém řádku. Můžete použít následující ukázkové obrázky:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Potom do souboru *ContentModeratorQuickstart.java* přidejte do třídy **ContentModeratorQuickstart** následující definici třídy. Tato vnitřní třída bude použita později v procesu moderování obrazu.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterate prostřednictvím obrázků

Dále přidejte následující kód na `main` konec metody. Nebo jej můžete přidat do samostatné metody, `main`která je volána z . Tento kód prochází každý řádek souboru _ImageFiles.txt._

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Kontrola obsahu pro dospělé/pikantní
Tento řádek kódu kontroluje obrázek na dané adrese URL pro obsah pro dospělé nebo pikantní. Informace o těchto podmínkách naleznete v koncepční příručce moderování obrázků.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Kontrola textu
Tento řádek kódu kontroluje, zda obrázek nezkontroluje viditelný text.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Kontrola ploch
Tento řádek kódu kontroluje obrázek pro lidské tváře.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Nakonec uložte vrácené `EvaluationData` informace v seznamu.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Tisk výsledků

Po `while` smyčku přidejte následující kód, který vytiskne výsledky do konzoly a do výstupního *souboru, src/main/resources/ModeonOutput.json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zavřete `try` příkaz a `catch` přidejte příkaz k dokončení metody.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci pomocí příkazu: `gradle run`

```console
gradle run
```

Potom přejděte do souboru *src/main/resources/ModeonOutput.json* a zobrazte výsledky moderování obsahu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat knihovnu Java Content Moderator k provádění úkolů moderování. Dále si přečtěte koncepční příručku, kde se dozvíte více o moderování obrázků nebo jiných médií.

> [!div class="nextstepaction"]
>[Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](./overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).