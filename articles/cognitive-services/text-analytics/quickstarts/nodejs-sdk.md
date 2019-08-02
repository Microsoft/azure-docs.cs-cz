---
title: 'Rychlý start: Použití Node. js k volání rozhraní API pro analýzu textu'
titleSuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro analýzu textu.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697482"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Rychlý start: Použití Node. js k volání služby pro rozpoznávání Analýza textu
<a name="HOLTop"></a>

Pomocí tohoto rychlého startu můžete začít s analýzou jazyka s Analýza textu SDK pro Node. js. I když je REST API [Analýza textu](//go.microsoft.com/fwlink/?LinkID=759711) kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na GitHubu. [](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/)
* Sada SDK Analýza textu [pro Node. js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) : sadu SDK můžete nainstalovat pomocí nástroje:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Dále musíte mít [koncový bod a přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) vygenerovaný během registrace.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Vytvoření aplikace Node. js a instalace sady SDK

Po instalaci Node. js vytvořte projekt Node. Vytvořte pro svou aplikaci nový adresář a přejděte do jeho adresáře.

```mkdir myapp && cd myapp```

Spusťte ```npm init``` pro vytvoření aplikace uzlu se souborem Package. JSON. Nainstalujte balíčky `azure-cognitiveservices-textanalytics`anpm: `ms-rest-azure`

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Soubor Package. JSON vaší aplikace se bude aktualizovat o závislosti.

## <a name="authenticate-your-credentials"></a>Ověření přihlašovacích údajů

Vytvoří nový soubor `index.js` v kořenovém adresáři projektu a naimportuje nainstalované knihovny.

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Vytvořte proměnnou pro klíč předplatného Analýza textu.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Pro zabezpečené nasazení tajných kódů v produkčních systémech doporučujeme použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta Analýza textu

Vytvoří nový `TextAnalyticsClient` objekt s `credentials` parametrem. Použijte pro předplatné Analýza textu správnou oblast Azure.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte seznam objektů obsahující dokumenty, které chcete analyzovat. Datová část do rozhraní API se `documents`skládá ze seznamu, který `id`obsahuje atribut, `language`a `text` . Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. `text` 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Zavolejte `client.sentiment` a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

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

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte seznam objektů, které obsahují vaše dokumenty. Datová část do rozhraní API se skládá ze seznamu `documents`, který `id` obsahuje atribut a `text` . Atribut ukládá text, který má být analyzován, `id` a může být libovolná hodnota. `text`

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

Zavolejte `client.detectLanguage()` a získejte výsledek. Pak projdete výsledky a vytiskněte ID každého dokumentu a první vrácený jazyk.

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

Spusťte kód `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte seznam objektů, které obsahují vaše dokumenty. Datová část do rozhraní API se `documents`skládá ze seznamu, který `id`obsahuje atribut, `language`a `text` . Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. `text`

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Zavolejte `client.entities()` a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte název Wikipedii, typ a dílčí typy (pokud existuje) a také umístění v původním textu.

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

Spusťte kód `node index.js` v okně konzoly.

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

Vytvořte seznam objektů, které obsahují vaše dokumenty. Datová část do rozhraní API se `documents`skládá ze seznamu, který `id`obsahuje atribut, `language`a `text` . Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. `text`

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

Zavolejte `client.keyPhrases()` a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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

Spusťte kód `node index.js` v okně konzoly.

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

## <a name="see-also"></a>Viz také:

 [Přehled Analýza textu](../overview.md) Nejčastější dotazy – Nejčastější [dotazy](../text-analytics-resource-faq.md)
