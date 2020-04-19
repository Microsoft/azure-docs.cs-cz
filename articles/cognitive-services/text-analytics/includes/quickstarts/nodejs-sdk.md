---
title: 'Úvodní příručka: Klientská knihovna Text Analytics v3 pro soubor Node.js | Dokumenty společnosti Microsoft'
description: Začínáme s klientskou knihovnou v3 Text Analytics pro soubor Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 1414d86577e5aa17cb42762403b3767948c1e30c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642898"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

[v3 Referenční dokumentace](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 Zdrojový kód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | knihovny[v3 Balíček (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[v2 Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 Zdrojový kód](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | knihovny[v2 Balíček (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 Ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [souboru Node.js](https://nodejs.org/).
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod. Po nasazení klikněte na **Přejít na prostředek**.
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * Můžete použít bezplatnou cenovou úroveň (`F0`) vyzkoušet službu a upgradovat později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp 

cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci `package.json` uzlu se souborem. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalace klientské knihovny

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Nainstalujte `@azure/ai-text-analytics` balíčky NPM:

```console
npm install --save @azure/ai-text-analytics@1.0.0-preview.4
```

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), který obsahuje příklady kódu v tomto rychlém startu. 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Nainstalujte `@azure/cognitiveservices-textanalytics` balíčky NPM:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), který obsahuje příklady kódu v tomto rychlém startu. 

---

`package.json` Soubor aplikace bude aktualizován závislostmi.
Vytvořte soubor `index.js` s názvem a přidejte následující:

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektový model

Klient Text Analytics `TextAnalyticsClient` je objekt, který se ověřuje v Azure pomocí vašeho klíče. Klient poskytuje několik metod pro analýzu textu jako jeden řetězec nebo dávka.

Text je odeslán do rozhraní `documents`API jako `dictionary` seznam , což `id` `text`jsou `language` objekty obsahující kombinaci , a atributy v závislosti na použité metodě. Atribut `text` ukládá text, který má `language`být analyzován `id` v počátku , a může být libovolná hodnota. 

Objekt odpovědi je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověřování klienta](#client-authentication)
* [Analýza mínění](#sentiment-analysis) 
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="client-authentication"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte `TextAnalyticsClient` nový objekt s klíčem a koncovým bodem jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte nový objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) s `credentials` parametrem a `endpoint` jako parametr.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Volání metody klienta `analyzeSentiment()` a získat `SentimentBatchResult` vrácený objekt. Iterate prostřednictvím seznamu výsledků a vytisknout ID každého dokumentu, mínění na úrovni dokumentu s skóre spolehlivosti. Pro každý dokument výsledek obsahuje mínění na úrovni věty spolu s posuny, délkou a skóre spolehlivosti.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte seznam slovníkových objektů obsahující dokumenty, které chcete analyzovat. Volání [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) metody klienta a získat vrácené [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iterate prostřednictvím seznamu výsledků a vytisknout id každého dokumentu a skóre mínění. Skóre blíže k 0 označuje negativní sentiment, zatímco skóre blíže k 1 označuje pozitivní sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `detectLanguage()` klienta a `DetectLanguageResultCollection`získejte vrácenou . Poté výsledky iterujte a vytiskněte ID každého dokumentu s příslušným primárním jazykem.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte seznam slovníkových objektů obsahujících dokumenty. Volání metody [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) klienta a získejte vrácenou [metodu LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Poté výsledky iteřit a vytisknout ID každého dokumentu a jazyk.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Uznání pojmenované entity (NER)

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

> [!NOTE]
> Ve `3.0-preview`verzi :
> * Propojení entit je samostatný požadavek než NER.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Volání metody klienta `recognizeEntities()` a `RecognizeEntitiesResult` získat objekt. Iterate prostřednictvím seznamu výsledků a vytisknout název entity, typ, podtyp, posun, délka a skóre.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="entity-linking"></a>Entity Linking

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Volání metody klienta `recognizeLinkedEntities()` a `RecognizeLinkedEntitiesResult` získat objekt. Iterate prostřednictvím seznamu výsledků a vytisknout název entity, ID, zdroj dat, url a shody. Každý objekt `matches` v poli bude obsahovat posun, délku a skóre pro tento zápas.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

> [!NOTE]
> Ve verzi 2.1 je propojení entit zahrnuto v odpovědi ner.

Vytvořte seznam objektů obsahujících dokumenty. Volání [entity klienta()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) metoda a získat [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) objektu. Iterate prostřednictvím seznamu výsledků a vytisknout ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte její název wikipedie, typ a podtypy (pokud existují) a také umístění v původním textu.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Volání metody klienta `extractKeyPhrases()` a získat `ExtractKeyPhrasesResult` vrácený objekt. Iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a všechny zjištěné klíčové fráze.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte seznam objektů obsahujících dokumenty. Volání metody [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) klienta a získejte vrácený objekt [KeyPhraseBatchResult.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) Iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a všechny zjištěné klíčové fráze.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Spusťte `node index.js` kód v okně konzole.

### <a name="output"></a>Výstup

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node` v souboru quickstart.

```console
node index.js
```
