---
title: 'Rychlý Start: Klientská knihovna Analýza textu v3 pro Node.js | Microsoft Docs'
description: Začínáme s klientskou knihovnou V3 Analýza textu pro Node.js
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 2a5bc5b6f9e2cb9a3f6895847dd8f03bc2540e18
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515102"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

[Referenční dokumentace V3](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  knihovny v3 [balíček V3 (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [ukázky V3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Referenční dokumentace V3](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  knihovny v3 [balíček V3 (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [ukázky V3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](/javascript/api/@azure/cognitiveservices-textanalytics)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)  |  knihovny v2 [balíček v2 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics)  |  [ukázky v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org/).
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Pokud chcete použít funkci analyzovat, budete potřebovat prostředek Analýza textu s cenovou úrovní Standard (y).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp 

cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalace klientské knihovny

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Instalace `@azure/ai-text-analytics` balíčků npm:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.3
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), který obsahuje příklady kódu v tomto rychlém startu. 


# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Instalace `@azure/ai-text-analytics` balíčků npm:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), který obsahuje příklady kódu v tomto rychlém startu. 

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Instalace `@azure/cognitiveservices-textanalytics` balíčků npm:

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), který obsahuje příklady kódu v tomto rychlém startu. 

---

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.
Vytvořte soubor s názvem `index.js` a přidejte následující:

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku.

Text se pošle do rozhraní API jako seznam `documents` objektů, což jsou `dictionary` objekty obsahující kombinaci `id` `text` atributů, a `language` v závislosti na použité metodě. `text`Atribut ukládá text, který má být analyzován v původním umístění `language` , a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověřování klienta](#client-authentication)
* [Analýza mínění](#sentiment-analysis) 
* [Dolování názoru](#opinion-mining)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* Identifikovatelné osobní údaje
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="client-authentication"></a>Ověření klienta

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte nový `TextAnalyticsClient` objekt s vaším klíčem a koncovým bodem jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte nový `TextAnalyticsClient` objekt s vaším klíčem a koncovým bodem jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte nový objekt [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) s `credentials` `endpoint` parametrem a jako parametr.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `analyzeSentiment()` metodu klienta a získejte vrácený `SentimentBatchResult` objekt. Procházejte seznamem výsledků a vytiskněte ID každého dokumentu, mínění na úrovni dokumentu s výsledky spolehlivosti. Výsledek každého dokumentu obsahuje mínění na úrovni věty spolu s posuny, délkou a hodnocením spolehlivosti.

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

Spusťte kód `node index.js` v okně konzoly.

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

### <a name="opinion-mining"></a>Dolování názoru

Chcete-li provést analýzu mínění s využitím dolování stanovisek, vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `analyzeSentiment()` metodu klienta s přidáním příznaku možnosti `includeOpinionMining: true` a získejte vráceného `SentimentBatchResult` objektu. Procházejte seznamem výsledků a vytiskněte ID každého dokumentu, mínění na úrovni dokumentu s výsledky spolehlivosti. Výsledek pro každý dokument obsahuje nejen mínění úrovně vět, jak je uvedeno výše, ale také aspekt a úroveň názoru mínění.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

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
            console.log("\tMined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`\t\tAspect text: ${aspect.text}`);
                console.log(`\t\tAspect sentiment: ${aspect.sentiment}`);
                console.log(`\t\tAspect Positive: ${aspect.confidenceScores.positive.toFixed(2)} \tNegative: ${aspect.confidenceScores.negative.toFixed(2)}`);
                console.log("\t\tAspect opinions:");
                for (const { text, sentiment, confidenceScores } of opinions) {
                    console.log(`\t\tOpinion text: ${text}`);
                    console.log(`\t\tOpinion sentiment: ${sentiment}`);
                    console.log(`\t\tOpinion Positive: ${confidenceScores.positive.toFixed(2)} \tNegative: ${confidenceScores.negative.toFixed(2)}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Sentences Sentiment(1):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Mined opinions
                Aspect text: food
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: service
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: concierge
                Aspect sentiment: positive
                Aspect Positive: 1.00   Negative: 0.00
                Aspect opinions:
                Opinion text: nice
                Opinion sentiment: positive
                Opinion Positive: 1.00  Negative: 0.00
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `analyzeSentiment()` metodu klienta a získejte vrácený `SentimentBatchResult` objekt. Procházejte seznamem výsledků a vytiskněte ID každého dokumentu, mínění na úrovni dokumentu s výsledky spolehlivosti. Výsledek každého dokumentu obsahuje mínění na úrovni věty spolu s posuny, délkou a hodnocením spolehlivosti.

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

Spusťte kód `node index.js` v okně konzoly.

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

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte seznam objektů slovníku obsahující dokumenty, které chcete analyzovat. Zavolejte metodu [mínění ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) klienta a získejte vrácenou [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Procházejte seznamem výsledků a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `detectLanguage()` metodu klienta a získejte vrácenou metodu `DetectLanguageResultCollection` . Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu s příslušným primárním jazykem.

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

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `detectLanguage()` metodu klienta a získejte vrácenou metodu `DetectLanguageResultCollection` . Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu s příslušným primárním jazykem.

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

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte seznam objektů slovníku obsahující vaše dokumenty. Zavolejte metodu [operaci DetectLanguage ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) klienta a získejte vrácenou [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Potom Iterujte výsledky a vytiskněte ID a jazyk každého dokumentu.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> Ve verzi `3.1` :
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `recognizeEntities()` metodu klienta a získejte `RecognizeEntitiesResult` objekt. Iterujte seznamem výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.

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
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `recognizeLinkedEntities()` metodu klienta a získejte `RecognizeLinkedEntitiesResult` objekt. Iterujte seznamem výsledků a vytiskněte název entity, ID, zdroj dat, adresu URL a shodu. Každý objekt v `matches` poli bude obsahovat posun, délku a skóre pro danou shodu.

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
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Rozpoznávání identifikačních údajů (PII)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `recognizePiiEntities()` metodu klienta a získejte `RecognizePIIEntitiesResult` objekt. Iterujte seznamem výsledků a vytiskněte název entity, typ a skóre.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0` :
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `recognizeEntities()` metodu klienta a získejte `RecognizeEntitiesResult` objekt. Iterujte seznamem výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.

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
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `recognizeLinkedEntities()` metodu klienta a získejte `RecognizeLinkedEntitiesResult` objekt. Iterujte seznamem výsledků a vytiskněte název entity, ID, zdroj dat, adresu URL a shodu. Každý objekt v `matches` poli bude obsahovat posun, délku a skóre pro danou shodu.

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
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Vytvořte seznam objektů, které obsahují vaše dokumenty. Zavolejte metodu entity klienta [()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) a získejte objekt [EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) . Iterujte seznamem výsledků a vytiskněte ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte název Wikipedii, typ a dílčí typy (pokud existuje) a také umístění v původním textu.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Spusťte kód `node index.js` v okně konzoly.

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

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `extractKeyPhrases()` metodu klienta a získejte vrácený `ExtractKeyPhrasesResult` objekt. Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte `extractKeyPhrases()` metodu klienta a získejte vrácený `ExtractKeyPhrasesResult` objekt. Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte seznam objektů, které obsahují vaše dokumenty. Zavolejte metodu klíčová [fráze klienta ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) a získejte vrácený objekt     [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) . Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Spusťte kód `node index.js` v okně konzoly.

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

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchronní použití rozhraní API s operací analyzovat

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

> [!CAUTION]
> Chcete-li použít příkaz analyzovat operace, je nutné použít prostředek Analýza textu s cenovou úrovní Standard (y).  

Vytvořte novou funkci nazvanou `analyze_example()` , která volá `beginAnalyze()` funkci. Výsledkem bude dlouhodobá operace, která se bude dotazovat na výsledky.

```javascript
const documents = [
  "Microsoft was founded by Bill Gates and Paul Allen.",
];

async function analyze_example(client) {
  console.log("== Analyze Sample ==");

  const tasks = {
    entityRecognitionTasks: [{ modelVersion: "latest" }]
  };
  const poller = await client.beginAnalyze(documents, tasks);
  const resultPages = await poller.pollUntilDone();

  for await (const page of resultPages) {
    const entitiesResults = page.entitiesRecognitionResults![0];
    for (const doc of entitiesResults) {
      console.log(`- Document ${doc.id}`);
      if (!doc.error) {
        console.log("\tEntities:");
        for (const entity of doc.entities) {
          console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
        }
      } else {
        console.error("  Error:", doc.error);
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Výstup

```console
== Analyze Sample ==
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Můžete také použít operaci analyzovat ke zjištění kódu PII a extrakce klíčových frází. Podívejte se na téma Analýza ukázek pro [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript) a [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/typescript/src) na GitHubu.

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Tato funkce není k dispozici ve verzi 3,0.

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tato funkce není k dispozici ve verzi 2,1.

---

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```
