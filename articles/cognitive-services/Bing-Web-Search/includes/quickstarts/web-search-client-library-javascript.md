---
title: Rychlý Start knihovny klienta Vyhledávání na webu Bingu JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/24/2020
ms.author: aahi
ms.openlocfilehash: 3f8d0e8996aa972aa63f31d6d5cf8b3fb3c00bd8
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652153"
---
Klientská knihovna Vyhledávání na webu Bingu umožňuje snadnou integraci Vyhledávání na webu Bingu do aplikace Node. js. V tomto rychlém startu se dozvíte, jak vytvořit instanci klienta, odeslat požadavek a vytisknout odpověď.

Chcete se rovnou podívat na kód? Ukázky pro [klientské knihovny vyhledávání Bingu pro JavaScript](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) jsou k dispozici na GitHubu.

## <a name="prerequisites"></a>Předpoklady
Tady je pár věcí, které budete na začátku tohoto rychlého startu potřebovat:

* [Node.js 6](https://nodejs.org/en/download/) nebo novější
* Klíč předplatného  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Nejprve si pro svůj projekt Node.js nastavíme vývojové prostředí.

1. Vytvořte pro svůj projekt nový adresář:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Vytvořte nový soubor balíčku:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Teď nainstalujeme některé moduly Azure a přidáme je do souboru `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Vytvoření projektu a deklarace požadovaných modulů

V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte ve stejném adresáři, jako je soubor `package.json`, nový projekt Node.js. Například: `sample.js`.

Teď zkopírujte tento kód do svého projektu. Načte moduly nainstalované v předchozí části.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Vytvoření instance klienta

Tento kód vytvoří instanci klienta s použitím modulu `azure-cognitiveservices-websearch`. Než budete pokračovat, ujistěte se, že jste zadali platný klíč předplatného k účtu Azure.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Vytvoření požadavku a tisk výsledků

Z klienta pošlete vyhledávací dotaz Bingu pro vyhledávání na webu. Pokud odpověď obsahuje výsledky některých položek v poli `properties`, vytiskne konzola `result.value`.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Spuštění programu

Posledním krokem je spuštění programu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až projekt dokončíte, nezapomeňte z kódu programu odebrat klíč předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ukázky kognitivních služeb sady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Viz také

* [Referenční informace k sadě Azure Node SDK](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
