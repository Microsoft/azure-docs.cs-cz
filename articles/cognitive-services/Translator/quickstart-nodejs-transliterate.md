---
title: 'Rychlý start: Transkripce text, Node.js – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak pomocí Node.js a rozhraní REST API služby Translator Text provést transkripci (převod) textu z jednoho skriptu do druhého. V této ukázce se provádí transkripce z japonštiny do latinky.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 10/29/2018
ms.author: erhopf
ms.openlocfilehash: 1d957adb58104b0c635999f724d99f089c1d1c30
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55855862"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-with-nodejs"></a>Rychlý start: Použití rozhraní Translator Text API transkripce textu s využitím Node.js

V tomto rychlém startu se dozvíte, jak pomocí Node.js a rozhraní REST API služby Translator Text provést transkripci (převod) textu z jednoho skriptu do druhého. V uvedené ukázce se provádí transkripce z japonštiny do latinky.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Node.js 8.12.x nebo novější](https://nodejs.org/en/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt. Pak do svého projektu, do souboru s názvem `transliterate-text.js`, zkopírujte tento fragment kódu.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `npm install request uuidv4`.

Tyto moduly jsou potřeba k vytvoření požadavku HTTP a jedinečného identifikátoru pro hlavičku `'X-ClientTraceId'`.

## <a name="set-the-subscription-key"></a>Nastavení klíče předplatného

Tento kód se pokusí přečíst klíč předplatného služby Translator Text z proměnné prostředí `TRANSLATOR_TEXT_KEY`. Pokud proměnné prostředí neznáte, můžete hodnotu `subscriptionKey` nastavit jako řetězec a okomentovat podmíněný příkaz.

Zkopírujte do svého projektu tento kód:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Konfigurace požadavku

Metoda `request()` zpřístupněná prostřednictvím modulu požadavků nám umožňuje předat metodu HTTP, adresu URL, parametry požadavku, hlavičky a text JSON jako objekt `options`. V tomto fragmentu kódu nakonfigurujeme požadavek:

>[!NOTE]
> Další informace o koncových bodech, cesty a parametry požadavku najdete v tématu [Translator Text API 3.0: Transkripce](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'transliterate',
    qs: {
      'api-version': '3.0',
      'language': 'ja',
      'fromScript': 'jpan',
      'toScript': 'latn'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'こんにちは'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

Nejjednodušším způsobem, jak ověřit požadavek, je předat klíč předplatného jako hlavičku `Ocp-Apim-Subscription-Key`, což děláme i v této ukázce. Alternativně můžete klíč předplatného vyměnit za přístupový token a k ověření požadavku předat přístupový token jako hlavičku `Authorization`. Další informace najdete v tématu [Ověřování](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Vytvoření požadavku a tisk odpovědi

Dále pomocí metody `request()` vytvoříme požadavek. Tato metoda jako první argument přebírá objekt `options`, který jsme vytvořili v předchozí části, a pak vytiskne očištěnou odpověď JSON.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> V této ukázce definujeme požadavek HTTP v objektu `options`. Modul požadavků však podporuje také pomocné metody jako `.post` a `.get`. Další informace najdete v článku věnovaném [pomocným metodám](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

To je vše, sestavili jste jednoduchý program, který zavolá službu Translator Text API a vrátí odpověď JSON. Teď je čas program spustit:

```console
node transliterate-text.js
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Ukázková odpověď

```json
[
    {
        "script": "latn",
        "text": "konnnichiha"
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste do svého programu pevně zakódovali klíč předplatného, nezapomeňte po dokončení tohoto rychlého startu tento klíč předplatného odebrat.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte příklady Node.js na GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Další informace najdete v tématech

Kromě rozpoznávání jazyka můžete pomocí služby Translator Text API provádět také následující úlohy:

* [Překlad textu](quickstart-nodejs-translate.md)
* [Identifikace jazyka podle vstupu](quickstart-nodejs-detect.md)
* [Získání alternativních překladů](quickstart-nodejs-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-nodejs-languages.md)
* [Určení délky věty ze vstupu](quickstart-nodejs-sentences.md)
