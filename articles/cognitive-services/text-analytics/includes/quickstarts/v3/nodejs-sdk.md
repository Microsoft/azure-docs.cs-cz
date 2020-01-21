---
title: 'Rychlý Start: Klientská knihovna Analýza textu v3 pro Node. js | Microsoft Docs'
description: Začínáme s klientskou knihovnou V3 Analýza textu pro Node. js
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281136"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://aka.ms/azsdk-js-textanalytics-ref-docs) |  | [ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) [(npm)](https://www.npmjs.com/package/@azure/ai-text-analytics)

> [!NOTE]
> * V tomto rychlém startu se používá `3.0-preview` verze Analýza textu klientské knihovny, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Kód v tomto článku používá nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. V produkčních scénářích doporučujeme odesílat řetězce v dávkách pro zajištění výkonu a škálovatelnosti. Například volání `SentimentBatchAsync()` namísto `Sentiment()`.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org/).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `npm init` pro vytvoření aplikace uzlu se souborem `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalace klientské knihovny

Instalace balíčků `@azure/cognitiveservices-textanalytics` NPM:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Soubor `package.json` vaší aplikace bude aktualizován pomocí závislostí.

Vytvořte soubor s názvem `index.js` a přidejte následující knihovny:

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se pomocí vašeho klíče ověřuje v Azure. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku.

Text se pošle do rozhraní API jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#client-authentication)
* [Analýza mínění](#sentiment-analysis) (Public Preview)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-public-preview) (Public Preview)
* [Rozpoznávání pojmenovaných entit – osobní informace](#named-entity-recognition---personal-information-public-preview) (Public Preview)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="client-authentication"></a>Ověření klienta

Vytvořte nový objekt `TextAnalyticsClient` s vaším klíčem a koncovým bodem jako parametry.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>Analýza mínění (Public Preview)

> [!NOTE]
> Níže uvedený kód je určen pro mínění Analysis v3, který je ve verzi Public Preview.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `analyzeSentiment()` klienta a získejte vrácený objekt `SentimentBatchResult`. Procházejte seznamem výsledků a vytiskněte ID každého dokumentu, mínění na úrovni dokumentu s výsledky spolehlivosti. Výsledek každého dokumentu obsahuje mínění na úrovni věty spolu s posuny, délkou a hodnocením spolehlivosti.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
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
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
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

## <a name="language-detection"></a>Detekce jazyka

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `detectLanguages()` klienta a získejte vrácenou `DetectLanguageResult`. Pak Projděte výsledky a vytiskněte ID každého dokumentu s příslušným primárním a zjištěným jazykem.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

> [!NOTE]
> Níže je uvedený kód pro rozpoznávání pojmenovaných entit v3, který je ve verzi Public Preview.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
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
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Rozpoznávání pojmenovaných entit – osobní informace (Public Preview)

> [!NOTE]
> Níže uvedený kód slouží ke zjišťování osobních údajů pomocí rozpoznávání pojmenovaných entit v3, který je ve verzi Public Preview.

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizePiiEntities()` klienta a získejte objekt `EntitiesBatchResult`. Iterujte seznamem výsledků a vytiskněte název entity, typ, podtyp, posun, délku a skóre.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
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
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Propojování entit

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `recognizeLinkedEntities()` klienta a získejte objekt `RecognizeLinkedEntitiesResult`. Iterujte seznamem výsledků a vytiskněte název entity, ID, zdroj dat, adresu URL a shodu. Každý objekt v poli `matches` bude pro tuto shodu obsahovat posun, délku a skóre.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
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
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte pole řetězců obsahující dokument, který chcete analyzovat. Zavolejte metodu `extractKeyPhrases()` klienta a získejte vrácený objekt `ExtractKeyPhrasesResult`. Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
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

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node` v souboru rychlého startu.

```console
node index.js
```
