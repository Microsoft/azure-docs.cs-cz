---
title: 'Úvodní příručka: Seznam hlasů pro převod textu na řeč, Node.js - Služba řeči'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neurálníhlasité hlasy pro oblast/koncový bod pomocí Node.js. Seznam je vrácen jako JSON a dostupnost hlasu se liší podle oblasti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 9fe8bc06aafd17518d37c35034fac9b566e079ce
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261546"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Úvodní příručka: Získání seznamu hlasů pro převod textu na řeč pomocí souboru Node.js

V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neurálníhlasité hlasy pro oblast/koncový bod pomocí Node.js. Seznam je vrácen jako JSON a dostupnost hlasu se liší podle oblasti. Seznam podporovaných oblastí najdete v [tématu Oblasti](regions.md).

Tento rychlý start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* <a href="https://nodejs.org/en/" target="_blank">Uzel 8.12.x nebo novější<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu Řeč. [Získejte jeden zdarma!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvoření projektu a vyžadování závislostí

Vytvořte nový projekt Node.js pomocí oblíbeného rozhraní IDE nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.js`, zkopírujte tento fragment kódu.

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

Rozhraní REST API pro převod textu na řeč vyžaduje přístupový token pro ověřování. Chcete-li získat přístupový token, je vyžadována výměna. Tato funkce vyměňuje klíč předplatného služby `issueToken` Speech pro přístupový token pomocí koncového bodu.

Tato ukázka předpokládá, že vaše předplatné služby Řeči je v oblasti USA – západ. Pokud používáte jinou oblast, aktualizujte `uri`hodnotu pro aplikaci . Úplný seznam naleznete v tématu [Oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> Další informace o ověřování naleznete v tématu [Authenticate with an access token](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

V další části vytvoříme funkci, která získá seznam hlasů a uloží výstup JSON do souboru.

## <a name="make-a-request-and-save-the-response"></a>Žádost a uložení odpovědi

Zde budete stavět žádost a uložit seznam vrácených hlasů. Tato ukázka předpokládá, že používáte koncový bod V USA . Pokud je prostředek zaregistrován v jiné oblasti, `uri`nezapomeňte aktualizovat . Další informace naleznete v tématu [Oblasti služby Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadované hlavičky pro požadavek. Nakonec požádáte o službu. Pokud je požadavek úspěšný a je vrácen stavový kód 200, odpověď je zapsána do souboru.

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

Už jste téměř hotovi. Posledním krokem je vytvoření asynchronní funkce. Tato funkce bude číst klíč předplatného z proměnné prostředí, získat token, počkejte na dokončení požadavku a pak napište odpověď JSON do souboru.

Pokud nejste obeznámeni s proměnnými prostředí nebo dáváte přednost testování pomocí `process.env.SPEECH_SERVICE_KEY` klíče předplatného pevně zakódovaného jako řetězec, nahraďte klíčem předplatného jako řetězec.

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

To je ono, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte odebrat všechny důvěrné informace ze zdrojového kódu ukázkové aplikace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky souboru Node.js na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam hlasových vzorků pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
