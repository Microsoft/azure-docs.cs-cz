---
title: 'Rychlý Start: převod textu na řeč, Node. js – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak převést převod textu na řeč pomocí Node. js a REST API převodu textu na řeč. Ukázkový text v tomto průvodci strukturovaná jako jazyk pro značky syntézu řeči (SSML). To umožňuje zvolit hlasu a jazyk odpovědi řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976498"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Rychlý Start: převod textu na řeč pomocí Node. js

V tomto rychlém startu se dozvíte, jak převést převod textu na řeč pomocí Node. js a REST API převodu textu na řeč. Text požadavku v této příručce má strukturu [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md), která umožňuje výběr hlasu a jazyk odpovědi.

Tento rychlý Start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* [Node.js 8.12.x nebo novější](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvořit projekt a vyžadovat závislosti

Vytvořte nový projekt Node. js pomocí oblíbeného integrovaného vývojového prostředí (IDE) nebo editoru. Pak do svého projektu, do souboru s názvem `tts.js`, zkopírujte tento fragment kódu.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `npm install request request-promise xmlbuilder readline-sync`.

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

V další části vytvoříme funkci pro volání rozhraní API pro převod textu na mluvené slovo a uložení syntetizované odpovědi na řeč.

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady se chystáte sestavit požadavek na rozhraní API pro převod textu na mluvené slovo a Uložit odpověď na řeč. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `uri`. Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je třeba přidat povinné hlavičky pro dané žádosti. Ujistěte se, že aktualizujete `User-Agent` názvem prostředku (nachází se na webu Azure Portal) a nastavte `X-Microsoft-OutputFormat` na preferovaný zvuku. Úplný seznam formátech výstupu najdete v tématu [zvuk výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Potom vytvořte datovou část požadavku pomocí řeči syntézu Markup Language (SSML). Tato ukázka definuje strukturu a používá `text` vstup jste vytvořili dříve.

>[!NOTE]
> Tento příklad používá `JessaRUS` hlasového písma. Pro úplný seznam Microsoft poskytuje hlasy/jazyky, naleznete v tématu [jazykovou podporu](language-support.md).
> Pokud vás zajímá vytváření jedinečné, rozpoznat hlas pro hodnotu značky, přečtěte si téma [vytváření vlastního hlasového písma](how-to-customize-voice-font.md).

A konečně provede požadavek do služby. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď na řeč se zapíše jako `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je vytvoření asynchronní funkce. Tato funkce načte klíč předplatného z proměnné prostředí, zobrazí výzvu k zadání textu, získá token, počká na dokončení žádosti a pak převede text na řeč a uloží zvuk jako. wav.

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit převod textu na řeč ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
node tts.js
```

Po zobrazení výzvy zadejte cokoli, co byste chtěli převést z převodu textu na řeč. V případě úspěšného ověření řeči soubor je umístěn ve složce vašeho projektu. Přehrávání pomocí vašeho oblíbeného přehrávač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky pro Node. js na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
