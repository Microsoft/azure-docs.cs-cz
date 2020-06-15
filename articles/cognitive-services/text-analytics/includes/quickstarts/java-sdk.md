---
title: 'Rychlý Start: Klientská knihovna Analýza textu v3 pro Java | Microsoft Docs'
description: Začněte s knihovnou klienta V3 Analýza textu pro Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: a4db7c59bd43a541a350d78511c6749a914bec79
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735531"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://aka.ms/azsdk-java-textanalytics-ref-docs)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics)  |  [Balíček](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) s verzí 8 nebo vyšší
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod.  Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="add-the-client-library"></a>Přidat klientskou knihovnu

Vytvořte projekt Maven v upřednostňovaném prostředí IDE nebo vývojovém prostředí. Pak přidejte následující závislost do souboru *pom.xml* projektu. Syntaxi implementace [pro jiné nástroje buildu](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0) můžete najít online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), který obsahuje příklady kódu v tomto rychlém startu. 

Vytvořte soubor Java s názvem `TextAnalyticsSamples.java` . Otevřete soubor a přidejte následující `import` příkazy:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

V souboru Java přidejte novou třídu a přidejte klíč a koncový bod prostředku Azure, jak je znázorněno níže.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Do třídy přidejte následující metodu Main. Zde budete definovat metody, které jsou zde volány později.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis) 
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte metodu pro vytvoření instance `TextAnalyticsClient` objektu s klíčem a koncovým bodem pro váš prostředek analýza textu.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

V `main()` metodě programu zavolejte metodu ověřování pro vytvoření instance klienta.

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou funkci s názvem `sentimentAnalysisExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `analyzeSentiment()` funkci. Vrácený `AnalyzeSentimentResult` objekt bude obsahovat `documentSentiment` a `sentenceSentiments` v případě úspěchu, nebo `errorMessage` if not. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
```

### <a name="output"></a>Výstup

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte novou funkci s názvem `detectLanguageExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `detectLanguage()` funkci. Vrácený `DetectLanguageResult` objekt bude obsahovat nalezen primární jazyk, seznam dalších jazyků, který byl nalezen v případě úspěchu, nebo v `errorMessage` případě potřeby.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` . Chcete-li nastavit jinou výchozí hodnotu, nastavte `TextAnalyticsClientOptions.DefaultCountryHint` vlastnost a předejte ji během inicializace klienta.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Výstup

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

> [!NOTE]
> Ve verzi `3.0` :
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci s názvem `recognizeEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizeEntities()` funkci. Vrácený `RecognizeEntitiesResult` objekt bude obsahovat seznam `NamedEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci s názvem `recognizeLinkedEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizeLinkedEntities()` funkci. Vrácený `RecognizeLinkedEntitiesResult` objekt bude obsahovat seznam `LinkedEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `LinkedEntity` objektu jako seznam `LinkedEntityMatch` objektů.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Výstup

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```
## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou funkci s názvem `extractKeyPhrasesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `extractKeyPhrases()` funkci. Vrácený `ExtractKeyPhraseResult` objekt bude obsahovat seznam klíčových frází, pokud je to úspěšné, nebo `errorMessage` Pokud ne.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized phrases: 
cat
veterinarian
```
