---
title: 'Rychlý Start: seznam hlasů pro převod textu na řeč, Node.js služby řeči'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Node.js. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: d7ec5b386a9e62606a8b46c4e66cea85f8098a83
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406822"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Rychlý Start: získání seznamu hlasů pro převod textu na řeč pomocí Node.js

V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Node.js. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší. Seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

Tento rychlý Start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* <a href="https://nodejs.org/en/" target="_blank">Uzel 8.12. x nebo novější<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvořit projekt a vyžadovat závislosti

Pomocí oblíbených rozhraní IDE nebo editoru vytvořte nový projekt Node.js. Pak do svého projektu, do souboru s názvem `get-voices.js`, zkopírujte tento fragment kódu.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `npm install request request-promise`.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

REST API převodu textu na řeč vyžaduje přístupový token pro ověřování. Pro získání přístupového tokenu se vyžaduje Exchange. Tato funkce vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken` koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jinou oblast, aktualizujte hodnotu pro `uri` . Úplný seznam najdete v tématu [oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte do svého projektu tento kód:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Další informace o ověřování najdete v tématu [ověřování pomocí přístupového tokenu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

V další části vytvoříme funkci, která získá seznam hlasů a uloží výstup JSON do souboru.

## <a name="make-a-request-and-save-the-response"></a>Vytvoření žádosti a uložení odpovědi

Tady se chystáte vytvořit žádost a uložit seznam vrácených hlasů. Tato ukázka předpokládá, že používáte koncový bod Západní USA. Pokud je prostředek zaregistrován v jiné oblasti, nezapomeňte aktualizovat `uri` . Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadovaná záhlaví pro požadavek. Nakonec vytvoříte požadavek na službu. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď se zapíše do souboru.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je vytvoření asynchronní funkce. Tato funkce přečte klíč předplatného z proměnné prostředí, získá token, počká na dokončení žádosti a pak zapíše odpověď JSON do souboru.

Pokud nejste obeznámeni s proměnnými prostředí nebo upřednostňujete testování pomocí klíče předplatného pevně zakódované jako řetězce, nahraďte `process.env.SPEECH_SERVICE_KEY` klíč předplatného jako řetězec.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je to, že jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relace Terminálové služby) přejděte do adresáře projektu a spusťte příkaz:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte ze zdrojového kódu ukázkové aplikace odebrat všechny důvěrné informace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte Node.js ukázky na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Viz také:

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam ukázek hlasu pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
