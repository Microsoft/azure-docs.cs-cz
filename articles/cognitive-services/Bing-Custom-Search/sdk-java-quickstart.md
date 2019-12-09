---
title: 'Rychlý start: Vlastní vyhledávání Bingu klientskou knihovnu pro Java | Microsoft Docs'
description: Začněte s knihovnou klienta Vlastní vyhledávání Bingu pro Java tím, že si vyžádáte výsledky hledání z vaší instance Vlastní vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 7d1a7b2cdba082c78a1753ea0dcce6ead02ab036
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147976"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Rychlý start: Klientská knihovna Vlastní vyhledávání Bingu pro Java

Začněte s knihovnou klienta Vlastní vyhledávání Bingu pro Java. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Rozhraní API pro vlastní vyhledávání Bingu vám umožní vytvářet v přizpůsobených prostředích vyhledávání bez reklamy pro témata, která vás zajímají.

Pomocí klientské knihovny Vlastní vyhledávání Bingu pro jazyk Java:

* Vyhledá výsledky hledání na webu z vaší instance Vlastní vyhledávání Bingu. 

[Referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [artefaktů (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [ukázky](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Instance Vlastní vyhledávání Bingu. Viz rychlý Start [: Pro další informace vytvořte první](quick-start.md) instance Vlastní vyhledávání Bingu.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-bing-custom-search-azure-resource"></a>Vytvoření prostředku Azure Vlastní vyhledávání Bingu

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Vlastní vyhledávání Bingu pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

> [!TIP]
> Pokud nepoužíváte Gradle, můžete najít podrobnosti o klientské knihovně pro jiné správce závislostí v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `gradle init` z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně souboru *Build. Gradle. kts* , který se používá za běhu ke konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.

## <a name="install-the-client-library"></a>Instalace klientské knihovny 

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do této konfigurace sestavení. Nezapomeňte do `dependencies`zahrnout knihovnu klienta:

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

Přejděte do nové složky a vytvořte soubor s názvem *BingCustomSearchSample. Java*. Otevřete ho a přidejte následující příkazy `import`:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Vytvořte třídu s názvem `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Ve třídě vytvořte `main` metodu a proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, zavřete a znovu otevřete Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné. Metody budete definovat později.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektový model

Vlastní vyhledávání Bingu klient je objekt [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) , který je vytvořen z metody [ověřování ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) objektu [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) . Požadavek hledání můžete odeslat pomocí metody [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta.

Odpověď rozhraní API je objekt [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) , který obsahuje informace o vyhledávacím dotazu a výsledky hledání.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Vlastní vyhledávání Bingu klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Získat výsledky hledání z vlastní instance hledání](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Ověření klienta

Vaše metoda Main by měla obsahovat objekt [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) , který přebírá klíč, a volá jeho `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Získat výsledky hledání z vlastní instance hledání

Pomocí funkce [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta odešlete vyhledávací dotaz do vlastní instance. Nastavte `withCustomConfig` na vlastní ID konfigurace nebo na výchozí hodnotu `1`. Po získání odpovědi z rozhraní API ověřte, zda byly nalezeny žádné výsledky hledání. Pokud ano, získejte první výsledek hledání voláním funkce Response `webPages().value().get()` a vytiskněte název výsledku a adresu URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Spuštění aplikace

Sestavte aplikaci pomocí následujícího příkazu z hlavního adresáře vašeho projektu:

```console
gradle build
```

Spusťte aplikaci s cílem `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření vlastní vyhledávací webové aplikace](./tutorials/custom-search-web-page.md)
