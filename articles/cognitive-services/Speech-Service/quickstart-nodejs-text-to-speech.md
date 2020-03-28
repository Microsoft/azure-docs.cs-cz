---
title: 'Úvodní příručka: Převod převodu textu na řeč, Node.js - Služba rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak převést převod textu na řeč pomocí Node.js a rozhraní REST API pro převod textu na řeč. Ukázkový text obsažený v této příručce je strukturován jako jazyk s označením syntézy řeči (SSML). To vám umožní zvolit hlas a jazyk odpovědi na řeč.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976498"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Úvodní příručka: Převod převodu textu na řeč pomocí souboru Node.js

V tomto rychlém startu se dozvíte, jak převést převod textu na řeč pomocí Node.js a rozhraní REST API pro převod textu na řeč. Tělo požadavku v této příručce je strukturováno jako [jazyk s poznámkami pro syntézu řeči (SSML),](speech-synthesis-markup.md)který umožňuje zvolit hlas a jazyk odpovědi.

Tento rychlý start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Node.js 8.12.x nebo novější](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu Řeč. [Získejte jeden zdarma!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvoření projektu a vyžadování závislostí

Vytvořte nový projekt Node.js pomocí oblíbeného rozhraní IDE nebo editoru. Pak do svého projektu, do souboru s názvem `tts.js`, zkopírujte tento fragment kódu.

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

V další části vytvoříme funkci pro volání rozhraní API pro převod textu na řeč a uložení syntetizované odpovědi na řeč.

## <a name="make-a-request-and-save-the-response"></a>Žádost a uložení odpovědi

Zde se chystáte vytvořit požadavek na rozhraní API pro převod textu na řeč a uložit odpověď na řeč. Tato ukázka předpokládá, že používáte koncový bod V USA . Pokud je prostředek zaregistrován v jiné oblasti, `uri`nezapomeňte aktualizovat . Další informace naleznete v tématu [Oblasti služby Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je třeba přidat požadované hlavičky pro požadavek. Ujistěte se, `User-Agent` že aktualizujete s názvem vašeho prostředku (umístěného na webu Azure Portal) a nastavte `X-Microsoft-OutputFormat` na upřednostňovaný zvukový výstup. Úplný seznam výstupních formátů naleznete [v tématu Zvukové výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Potom vytvořte tělo požadavku pomocí jazyka značek syntézy řeči (SSML). Tato ukázka definuje strukturu a `text` používá vstup, který jste vytvořili dříve.

>[!NOTE]
> Tato ukázka `JessaRUS` používá hlasové písmo. Úplný seznam hlasů/jazyků poskytovaných společností Microsoft naleznete v [tématu Language support](language-support.md).
> Pokud máte zájem o vytvoření jedinečného, rozpoznatelného hlasu pro vaši značku, přečtěte si viz [Vytvoření vlastních hlasových písem](how-to-customize-voice-font.md).

Nakonec požádáte o službu. Pokud je požadavek úspěšný a je vrácen stavový kód 200, odpověď na řeč je zapsána jako `TTSOutput.wav`.

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

Už jste téměř hotovi. Posledním krokem je vytvoření asynchronní funkce. Tato funkce bude číst klíč předplatného z proměnné prostředí, výzva k zadání textu, získat token, čekat na dokončení požadavku, pak převést převod převodu převodu převodu textu na řeč a uložení zvuku jako .wav.

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

To je ono, jste připraveni spustit ukázkovou aplikaci pro převod textu na řeč. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
node tts.js
```

Po zobrazení výzvy zadejte vše, co chcete převést z převodu z převodu na řeč. V případě úspěchu je soubor řeči umístěn ve složce projektu. Přehrajte si ji pomocí svého oblíbeného přehrávače médií.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte odebrat všechny důvěrné informace ze zdrojového kódu ukázkové aplikace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky souboru Node.js na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam hlasových vzorků pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
