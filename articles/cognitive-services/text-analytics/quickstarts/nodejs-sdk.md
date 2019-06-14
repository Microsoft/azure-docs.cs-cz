---
title: 'Rychlý start: Použití Node.js k volání rozhraní Text Analytics API'
titleSuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro analýzu textu.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081516"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Rychlý start: Použití Node.js k volání Text Analytics služby Cognitive Services
<a name="HOLTop"></a>

V tomto rychlém startu můžete začít analýzou jazyce s využitím sada Text Analytics SDK pro Node.js. Zatímco [rozhraní Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) rozhraní REST API je kompatibilní s Většina programovacích jazyků, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/)
* Rozhraní Text Analytics [SDK pro Node.js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) můžete nainstalovat sadu SDK s:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Dále musíte mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Vytvoření aplikace v Node.js a instalace sady SDK

Po instalaci Node.js, vytvořte projekt uzlu. Vytvořte nový adresář pro vaši aplikaci a přejděte do adresáře.

```mkdir myapp && cd myapp```

Spustit ```npm init``` k vytvoření aplikace v Ruby s soubor package.json. Nainstalujte `ms-rest-azure` a `azure-cognitiveservices-textanalytics` balíčky NPM:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Soubor package.json vaší aplikace se aktualizují se závislostmi.

## <a name="authenticate-your-credentials"></a>Ověření vašich pověření

Vytvořte nový soubor `index.js` v projektu kořenové a importovat nainstalované knihovny

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Vytvořte proměnnou pro váš klíč předplatného pro analýzu textu.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Pro zabezpečené nasazení tajných kódů v produkční systémy doporučujeme použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta pro analýzu textu

Vytvořte nový `TextAnalyticsClient` objekt s `credentials` jako parametr. Použijte správný oblast Azure pro vaše předplatné pro analýzu textu.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvoří seznam objektů, která bude obsahovat dokumenty, které chcete analyzovat. Datová část rozhraní API se skládá ze seznamu `documents`, které obsahují `id`, `language`, a `text` atribut. `text` Text, který má být analyzován, úložišť atributů `language` je jazyk dokumentu a `id` může být libovolná hodnota. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Volání `client.sentiment` a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu a skóre mínění. Skóre blíže 0 znamená negativní zabarvení, zatímco skóre blíže 1 označuje pozitivní mínění.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Spuštění kódu pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Detekce jazyka

Vytvoření seznamu objekty, které obsahují vaše dokumenty. Datová část rozhraní API se skládá ze seznamu `documents`, které obsahují `id` a `text` atribut. `text` Text, který má být analyzován, úložišť atributů a `id` může být libovolná hodnota.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

Volání `client.detectLanguage()` a získat výsledek. Pak iterování přes výsledky a tisknout ID každé dokumentu a první vrácené jazyk.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Spuštění kódu pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvoří seznam objektů, obsahující vaše dokumenty. Datová část rozhraní API se skládá ze seznamu `documents`, které obsahují `id`, `language`, a `text` atribut. `text` Text, který má být analyzován, úložišť atributů `language` je jazyk dokumentu a `id` může být libovolná hodnota.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Volání `client.entities()` a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu. Pro každou zjištěnou entitu, vytisknout názvu wikipedia, typ a podtypů (pokud existuje) a také umístění původního textu.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Spuštění kódu pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvoří seznam objektů, obsahující vaše dokumenty. Datová část rozhraní API se skládá ze seznamu `documents`, které obsahují `id`, `language`, a `text` atribut. `text` Text, který má být analyzován, úložišť atributů `language` je jazyk dokumentu a `id` může být libovolná hodnota.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

Volání `client.keyPhrases()` a získat výsledek. Pak iterování přes výsledky a tisknout ID každé dokumentu a všechny zjištěné klíčové fráze.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Spuštění kódu pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech

 [Přehled analýzy textu](../overview.md) [– nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)
