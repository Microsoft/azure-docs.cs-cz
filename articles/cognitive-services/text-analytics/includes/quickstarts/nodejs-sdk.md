---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 5c5c2ed6b7806095dada40cc921a773d28421424
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750229"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) |  | [ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) [(npm)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org/).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `npm init` pro vytvoření aplikace uzlu se souborem `package.json`. 

```console
npm init
```

Vytvořte soubor s názvem `index.js` a přidejte následující knihovny:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure. Získejte tyto hodnoty z proměnných prostředí `TEXT_ANALYTICS_SUBSCRIPTION_KEY` a `TEXT_ANALYTICS_ENDPOINT`. Pokud jste po zahájení úprav aplikace vytvořili tyto proměnné prostředí, budete muset zavřít a znovu otevřít Editor, integrované vývojové prostředí (IDE) nebo prostředí, které používáte pro přístup k proměnným.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-javascript[Key and endpoint vars](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyVars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `@azure/ms-rest-js` a balíčky `@azure/cognitiveservices-textanalytics` NPM:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Soubor `package.json` vaší aplikace bude aktualizován pomocí závislostí.

## <a name="object-model"></a>Objektový model

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku.

Text se pošle do rozhraní API jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Ověření klienta

Vytvoří nový objekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) s `credentials` a `endpoint` jako parametr.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte seznam objektů slovníku obsahující dokumenty, které chcete analyzovat. Zavolejte metodu [mínění ()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#sentiment-object-) klienta a získejte vrácenou [SentimentBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/sentimentbatchresult?view=azure-node-latest). Procházejte seznamem výsledků a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte seznam objektů slovníku obsahující vaše dokumenty. Zavolejte metodu [operaci DetectLanguage ()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#detectlanguage-object-) klienta a získejte vrácenou [LanguageBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/languagebatchresult?view=azure-node-latest). Potom Iterujte výsledky a vytiskněte ID a jazyk každého dokumentu.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Spusťte kód pomocí `node index.js` v okně konzoly.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte seznam objektů, které obsahují vaše dokumenty. Zavolejte metodu entity klienta [()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#entities-object-) a získejte objekt [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/entitiesbatchresult?view=azure-node-latest) . Iterujte seznamem výsledků a vytiskněte ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte název Wikipedii, typ a dílčí typy (pokud existuje) a také umístění v původním textu.

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

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte seznam objektů, které obsahují vaše dokumenty. Zavolejte metodu klíčová [fráze klienta ()](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest#keyphrases-object-) a získejte vrácený objekt [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/keyphrasebatchresult?view=azure-node-latest) . Iterujte výsledky a vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node` v souboru rychlého startu.

```console
node index.js
```
