---
title: 'Rychlý Start: seznam hlasů pro převod textu na řeč, Node. js – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Node. js. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976566"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Rychlý Start: získání seznamu hlasů pro převod textu na mluvené slovo pomocí Node. js

V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Node. js. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší. Seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

Tento rychlý Start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* [Node.js 8.12.x nebo novější](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvořit projekt a vyžadovat závislosti

Vytvořte nový projekt Node. js pomocí oblíbeného integrovaného vývojového prostředí (IDE) nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.js`, zkopírujte tento fragment kódu.

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

Převod textu na řeč rozhraní REST API vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato funkce vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken`ho koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `uri`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady se chystáte vytvořit žádost a uložit seznam vrácených hlasů. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `uri`. Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadovaná záhlaví pro požadavek. A konečně provede požadavek do služby. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď se zapíše do souboru.

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

Pokud nejste obeznámeni s proměnnými prostředí nebo upřednostňujete testování pomocí klíče předplatného pevně zakódované jako řetězce, nahraďte `process.env.SPEECH_SERVICE_KEY` klíčovým předplatným jako řetězec.

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

To je to, že jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky pro Node. js na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
