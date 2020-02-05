---
title: 'Rychlý Start: Klientská knihovna Analýza textu v3 pro Java | Microsoft Docs'
description: Začněte s knihovnou klienta V3 Analýza textu pro Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: c5898b0c05400904bc3c9e6a6bc318beac76d93c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987881"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://aka.ms/azsdk-java-textanalytics-ref-docs) |  | [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) [(Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) s verzí 8 nebo vyšší


[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-maven-project"></a>Vytvořit nový projekt Maven

Přidejte do projektu následující závislost analýzy textu. Tato verze závislosti používá `3.0-preview` verze rozhraní API pro analýzu textu. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
</dependencies>
```

Vytvořte nový soubor Java v následujícím adresáři: `\src\main\java`.

Otevřete soubor Java a přidejte následující příkazy `import`:

```java
import com.azure.ai.textanalytics.models.AnalyzeSentimentResult;
import com.azure.ai.textanalytics.models.DetectLanguageResult;
import com.azure.ai.textanalytics.models.DetectedLanguage;
import com.azure.ai.textanalytics.models.ExtractKeyPhraseResult;
import com.azure.ai.textanalytics.models.LinkedEntity;
import com.azure.ai.textanalytics.models.LinkedEntityMatch;
import com.azure.ai.textanalytics.models.NamedEntity;
import com.azure.ai.textanalytics.models.RecognizeEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizeLinkedEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizePiiEntitiesResult;
import com.azure.ai.textanalytics.models.TextSentiment;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
import java.util.List;
```

V souboru Java přidejte novou třídu a přidejte klíč a koncový bod prostředku Azure, jak je znázorněno níže.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSample {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Do třídy přidejte následující metodu Main. Zde budete definovat metody, které jsou zde volány později.

```java
public static void main(String[] args) {
    
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

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza subjektivního hodnocení](#sentiment-analysis) 
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte metodu pro vytvoření instance objektu `TextAnalyticsClient` pomocí `KEY` a `ENDPOINT` vytvořili výše.

```java
static TextAnalyticsClient authenticateClient(String subscriptionKey, String endpoint) {
    return new TextAnalyticsClientBuilder()
    .subscriptionKey(subscriptionKey)
    .endpoint(endpoint)
    .buildClient();
}
```

V metodě `main()` programu zavolejte metodu ověřování pro vytvoření instance klienta.

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

Vytvořte novou funkci nazvanou `sentimentAnalysisExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `analyzeSentiment()`. Vrácený objekt `AnalyzeSentimentResult` bude obsahovat `documentSentiment` a `sentenceSentiments` v případě úspěchu nebo `errorMessage`, pokud ne. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    AnalyzeSentimentResult sentimentResult = client.analyzeSentiment(text);
    TextSentiment documentSentiment = sentimentResult.getDocumentSentiment();
    System.out.printf(
        "Recognized TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
        documentSentiment.getTextSentimentClass(),
        documentSentiment.getPositiveScore(),
        documentSentiment.getNeutralScore(),
        documentSentiment.getNegativeScore());

    List<TextSentiment> sentiments = sentimentResult.getSentenceSentiments();
    for (TextSentiment textSentiment : sentiments) {
        System.out.printf(
            "Recognized Sentence TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
            textSentiment.getTextSentimentClass(),
            textSentiment.getPositiveScore(),
            textSentiment.getNeutralScore(),
            textSentiment.getNegativeScore());
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: neutral, Positive Score: 0.21, Neutral Score: 0.77, Negative Score: 0.02.
```
## <a name="language-detection"></a>Detekce jazyka

Vytvořte novou funkci nazvanou `detectLanguageExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `detectLanguage()`. Vrácený objekt `DetectLanguageResult` bude obsahovat nalezený primární jazyk, seznam dalších jazyků zjištěných v případě úspěchu nebo `errorMessage`, pokud ne.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí parametru `countryHint` můžete zadat kód země se dvěma písmeny. Ve výchozím nastavení používá rozhraní API "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""`. Chcete-li nastavit jinou výchozí hodnotu, nastavte vlastnost `TextAnalyticsClientOptions.DefaultCountryHint` a předejte ji během inicializace klienta.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectLanguageResult detectLanguageResult = client.detectLanguage(text, "US");
    DetectedLanguage detectedDocumentLanguage = detectLanguageResult.getPrimaryLanguage();
    System.out.printf("Language: %s, ISO 6391 Name: %s, Score: %s.%n",
        detectedDocumentLanguage.getName(),
        detectedDocumentLanguage.getIso6391Name(),
        detectedDocumentLanguage.getScore());
}
```

### <a name="output"></a>Výstup

```console
Language: French, ISO 6391 Name: fr, Score: 1.0.
```
## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

> [!NOTE]
> Ve verzi `3.0-preview`:
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci nazvanou `recognizeEntitiesExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `recognizeEntities()`. Vrácený objekt `RecognizeEntitiesResult` bude obsahovat seznam `NamedEntity`, pokud je to úspěšné, nebo `errorMessage`, pokud ne.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "I had a wonderful trip to Seattle last week.";
    
    RecognizeEntitiesResult recognizeEntitiesResult = client.recognizeEntities(text);

    for (NamedEntity entity : recognizeEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Recognized NamedEntity Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %.3f.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized NamedEntity Text: Seattle, Type: Location, Subtype: N/A, Offset: 26, Length: 7, Score: 0.806.
Recognized NamedEntity Text: last week, Type: DateTime, Subtype: DateRange, Offset: 34, Length: 9, Score: 0.800.
```

## <a name="using-ner-to-detect-personal-information"></a>Použití NER k detekci osobních údajů

Vytvořte novou funkci nazvanou `recognizePIIEntitiesExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `recognizePiiEntities()`. Vrácený objekt `RecognizePiiEntitiesResult` bude obsahovat seznam `NamedEntity`, pokud je to úspěšné, nebo `errorMessage`, pokud ne. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    
    RecognizePiiEntitiesResult recognizePIIEntitiesResult = client.recognizePiiEntities(text);

    for (NamedEntity entity : recognizePIIEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Personally Identifiable Information Entities Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %s.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Výstup

```console
Personally Identifiable Information Entities
Text: 123-12-1234, Type: U.S. Social Security Number (SSN), Subtype: N/A, Offset: 33, Length: 11, Score: 0.85.
```

## <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci nazvanou `recognizeLinkedEntitiesExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `recognizeLinkedEntities()`. Vrácený objekt `RecognizeLinkedEntitiesResult` bude obsahovat seznam `LinkedEntity`, pokud je to úspěšné, nebo `errorMessage`, pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny pod objektem `LinkedEntity` jako seznam objektů `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";
    
    RecognizeLinkedEntitiesResult recognizeLinkedEntitiesResult = client.recognizeLinkedEntities(text);

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : recognizeLinkedEntitiesResult.getLinkedEntities()) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("tMatches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                linkedEntityMatch.getText(),
                linkedEntityMatch.getOffset(),
                linkedEntityMatch.getLength(),
                linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Výstup

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
tMatches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.65.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
tMatches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.24.
Text: Gates, Offset: 5, Length: 161, Score: 0.24.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
tMatches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.17.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
tMatches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.20.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.20.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
tMatches:
Text: April 4, Offset: 7, Length: 54, Score: 0.14.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
tMatches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.05.
```
## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou funkci nazvanou `extractKeyPhrasesExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `extractKeyPhrases()`. Vrácený objekt `ExtractKeyPhraseResult` bude obsahovat seznam klíčových frází, pokud je to úspěšné, nebo `errorMessage`, pokud ne.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";
    
    ExtractKeyPhraseResult keyPhraseResult = client.extractKeyPhrases(text);

    for (String keyPhrase : keyPhraseResult.getKeyPhrases()) {
        System.out.printf("Recognized Phrases: %s.%n", keyPhrase);
    }
}
```

### <a name="output"></a>Výstup

```console
Recognized Phrases: cat.
Recognized Phrases: veterinarian.
```
