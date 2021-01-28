---
title: Rychlý Start klientské knihovny s Vlastní vyhledávání Bingu Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948340"
---
Začněte s knihovnou klienta Vlastní vyhledávání Bingu pro Java. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Rozhraní API pro vlastní vyhledávání Bingu vám umožní vytvářet v přizpůsobených prostředích vyhledávání bez reklamy pro témata, která vás zajímají. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch) .

Pomocí klientské knihovny Vlastní vyhledávání Bingu pro jazyk Java:

* Vyhledá výsledky hledání na webu z vaší instance Vlastní vyhledávání Bingu.

[Referenční dokumentace](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/).
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Instance Vlastní vyhledávání Bingu. Další informace najdete v tématu [rychlý Start: Vytvoření první instance vlastní vyhledávání Bingu](../../quick-start.md) .

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Po získání klíče z prostředku [Vytvořte proměnnou prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro klíč s názvem `AZURE_BING_CUSTOM_SEARCH_API_KEY` .

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

> [!TIP]
> Pokud nepoužíváte Gradle, můžete najít podrobnosti o klientské knihovně pro jiné správce závislostí v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně souboru *Build. Gradle. kts* , který se používá za běhu ke konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.

## <a name="install-the-client-library"></a>Instalace klientské knihovny

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do této konfigurace sestavení. Nezapomeňte zahrnout klientskou knihovnu do `dependencies` :

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

Vytvořte složku pro ukázkovou aplikaci. V pracovním adresáři spusťte následující příkaz:

```console
mkdir src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *BingCustomSearchSample. Java*. Otevřete ho a přidejte následující `import` příkazy:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Vytvořte třídu s názvem `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Ve třídě vytvořte `main` metodu a proměnnou pro klíč prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, zavřete a znovu otevřete Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné. Metody budete definovat později.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektový model

Vlastní vyhledávání Bingu klient je objekt [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) , který je vytvořen z metody [ověřování ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) objektu [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager) . Požadavek hledání můžete odeslat pomocí metody [BingCustomInstances. Search ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta.

Odpověď rozhraní API je objekt [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse) , který obsahuje informace o vyhledávacím dotazu a výsledky hledání.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Vlastní vyhledávání Bingu klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Získat výsledky hledání z vlastní instance hledání](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Ověření klienta

Vaše metoda Main by měla obsahovat objekt [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) , který přebírá svůj klíč, a volá svůj `authenticate()` .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Získat výsledky hledání z vlastní instance hledání

Pomocí funkce [BingCustomInstances. Search ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta odešlete vyhledávací dotaz do vlastní instance. Nastavte na `withCustomConfig` vlastní ID konfigurace nebo výchozí na `1` . Po získání odpovědi z rozhraní API ověřte, zda byly nalezeny žádné výsledky hledání. Pokud ano, získejte první výsledek hledání voláním `webPages().value().get()` funkce odpovědi a vytiskněte název výsledku a adresu URL.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Spuštění aplikace

Sestavte aplikaci pomocí následujícího příkazu z hlavního adresáře vašeho projektu:

```console
gradle build
```

Spusťte aplikaci s `run` cílem:

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vlastní vyhledávací webové aplikace](../../tutorials/custom-search-web-page.md)
