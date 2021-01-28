---
title: Rychlý Start klientské knihovny s Vizuální vyhledávání Bingu Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: d1c9589265726bae01b86c152853c40f79825ceb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947398"
---
Pomocí tohoto rychlého startu můžete začít získávat přehledy imagí z Vizuální vyhledávání Bingu služby pomocí klientské knihovny Java. I když Vizuální vyhledávání Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch).

Pomocí klientské knihovny Vizuální vyhledávání Bingu pro jazyk Java:

* Nahrajte obrázek pro odeslání požadavku vizuálního vyhledávání.
* Získejte tokeny Insight image a vizuální vyhledávací značky.

[Referenční dokumentace](/java/api/overview/azure/cognitiveservices/client/bingvisualsearch)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  |  [Artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Po získání klíče z prostředku [Vytvořte proměnnou prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro klíč s názvem `BING_SEARCH_V7_SUBSCRIPTION_KEY` .

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts* , který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do této konfigurace sestavení:

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

Vytvořte složku pro ukázkovou aplikaci. V pracovním adresáři spusťte následující příkaz:

```console
mkdir -p src/main/java
```

Vytvořte složku pro obrázek, který chcete nahrát do rozhraní API. Umístěte obrázek do složky **Resources** .

```console
mkdir -p src/main/resources
``` 

Přejděte do nové složky a vytvořte soubor s názvem *BingVisualSearchSample. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující `import` příkazy:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Pak vytvořte novou třídu.

```java
public class BingVisualSearchSample {
}
```

V `main` metodě aplikace vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné. Pak vytvořte `byte[]` obrázek, který budete nahrávat. Vytvořte `try` blok pro metody, které budete definovat později, a načtěte image a převeďte ji na bajty pomocí `toByteArray()` .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instalace klientské knihovny

V tomto rychlém startu se používá správce závislostí Gradle. Knihovnu klienta a informace pro jiné správce závislostí najdete v [centrálním úložišti Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

V souboru *Build. Gradle. kts* vašeho projektu nezapomeňte zahrnout knihovnu klienta jako `implementation` příkaz. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Vizuální vyhledávání Bingu klientské knihovny a Java:

* [Ověření klienta](#authenticate-the-client)
* [Odeslat žádost o vizuální vyhledávání](#send-a-visual-search-request)
* [Tisk značek Insight image a vizuálního vyhledávání](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro váš vizuální vyhledávání Bingu klíč s názvem `BING_SEARCH_V7_SUBSCRIPTION_KEY` .


V metodě Main nezapomeňte použít svůj klíč předplatného k vytvoření instance objektu [BingVisualSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi) .

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Odeslat žádost o vizuální vyhledávání

V nové metodě odešlete pole bajtů obrázku (které bylo vytvořeno v `main()` metodě) pomocí metody [bingImages (). visualSearch ()](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) klienta. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Tisk značek Insight image a vizuálního vyhledávání

Zkontroluje, jestli objekt [ImageKnowledge](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge) má hodnotu null. Pokud ne, vytiskněte token Insights pro obrázky, počet značek, počet akcí a první typ akce.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

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
> [Sestavení jednostránkové webové aplikace](../../tutorial-bing-visual-search-single-page-app.md)
