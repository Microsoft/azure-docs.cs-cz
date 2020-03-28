---
title: Rychlý start klientské knihovny Java pro vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: e175d77ec4684053070e817f8a6ec7e9455668b1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252895"
---
Začínáme s klientskou knihovnou vlastního vyhledávání Bingu pro Jazyk Java. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Rozhraní API pro vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobené vyhledávací prostředí bez reklam pro témata, která vás zajímají. Zdrojový kód pro tuto ukázku naleznete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Pomocí klientské knihovny vlastního vyhledávání Bingu pro Jazyk Java:

* Najděte výsledky hledání na webu z instance vlastního vyhledávání Bingu. 

[Referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [knihovny](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Aktuální verze [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Nástroj pro sestavení Gradle](https://gradle.org/install/)nebo jiný správce závislostí.
* Instance vlastního vyhledávání Bingu. Další informace najdete [v tématu Úvodní příručka: Vytvoření první instance vlastního vyhledávání Bingu.](../../quick-start.md)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Vytvoření nového projektu Gradle

> [!TIP]
> Pokud nepoužíváte Gradle, můžete najít podrobnosti klientské knihovny pro ostatní správce závislostí na [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně souboru *build.gradle.kts,* který se používá za běhu ke konfiguraci aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **dsl**vyberte **možnost Kotlin**.

## <a name="install-the-client-library"></a>Instalace klientské knihovny 

Vyhledejte *soubor build.gradle.kts* a otevřete jej pomocí upřednostňovaného rozhraní IDE nebo textového editoru. Potom zkopírujte v této konfiguraci sestavení. Nezapomeňte zahrnout klientskou knihovnu do části `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Vytvořte složku pro ukázkovou aplikaci. Z pracovního adresáře spusťte následující příkaz:

```console
mkdir src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *BingCustomSearchSample.java*. Otevřete ji a `import` přidejte následující příkazy:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Vytvoření třídy s názvem`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Ve třídě vytvořte `main` metodu a proměnnou pro klíč zdroje. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, zavřete a znovu otevřete editor, IDE nebo prostředí, které ji spustilo pro přístup k proměnné. Metody definujete později.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektový model

Klient vlastního vyhledávání Bing je objekt [BingCustomSearchAPI,](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) který je vytvořen z metody [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) objektu [BingCustomSearchManager.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) Požadavek na hledání můžete odeslat pomocí metody [BingCustomInstances.search() klienta.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__)

Odpověď rozhraní API je objekt [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) obsahující informace o vyhledávacím dotazu a výsledky hledání.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou vlastního vyhledávání Bingpro Jazyk Java:

* [Ověření klienta](#authenticate-the-client)
* [Získání výsledků hledání z vlastní instance hledání](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Ověření klienta

Hlavní metoda by měla zahrnovat [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) objekt, který `authenticate()`bere váš klíč a volá jeho .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Získání výsledků hledání z vlastní instance hledání

Pomocí funkce [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta odešlete vyhledávací dotaz do vlastní instance. `withCustomConfig` Nastavte vlastní ID konfigurace nebo výchozí `1`hodnotu . Po získání odpovědi z rozhraní API zkontrolujte, zda byly nalezeny nějaké výsledky hledání. Pokud ano, získejte první výsledek hledání `webPages().value().get()` voláním funkce odpovědi a vytiskněte název výsledku a adresu URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Spuštění aplikace

Vytvořte aplikaci pomocí následujícího příkazu z hlavního adresáře projektu:

```console
gradle build
```

Spusťte aplikaci `run` s cílem:

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro vlastní vyhledávání](../../tutorials/custom-search-web-page.md)
