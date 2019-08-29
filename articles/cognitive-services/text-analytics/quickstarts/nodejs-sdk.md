---
title: 'Rychlý start: Analýza textu klientskou knihovnu pro Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro analýzu textu.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: 5b18ba4e06e8a44d7bd59b12a69a14ae15506234
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70134996"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Rychlý start: Klientská knihovna pro analýzu textu pro Node. js
<a name="HOLTop"></a>

Začínáme s klientskou knihovnou Analýza textu pro. Node. js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. 

K provedení této akce použijte klientskou knihovnu Analýza textu pro Node. js:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

[](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) balíčku | [zdrojového kódu](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | knihovny Referenční dokumentace[(npm)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro analýzu textu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md) nebo rozhraní příkazového [řádku Azure CLI](../../cognitive-services-apis-create-account-cli.md) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředku na [Azure Portal](https://portal.azure.com/)

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro tento klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `TEXTANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním příkazu vytvořte aplikaci uzlu `package.json` se souborem. `npm init` 

```console
npm init
```

Vytvořte soubor s názvem `index.js` a importujte následující knihovny:

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

Vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure. Získejte tyto hodnoty z proměnných prostředí TEXT_ANALYTICS_SUBSCRIPTION_KEY a TEXT_ANALYTICS_ENDPOINT. Pokud jste po zahájení úprav aplikace vytvořili tyto proměnné prostředí, budete muset zavřít a znovu otevřít Editor, integrované vývojové prostředí (IDE) nebo prostředí, které používáte pro přístup k proměnným.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte balíčky `@azure/cognitiveservices-textanalytics`anpm: `@azure/ms-rest-js`

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

`package.json` Soubor vaší aplikace bude aktualizován pomocí závislostí.

## <a name="object-model"></a>Objektový model

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku.

Text se pošle do rozhraní API jako seznam `documents` `id`objektů, což jsou `dictionary` objekty obsahující kombinaci atributů, `text`a `language` v závislosti na použité metodě. Atribut ukládá text, který má být analyzován v původním `language`umístění, a `id` může být libovolná hodnota. `text` 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza subjektivního hodnocení](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte nový objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) s `credentials` parametrem `endpoint` a jako parametr.

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte seznam objektů obsahující dokumenty, které chcete analyzovat.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
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
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte seznam objektů, které obsahují vaše dokumenty.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
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
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte seznam objektů, které obsahují vaše dokumenty.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
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
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte seznam objektů, které obsahují vaše dokumenty.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
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
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)
