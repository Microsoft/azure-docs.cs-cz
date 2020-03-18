---
title: 'Rychlý start: Klientská knihovna Analýza textu v3 pro Node.js | Microsoft Docs'
description: Začínáme s klientskou knihovnou Analýza textu v3 pro Node.js
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 8a045881a0533054633670349c22325851178b70
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371310"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

[Referenční dokumentace k v3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Zdrojový kód knihovny v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Balíček v3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Ukázky v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[Referenční dokumentace k v2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Zdrojový kód knihovny v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Balíček v2 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Ukázky v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Můžete si ho [vytvořit zdarma](https://azure.microsoft.com/free/).
* Aktuální verze [Node.js](https://nodejs.org/).
* Jakmile máte předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Vytvoření prostředku Analýzy textu"  target="_blank">vytvořte si prostředek Analýza textu<span class="docon docon-navigate-external x-hidden-focus"></span></a> na webu Azure Portal. Získáte klíč a koncový bod. 
    * Tento klíč a koncový bod z vytvářeného prostředku budete potřebovat pro připojení vaší aplikace k rozhraní API pro analýzu textu. Uděláte to v další fázi tohoto rychlého zprovoznění.
    * Můžete si službu vyzkoušet s bezplatnou cenovou úrovní a pro produkční prostředí později upgradovat na placenou úroveň.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte pro vaši aplikaci nový adresář a přejděte do něj. 

```console
mkdir myapp 

cd myapp
```

Spuštěním příkazu `npm init` vytvoříte aplikaci uzlu se souborem `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalace klientské knihovny

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Nainstalujte balíčky NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics
```

> [!TIP]
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js). 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Nainstalujte balíčky NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js). 

---

Soubor `package.json` vaší aplikace se bude aktualizovat s využitím závislostí.
Vytvořte soubor s názvem `index.js` a přidejte do něj následující:

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
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

Klient Analýzy textu je objekt `TextAnalyticsClient`, který se ověřuje pro Azure pomocí vašeho klíče. Tento klient nabízí několik metod analýzy textu, a to ve formě jednoho řetězce nebo dávky.

Text se do rozhraní API odesílá ve formě seznamu `documents`, což jsou objekty `dictionary` obsahující kombinaci atributů `id`, `text` a `language` (v závislosti na zvolené metodě). Atribut `text` ukládá text, který se má analyzovat v původním jazyce (`language`) a `id` může být libovolná hodnota. 

Objekt odpovědi je seznam obsahující analytické informace pro jednotlivé dokumenty. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#client-authentication)
* [Analýza mínění](#sentiment-analysis) 
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Spojování entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="client-authentication"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte nový objekt `TextAnalyticsClient` a jako parametry použijte váš klíč a koncový bod.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte nový objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) s využitím `credentials` a jako parametr použijte `endpoint`.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `analyzeSentiment()` klienta. Získáte vrácený objekt `SentimentBatchResult`. Iterujte přes seznam výsledků a vytiskněte ID každého dokumentu, mínění na úrovni dokumentu a skóre spolehlivosti. Pro každý dokument tyto výsledky obsahují mínění na úrovni jednotlivých vět společně s posuny, délkou a skóre spolehlivosti.

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

Spusťte kód s `node index.js` v okně konzoly.

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

Vytvořte seznam slovníkových objektů obsahujících dokumenty, které chcete analyzovat. Zavolejte metodu [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) klienta. Získáte vrácený objekt [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a skóre mínění. Skóre blížící se 0 značí negativní mínění, zatímco skóre blížící se 1 značí pozitivní mínění.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Spusťte kód s `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `detectLanguage()` klienta. Získáte vrácený objekt `DetectLanguageResultCollection`. Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu spolu s příslušným primárním jazykem.

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

Spusťte kód s `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte seznam slovníkových objektů obsahujících vaše dokumenty. Zavolejte metodu [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) klienta. Získáte vrácený objekt [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a jazyk.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Spusťte kód s `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER, Named Entity Recognition)

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0-preview`:
> * Rozpoznávání pojmenovaných entit zahrnuje samostatné metody pro detekci osobních údajů. 
> * Spojování entit je samostatný požadavek.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizeEntities()` klienta. Získáte objekt `RecognizeEntitiesResult`. Iterujte přes seznam výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.

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

Spusťte kód s `node index.js` v okně konzoly.

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

## <a name="using-ner-to-detect-personal-information"></a>Použití rozpoznávání pojmenovaných entit k detekci osobních údajů

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizePiiEntities()` klienta. Získáte objekt `EntitiesBatchResult`. Iterujte přes seznam výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.


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
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Spusťte kód s `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>Entity Linking

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizeLinkedEntities()` klienta. Získáte objekt `RecognizeLinkedEntitiesResult`. Iterujte přes seznam výsledků a vytiskněte název entity, ID, zdroj dat, adresu URL a shody. Každý objekt v poli `matches` bude obsahovat posun, délku a skóre pro příslušnou shodu.

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

Spusťte kód s `node index.js` v okně konzoly.

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
> Ve verzi 2.1 je spojování entit součástí odpovědi NER.

Vytvořte seznam objektů obsahujících vaše dokumenty. Zavolejte metodu [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) klienta. Získáte objekt [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Iterujte přes seznam výsledků a vytiskněte ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte odpovídající název na Wikipedii, typ a podtypy (pokud existují) a také umístění v původním textu.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Spusťte kód s `node index.js` v okně konzoly.

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

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `extractKeyPhrases()` klienta. Získáte vrácený objekt `ExtractKeyPhrasesResult`. Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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

Spusťte kód s `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte seznam objektů obsahujících vaše dokumenty. Zavolejte metodu [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) klienta. Získáte vrácený objekt [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult). Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Spusťte kód s `node index.js` v okně konzoly.

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

Spusťte aplikaci pomocí příkazu `node` pro soubor rychlého startu.

```console
node index.js
```
