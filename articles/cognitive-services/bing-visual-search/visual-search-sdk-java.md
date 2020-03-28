---
title: 'Úvodní příručka: Klientská knihovna Vizuálního vyhledávání Bingu pro Jazyk Java | Dokumenty společnosti Microsoft'
description: Nahrajte obrázek pomocí sady SDK vizuálního vyhledávání Bingu a získejte o něm přehledy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379486"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Úvodní příručka: Klientská knihovna Vizuálního vyhledávání Bingu pro Jazyk Java

Začínáme s klientskou knihovnou Vizuální vyhledávání Bing pro Jazyk Java. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. 

Pomocí klientské knihovny Vizuální vyhledávání Bing pro Jazyk Java:

* Nahrajte obrázek a odešlete žádost o vizuální vyhledávání.
* Získejte token pro vyhledávání obrázků a vizuální vyhledávací značky.

[Referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [knihovny](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Nástroj [pro sestavení Gradle](https://gradle.org/install/)nebo jiný správce závislostí

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Vytvoření nového projektu Gradle

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *build.gradle.kts,* který se používá za běhu k vytvoření a konfiguraci aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **dsl**vyberte **možnost Kotlin**.

Vyhledejte *soubor build.gradle.kts* a otevřete jej pomocí upřednostňovaného rozhraní IDE nebo textového editoru. Potom zkopírujte v této konfiguraci sestavení:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Vytvořte složku pro ukázkovou aplikaci. Z pracovního adresáře spusťte následující příkaz:

```console
mkdir -p src/main/java
```

Vytvořte složku pro obrázek, který chcete nahrát do rozhraní API. Umístěte obrázek do složky **prostředků.**

```console
mkdir -p src/main/resources
``` 

Přejděte do nové složky a vytvořte soubor s názvem *BingVisualSearchSample.java*. Otevřete ji v preferovaném editoru `import` nebo rozhraní IDE a přidejte následující příkazy:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Pak vytvořte novou třídu.

```java
public class BingVisualSearchSample {
}
```

V `main` metodě aplikace vytvořte proměnné pro koncový bod a klíč azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné. Pak vytvořte `byte[]` pro obrázek, který budete nahrávat. Vytvořte `try` blok pro metody, které budete definovat později, načíst obraz `toByteArray()`a převést jej na bajty pomocí .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instalace klientské knihovny

Tento rychlý start používá správce závislostí Gradle. Klientskou knihovnu a informace pro ostatní správce závislostí najdete v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

V souboru *build.gradle.kts* projektu nezapomeňte zahrnout klientskou knihovnu jako příkaz. `implementation` 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Vizuální vyhledávání Bing a jazykovou javou:

* [Ověření klienta](#authenticate-the-client)
* [Odeslání požadavku na vizuální vyhledávání](#send-a-visual-search-request)
* [Tisk tokenu přehledu obrázků a vizuálních vyhledávacích značek](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnnou](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) prostředí `BING_SEARCH_V7_SUBSCRIPTION_KEY`pro klíč vizuálního vyhledávání Bing s názvem .


V hlavní metodě nezapomeňte použít klíč předplatného k vytvoření instance objektu [BingVisualSearchAPI.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable)

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Odeslání požadavku na vizuální vyhledávání

V nové metodě odešlete pole bajtů `main()` bitových kopií (které bylo vytvořeno v metodě) pomocí metody [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) klienta. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Tisk tokenu přehledu obrázků a vizuálních vyhledávacích značek

Zkontrolujte, zda je objekt [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) null. Pokud ne, vytiskněte token přehledů obrázků, počet značek, počet akcí a typ první akce.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci `run` s cílem:

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-visual-search-single-page-app.md)
