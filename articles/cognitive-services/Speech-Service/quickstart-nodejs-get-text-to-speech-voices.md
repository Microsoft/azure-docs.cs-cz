---
title: 'Rychlý start: Seznam převod textu na řeč hlasů, Node.js – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete zjistěte, jak získat celý seznam standardní a neuronových sítí hlasy pro oblast nebo koncový bod pomocí Node.js. V seznamu se vrátí jako JSON a hlasové dostupnosti se liší podle oblasti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887092"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Rychlý start: Získání seznamu převod textu na řeč hlasy pomocí Node.js

V tomto rychlém startu budete zjistěte, jak získat celý seznam standardní a neuronových sítí hlasy pro oblast nebo koncový bod pomocí Node.js. V seznamu se vrátí jako JSON a hlasové dostupnosti se liší podle oblasti. Seznam podporovaných oblastí najdete v tématu [oblastech](regions.md).

Tento rychlý start vyžaduje [účtu služeb Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem hlasové služby. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Uzel 8.12.x nebo novější](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč předplatného pro hlasové služby. [Získejte ji zdarma. ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvoření projektu a vyžadují závislosti

Vytvořte nový projekt Node.js pomocí Oblíbené prostředí IDE nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.js`, zkopírujte tento fragment kódu.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `npm install request request-promise`.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Převod textu na řeč rozhraní REST API vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Vymění váš klíč předplatného hlasových služeb pro aplikace access token pomocí této funkce `issueToken` koncového bodu.

Tento příklad předpokládá, že vaše předplatné hlasové služby je v oblasti západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `uri`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

V další části vytvoříme funkci k získání seznamu hlasy a uložte výstup JSON do souboru.

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady budete vytvářet žádosti a uložit seznam vrácených hlasy. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `uri`. Další informace najdete v tématu [hlasových služeb regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

V dalším kroku přidejte požadované hlavičky pro dané žádosti. A konečně provede požadavek do služby. Pokud je žádost úspěšná a vrátí stavový kód 200 kód, odpověď zapsána do souboru.

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

Už jste téměř hotovi. Posledním krokem je vytvoření asynchronní funkce. Tato funkce bude číst váš klíč předplatného z proměnné prostředí, získat token, počkejte na dokončení žádosti a potom napsat odpověď JSON do souboru.

Pokud jste obeznámeni s proměnnými prostředí nebo dáváte přednost testu se vaše předplatné klíče pevně zakódované jako řetězec, nahraďte `process.env.SPEECH_SERVICE_KEY` s klíči předplatného jako řetězec.

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

Je to, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte Node.js ukázky na Githubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
