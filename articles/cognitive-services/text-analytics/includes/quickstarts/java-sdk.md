---
title: 'Rychlý Start: Klientská knihovna Analýza textu v3 pro Java | Microsoft Docs'
description: Začněte s knihovnou klienta V3 Analýza textu pro Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: f9f5a8904ff8038b0747fa8f086bc9894971428c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332299"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

[Referenční dokumentace](https://aka.ms/azsdk-java-textanalytics-ref-docs)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics)  |  [Balíček](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Referenční dokumentace](https://aka.ms/azsdk-java-textanalytics-ref-docs)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Balíček](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tento článek popisuje pouze verzi 3. x rozhraní API.

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) s verzí 8 nebo vyšší
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod.  Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="add-the-client-library"></a>Přidat klientskou knihovnu

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte projekt Maven v upřednostňovaném prostředí IDE nebo vývojovém prostředí. Pak přidejte následující závislost do souboru *pom.xml* projektu. Syntaxi implementace [pro jiné nástroje buildu](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) můžete najít online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.1</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte projekt Maven v upřednostňovaném prostředí IDE nebo vývojovém prostředí. Pak přidejte následující závislost do souboru *pom.xml* projektu. Syntaxi implementace [pro jiné nástroje buildu](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) můžete najít online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), který obsahuje příklady kódu v tomto rychlém startu. 

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tento článek popisuje pouze verzi 3. x rozhraní API.

---

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

# <a name="version-31-preview"></a>[Verze 3,1 (Preview)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

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

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tento článek popisuje pouze verzi 3. x rozhraní API.

---


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

Vytvořte metodu pro vytvoření instance `TextAnalyticsClient` objektu s klíčem a koncovým bodem pro váš prostředek analýza textu. Tento příklad je stejný pro verze 3,0 a 3,1 rozhraní API.

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

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> Ve verzi `3.1` :
> * Analýza mínění obsahuje názory na analýzu dolování, která je volitelným příznakem. 
> * Dolování stanovisek obsahuje aspekty a mínění úrovně názoru. 

Chcete-li provést analýzu mínění s využitím dolování, vytvořte novou funkci s názvem `sentimentAnalysisWithOpinionMiningExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `analyzeSentiment()` funkci s nastavením objektu Option `AnalyzeSentimentOptions` . Vrácený `AnalyzeSentimentResult` objekt bude obsahovat `documentSentiment` a `sentenceSentiments` v případě úspěchu, nebo `errorMessage` if not. 

```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The Document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "\tRecognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\t\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
            }
        });
    });
}
```

### <a name="output"></a>Výstup

```console
Text = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
    Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: atmosphere
                'negative' opinion sentiment because of "bad". Is the opinion negated: false.
        Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: Staff
                'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
                'negative' opinion sentiment because of "helpful". Is the opinion negated: true.

Process finished with exit code 0
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

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
}
```

### <a name="output"></a>Výstup

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tento článek popisuje pouze verzi 3. x rozhraní API.

---

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte novou funkci s názvem `detectLanguageExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `detectLanguage()` funkci. Vrácený `DetectLanguageResult` objekt bude obsahovat nalezen primární jazyk, seznam dalších jazyků, který byl nalezen v případě úspěchu, nebo v `errorMessage` případě potřeby. Tento příklad je stejný pro verze 3,0 a 3,1 rozhraní API.

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

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> Ve verzi `3.1` :
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci s názvem `recognizeEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizeEntities()` funkci. Vrácený `CategorizedEntityCollection` objekt bude obsahovat seznam `CategorizedEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci s názvem `recognizeLinkedEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizeLinkedEntities()` funkci. Vrácený `LinkedEntityCollection` objekt bude obsahovat seznam `LinkedEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `LinkedEntity` objektu jako seznam `LinkedEntityMatch` objektů.


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
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Výstup

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Rozpoznávání identifikovatelných osobních údajů

Vytvořte novou funkci s názvem `recognizePiiEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizePiiEntities()` funkci. Vrácený `PiiEntityCollection` objekt bude obsahovat seznam `PiiEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne. Bude také obsahovat text redigování, který se skládá ze vstupního textu se všemi identifikovatelnými entitami nahrazenými `*****` .

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Výstup

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0` :
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci s názvem `recognizeEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizeEntities()` funkci. Vrácený `CategorizedEntityCollection` objekt bude obsahovat seznam `CategorizedEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne.

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

### <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci s názvem `recognizeLinkedEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `recognizeLinkedEntities()` funkci. Vrácený `LinkedEntityCollection` objekt bude obsahovat seznam `LinkedEntity` úspěšného dokončení, nebo `errorMessage` Pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `LinkedEntity` objektu jako seznam `LinkedEntityMatch` objektů.

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

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tento článek popisuje pouze verzi 3. x rozhraní API.

---

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou funkci s názvem `extractKeyPhrasesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `extractKeyPhrases()` funkci. Vrácený `ExtractKeyPhraseResult` objekt bude obsahovat seznam klíčových frází, pokud je to úspěšné, nebo `errorMessage` Pokud ne. Tento příklad je stejný pro verze 3,0 a 3,1 rozhraní API.

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