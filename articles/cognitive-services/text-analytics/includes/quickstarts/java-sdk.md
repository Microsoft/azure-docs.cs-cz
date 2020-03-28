---
title: 'Úvodní příručka: Klientská knihovna Text Analytics v3 pro Javu | Dokumenty společnosti Microsoft'
description: Začínáme s klientskou knihovnou v3 Text Analytics pro Jazyk Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: a0e6b5b7d5cedc821ee34bdd219ae07bb9d43199
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481904"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [knihovny](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) s verzí 8 nebo vyšší
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod.  Po nasazení klikněte na **Přejít na prostředek**.
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * Můžete použít bezplatnou cenovou úroveň (`F0`) vyzkoušet službu a upgradovat později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="add-the-client-library"></a>Přidání klientské knihovny

Vytvořte projekt Maven ve vašem upřednostňovaném prostředí IDE nebo vývojovém prostředí. Potom přidejte následující závislost do souboru *pom.xml* projektu. Syntaxi implementace [pro jiné nástroje sestavení](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) najdete online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), který obsahuje příklady kódu v tomto rychlém startu. 

Vytvořte soubor `TextAnalyticsSamples.java`Java s názvem . Otevřete soubor a `import` přidejte následující příkazy:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

V souboru java přidejte novou třídu a přidejte klíč a koncový bod vašeho prostředku Azure, jak je znázorněno níže.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Přidejte následující hlavní metodu do třídy. Zde volané metody budete později.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objektový model

Klient Text Analytics `TextAnalyticsClient` je objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro přijímání textu jako jednotlivé řetězce nebo jako dávka. Text můžete odeslat do rozhraní API synchronně nebo asynchronně. Objekt odpovědi bude obsahovat informace o analýze pro každý odeslané dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis) 
* [Detekce jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte metodu pro vytvoření `TextAnalyticsClient` instance objektu pomocí klíče a koncového bodu pro prostředek analýzy textu.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

V `main()` metodě programu volejte metodu ověřování k vytvoření instance klienta.

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou `sentimentAnalysisExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `analyzeSentiment()` funkce. Vrácený `AnalyzeSentimentResult` objekt `documentSentiment` bude `sentenceSentiments` obsahovat a `errorMessage` pokud je úspěšný, nebo pokud ne. 

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

Vytvořte novou `detectLanguageExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `detectLanguage()` funkce. Vrácený `DetectLanguageResult` objekt bude obsahovat zjištěný primární jazyk, seznam dalších jazyků zjištěných v případě úspěchu `errorMessage` nebo pokud ne.

> [!Tip]
> V některých případech může být obtížné rozptýlit jazyky na základě vstupu. `countryHint` Parametr můžete použít k určení dvoupísmenný kód země. Ve výchozím nastavení rozhraní API používá "US" jako výchozí zeměHint, chcete-li odebrat toto chování, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""`. Chcete-li nastavit jiné `TextAnalyticsClientOptions.DefaultCountryHint` výchozí nastavení, nastavte vlastnost a předat ji během inicializace klienta.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Výstup

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

> [!NOTE]
> Ve `3.0-preview`verzi :
> * Ner obsahuje samostatné metody pro zjišťování osobních údajů. 
> * Propojení entit je samostatný požadavek než NER.

Vytvořte novou `recognizeEntitiesExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `recognizeEntities()` funkce. Vrácený `RecognizeEntitiesResult` objekt bude obsahovat seznam `NamedEntity` v `errorMessage` případě úspěšné, nebo pokud ne.

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
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>Použití funkce NER k rozpoznání osobních údajů

Vytvořte novou `recognizePIIEntitiesExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `recognizePiiEntities()` funkce. Vrácený `RecognizePiiEntitiesResult` objekt bude obsahovat seznam `NamedEntity` v `errorMessage` případě úspěšné, nebo pokud ne. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Propojení entit

Vytvořte novou `recognizeLinkedEntitiesExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `recognizeLinkedEntities()` funkce. Vrácený `RecognizeLinkedEntitiesResult` objekt bude obsahovat seznam `LinkedEntity` v `errorMessage` případě úspěšné, nebo pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, výskyty stejné entity jsou seskupeny pod objektem `LinkedEntity` jako seznam `LinkedEntityMatch` objektů.

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
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
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
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou `extractKeyPhrasesExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `extractKeyPhrases()` funkce. Vrácený `ExtractKeyPhraseResult` objekt bude obsahovat seznam klíčových frází, pokud je úspěšný, nebo `errorMessage` pokud ne.

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
