---
title: 'Rychlý start: Klientská knihovna Analýzy textu v3 pro Javu | Microsoft Docs'
description: Začínáme s klientskou knihovnou Analýzy textu v3 pro Javu
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 11092b74c0256d298dece0f909d8d7dd241e7b13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371311"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Balíček](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Můžete si ho [vytvořit zdarma](https://azure.microsoft.com/free/).
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) verze 8 nebo novější
* Jakmile máte předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Vytvoření prostředku Analýzy textu"  target="_blank">vytvořte si prostředek Analýza textu<span class="docon docon-navigate-external x-hidden-focus"></span></a> na webu Azure Portal. Získáte klíč a koncový bod. 
    * Tento klíč a koncový bod z vytvářeného prostředku budete potřebovat pro připojení vaší aplikace k rozhraní API pro analýzu textu. Uděláte to v další fázi tohoto rychlého zprovoznění.
    * Můžete si službu vyzkoušet s bezplatnou cenovou úrovní a pro produkční prostředí později upgradovat na placenou úroveň.

## <a name="setting-up"></a>Nastavení

### <a name="add-the-client-library"></a>Přidání klientské knihovny

V oblíbeném vývojovém prostředí nebo integrovaném vývojovém prostředí (IDE) vytvořte projekt Maven. Pak do souboru *pom.xml* projektu přidejte následující závislost. Syntaxi pro implementaci [pro další nástroje pro sestavování](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) najdete online.

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
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java). 

Vytvořte soubor Java s názvem `TextAnalyticsSamples.java`. Otevřete tento soubor a přidejte do něj následující příkazy `import`:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

V souboru Java přidejte novou třídu a klíč a koncový bod vašeho prostředku Azure, jak je znázorněno níže.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Přidejte do třídy následující metodu main. Metody, které se tady volají, nadefinujete později.

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

Klient Analýzy textu je objekt `TextAnalyticsClient`, který se ověřuje pro Azure pomocí vašeho klíče a nabízí funkce umožňující příjem textu jako jednotlivých řetězců nebo dávky. Text můžete do rozhraní API odesílat synchronně nebo asynchronně. Objekt odpovědi bude obsahovat analytické informace o jednotlivých odeslaných dokumentech. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis) 
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Spojování entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte metodu, která vytvoří instanci objektu `TextAnalyticsClient` s klíčem a koncovým bodem vašeho prostředku Analýzy textu.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

V metodě `main()` programu zavolejte metodu ověřování a vytvořte instanci klienta.

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou funkci `sentimentAnalysisExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `analyzeSentiment()`. Pokud vše proběhne úspěšně, vrácený objekt `AnalyzeSentimentResult` bude obsahovat objekty `documentSentiment` a `sentenceSentiments`, jinak bude obsahovat objekt `errorMessage`. 

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

Vytvořte novou funkci `detectLanguageExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `detectLanguage()`. Pokud vše proběhne úspěšně, vrácený objekt `DetectLanguageResult` bude obsahovat zjištěný primární jazyk a seznam dalších zjištěných jazyků, jinak bude obsahovat objekt `errorMessage`.

> [!Tip]
> V některých případech může být obtížné jednoznačně rozpoznat jazyk na základě vstupu. Pomocí parametru `countryHint` můžete zadat dvoupísmenný kód země. Rozhraní API ve výchozím nastavení jako hodnotu countryHint používá US. Pokud chcete toto chování zrušit, můžete tento parametr resetovat nastavením této hodnoty na prázdný řetězec – `countryHint = ""`. Pokud chcete nastavit jinou výchozí hodnotu, nastavte vlastnost `TextAnalyticsClientOptions.DefaultCountryHint` a předejte ji během inicializace klienta.

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
## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER, Named Entity Recognition)

> [!NOTE]
> Ve verzi `3.0-preview`:
> * Rozpoznávání pojmenovaných entit zahrnuje samostatné metody pro detekci osobních údajů. 
> * Spojování entit je samostatný požadavek.

Vytvořte novou funkci `recognizeEntitiesExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `recognizeEntities()`. Pokud vše proběhne úspěšně, vrácený objekt `RecognizeEntitiesResult` bude obsahovat seznam objektů `NamedEntity`, jinak bude obsahovat objekt `errorMessage`.

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

## <a name="using-ner-to-recognize-personal-information"></a>Použití rozpoznávání pojmenovaných entit k rozpoznávání osobních údajů

Vytvořte novou funkci `recognizePIIEntitiesExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `recognizePiiEntities()`. Pokud vše proběhne úspěšně, vrácený objekt `RecognizePiiEntitiesResult` bude obsahovat seznam objektů `NamedEntity`, jinak bude obsahovat objekt `errorMessage`. 

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

## <a name="entity-linking"></a>Spojování entit

Vytvořte novou funkci `recognizeLinkedEntitiesExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `recognizeLinkedEntities()`. Pokud vše proběhne úspěšně, vrácený objekt `RecognizeLinkedEntitiesResult` bude obsahovat seznam objektů `LinkedEntity`, jinak bude obsahovat objekt `errorMessage`. Vzhledem k tomu, že jsou propojené entity jednoznačně identifikované, výskyty stejné entity se seskupují v rámci objektu `LinkedEntity` jako seznam objektů `LinkedEntityMatch`.

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

Vytvořte novou funkci `extractKeyPhrasesExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `extractKeyPhrases()`. Pokud vše proběhne úspěšně, vrácený objekt `ExtractKeyPhraseResult` bude obsahovat seznam klíčových frází, jinak bude obsahovat objekt `errorMessage`.

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
