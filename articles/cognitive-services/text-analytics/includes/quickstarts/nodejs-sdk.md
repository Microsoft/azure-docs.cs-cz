---
title: 'Rychlý Start: Klientská knihovna Analýza textu v3 pro Node. js | Microsoft Docs'
description: Začínáme s klientskou knihovnou V3 Analýza textu pro Node. js
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/26/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 9f4c5e8d491bcff420e354657ff7302a22cd7583
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155444"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

[Referenční dokumentace v3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [zdrojový kód knihovny v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 Package (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [V3 Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [zdrojového kódu knihovny v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 Samples](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org/).
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="vytvořit prostředek Analýza textu"  target="_blank">vytvořit Analýza textu</a> prostředků <span class="docon docon-navigate-external x-hidden-focus"></span> v Azure Portal a získat tak klíč a koncový bod. 
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Provedete to později v rychlém startu.
    * Pomocí cenové úrovně Free můžete službu vyzkoušet a později upgradovat na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

> [!NOTE]
> Tuto verzi klientské knihovny Analýza textu můžete také spustit [v prohlížeči](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `npm init` pro vytvoření aplikace uzlu se souborem `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalace klientské knihovny

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Instalace balíčků `@azure/ai-text-analytics` NPM:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Instalace balíčků `@azure/cognitiveservices-textanalytics` NPM:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Soubor `package.json` vaší aplikace bude aktualizován pomocí závislostí.

Vytvořte soubor s názvem `index.js` a přidejte následující knihovny:

---

Soubor `package.json` vaší aplikace bude aktualizován pomocí závislostí.
Vytvořte soubor s názvem `index.js` a přidejte následující knihovny:

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se pomocí vašeho klíče ověřuje v Azure. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku.

Text se pošle do rozhraní API jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověřování klienta](#client-authentication)
* [Analýza mínění](#sentiment-analysis) 
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="client-authentication"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Vytvořte nový objekt `TextAnalyticsClient` s vaším klíčem a koncovým bodem jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvoří nový objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) s `credentials` a `endpoint` jako parametr.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `analyzeSentiment()` klienta a získejte vrácený objekt `SentimentBatchResult`. Procházejte seznamem výsledků a vytiskněte ID každého dokumentu, mínění na úrovni dokumentu s výsledky spolehlivosti. Výsledek každého dokumentu obsahuje mínění na úrovni věty spolu s posuny, délkou a hodnocením spolehlivosti.

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
        console.log(`\t\tPositive: ${document.sentimentScores.positive.toFixed(2)} \tNegative: ${document.sentimentScores.negative.toFixed(2)} \tNeutral: ${document.sentimentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentimentScores.positive.toFixed(2)} \tNegative: ${sentence.sentimentScores.negative.toFixed(2)} \tNeutral: ${sentence.sentimentScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Spusťte kód pomocí `node index.js` v okně konzoly.

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
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte seznam objektů slovníku obsahující dokumenty, které chcete analyzovat. Zavolejte metodu [mínění ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) klienta a získejte vrácenou [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Procházejte seznamem výsledků a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `detectLanguage()` klienta a získejte vrácenou `DetectLanguageResultCollection`. Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu s příslušným primárním jazykem.

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

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte seznam objektů slovníku obsahující vaše dokumenty. Zavolejte metodu [operaci DetectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) klienta a získejte vrácenou [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Potom Iterujte výsledky a vytiskněte ID a jazyk každého dokumentu.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0-preview`:
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizeEntities()` klienta a získejte objekt `RecognizeEntitiesResult`. Iterujte seznamem výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.

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
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Offset: 25, Length: 10  Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Offset: 40, Length: 10  Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Offset: 95, Length: 12  Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 21, Length: 9   Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Offset: 60, Length: 7   Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Offset: 71, Length: 2   Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
```

## <a name="using-ner-to-detect-personal-information"></a>Použití NER k detekci osobních údajů

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizePiiEntities()` klienta a získejte objekt `EntitiesBatchResult`. Iterujte seznamem výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entity Linking

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizeLinkedEntities()` klienta a získejte objekt `RecognizeLinkedEntitiesResult`. Iterujte seznamem výsledků a vytiskněte název entity, ID, zdroj dat, adresu URL a shodu. Každý objekt v poli `matches` bude pro tuto shodu obsahovat posun, délku a skóre.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.777
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.555
                Text: Gates
                Offset: 161, Length: 5  Score: 0.555
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.533
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.469
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.469
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.248
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.281
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Vytvořte seznam objektů, které obsahují vaše dokumenty. Zavolejte metodu entity klienta [()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) a získejte objekt [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) . Iterujte seznamem výsledků a vytiskněte ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte název Wikipedii, typ a dílčí typy (pokud existuje) a také umístění v původním textu.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Spusťte kód pomocí `node index.js` v okně konzoly.

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

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `extractKeyPhrases()` klienta a získejte vrácený objekt `ExtractKeyPhrasesResult`. Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte seznam objektů, které obsahují vaše dokumenty. Zavolejte metodu klíčová [fráze klienta ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) a získejte vrácený objekt [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) . Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Spusťte kód pomocí `node index.js` v okně konzoly.

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

Spusťte aplikaci pomocí příkazu `node` v souboru rychlého startu.

```console
node index.js
```
