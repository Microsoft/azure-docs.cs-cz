---
title: 'Rychlý start: Převést převod textu na řeč, Node.js – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí Node.js a rozhraní REST API pro převod textu na řeč. Ukázkový text v tomto průvodci strukturovaná jako jazyk pro značky syntézu řeči (SSML). To umožňuje zvolit hlasu a jazyk odpovědi řeči.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 808466e9d2546472a4f86dd0d7eacd48e75cb94a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224475"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Rychlý start: Převést převod textu na řeč pomocí Node.js

V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí Node.js a převod textu na řeč rozhraní REST API. Text požadavku v této příručce má strukturu [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md), která umožňuje výběr hlasu a jazyk odpovědi.

Tento rychlý start vyžaduje [účtu služeb Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem Speech Service. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Node.js 8.12.x nebo novější](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč předplatného pro službu rozpoznávání řeči. [Získejte ji zdarma. ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvoření projektu a vyžadují závislosti

Vytvořte nový projekt Node.js pomocí Oblíbené prostředí IDE nebo editoru. Pak do svého projektu, do souboru s názvem `tts.js`, zkopírujte tento fragment kódu.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `npm install request readline-sync`.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavte klíč předplatného a vytvoření výzvu pro převod textu na ŘEČ

V následujících částech vytvoříte funkcí k autorizaci, volání rozhraní API pro převod textu na řeč a ověřit odpověď. Začněme přidáním klíč předplatného a vytvoření výzva k zadání textu.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Převod textu na řeč rozhraní REST API vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vymění váš klíč předplatného Speech Service token přístupu k použití `issueToken` koncového bodu.

Tato funkce přebírá dva argumenty, váš klíč předplatného hlasové služby a funkce zpětného volání. Po získání přístupového tokenu funkce předává hodnota funkce zpětného volání. V další části vytvoříme funkce pro volání rozhraní API pro převod textu na řeč a uložení odpovědi řečového.

Tento příklad předpokládá, že je vaše předplatné Speech Service v oblasti západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `uri`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte do svého projektu tento kód:

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> Další informace o ověřování najdete v tématu [ověřování pomocí přístupového tokenu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady se chystáte vytvořit požadavek na rozhraní API pro převod textu na řeč a uložení odpovědi řeči. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `uri`. Další informace najdete v tématu [Speech Service oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je třeba přidat povinné hlavičky pro dané žádosti. Ujistěte se, že aktualizujete `User-Agent` názvem prostředku (nachází se na webu Azure Portal) a nastavte `X-Microsoft-OutputFormat` na preferovaný zvuku. Úplný seznam formátech výstupu najdete v tématu [zvuk výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Potom vytvořte datovou část požadavku pomocí řeči syntézu Markup Language (SSML). Tato ukázka definuje strukturu a používá `text` vstup jste vytvořili dříve.

>[!NOTE]
> Tento příklad používá `JessaRUS` hlasového písma. Pro úplný seznam Microsoft poskytuje hlasy/jazyky, naleznete v tématu [jazykovou podporu](language-support.md).
> Pokud vás zajímá vytváření jedinečné, rozpoznat hlas pro hodnotu značky, přečtěte si téma [vytváření vlastního hlasového písma](how-to-customize-voice-font.md).

A konečně provede požadavek do služby. Pokud je žádost úspěšná a se vrátí stavový kód 200 kódu, odpověď řeči je zapsán jako `sample.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
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
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je volání `textToSpeech` funkce.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit převod textu na řeč ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
node tts.js
```

Po zobrazení výzvy zadejte cokoli, co byste chtěli převést z převodu textu na řeč. V případě úspěšného ověření řeči soubor je umístěn ve složce vašeho projektu. Přehrávání pomocí vašeho oblíbeného přehrávač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte Node.js ukázky na Githubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
