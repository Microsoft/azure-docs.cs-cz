---
title: 'Rychlý Start: převod textu na řeč, Node.js služby řeči'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak převést převod textu na řeč pomocí Node.js a REST API převodu textu na řeč. Vzorový text obsažený v této příručce je strukturovaný jako SSML (Speech syntézy Language). To vám umožní zvolit hlas a jazyk odpovědi na řeč.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 38f8df1e721d2e29d4871a05263d9f4559aaedc4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406805"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Rychlý Start: převod textu na řeč pomocí Node.js

V tomto rychlém startu se dozvíte, jak převést převod textu na řeč pomocí Node.js a REST API převodu textu na řeč. Text žádosti v této příručce je strukturovaný jako [SSML (Speech syntézy Language)](speech-synthesis-markup.md), který umožňuje zvolit hlas a jazyk odpovědi.

Tento rychlý Start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* <a href="https://nodejs.org/en/" target="_blank">Uzel 8.12. x nebo novější<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Vytvořit projekt a vyžadovat závislosti

Pomocí oblíbených rozhraní IDE nebo editoru vytvořte nový projekt Node.js. Pak do svého projektu, do souboru s názvem `tts.js`, zkopírujte tento fragment kódu.

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

V další části vytvoříme funkci pro volání rozhraní API pro převod textu na mluvené slovo a uložení syntetizované odpovědi na řeč.

## <a name="make-a-request-and-save-the-response"></a>Vytvoření žádosti a uložení odpovědi

Tady se chystáte sestavit požadavek na rozhraní API pro převod textu na mluvené slovo a Uložit odpověď na řeč. Tato ukázka předpokládá, že používáte koncový bod Západní USA. Pokud je prostředek zaregistrován v jiné oblasti, nezapomeňte aktualizovat `uri` . Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je nutné přidat požadovaná záhlaví pro požadavek. Ujistěte se, že provedete aktualizaci `User-Agent` s názvem vašeho prostředku (umístěný v Azure Portal) a nastavíte `X-Microsoft-OutputFormat` na preferovaný zvukový výstup. Úplný seznam výstupních formátů najdete v tématu [zvukové výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Pak sestavte text žádosti pomocí jazyka Speech syntéz Markup Language (SSML). Tato ukázka definuje strukturu a používá `text` vstup, který jste vytvořili dříve.

>[!NOTE]
> Tato ukázka používá `JessaRUS` písmo hlasu. Úplný seznam hlasů/jazyků poskytovaných společností Microsoft najdete v tématu [Podpora jazyků](language-support.md).
> Pokud vás zajímá vytváření jedinečného a rozpoznatelného hlasu pro vaši značku, přečtěte si téma [vytváření vlastních hlasových písem](how-to-customize-voice-font.md).

Nakonec vytvoříte požadavek na službu. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď na řeč se zapíše jako `TTSOutput.wav` .

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

To je to, že jste připraveni spustit ukázkovou aplikaci pro převod textu na řeč. Z příkazového řádku (nebo relace Terminálové služby) přejděte do adresáře projektu a spusťte příkaz:

```console
node tts.js
```

Po zobrazení výzvy zadejte cokoli, co byste chtěli převést z převodu textu na řeč. V případě úspěchu se soubor řeči nachází ve složce projektu. Přehrajte ho pomocí oblíbeného Media Playeru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte ze zdrojového kódu ukázkové aplikace odebrat všechny důvěrné informace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte Node.js ukázky na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Viz také:

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam ukázek hlasu pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
