---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: dfca8bb6f897f532081df7d141112ddbb190d109
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406536"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt. Pak do svého projektu, do souboru s názvem `detect.js`, zkopírujte tento fragment kódu.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `npm install request uuidv4`.

Tyto moduly jsou potřeba k vytvoření požadavku HTTP a jedinečného identifikátoru pro hlavičku `'X-ClientTraceId'`.

## <a name="set-the-subscription-key-and-endpoint"></a>Nastavení klíče předplatného a koncového bodu

Tato ukázka se pokusí přečíst klíč předplatného vašeho převaděče a koncový bod z těchto proměnných prostředí: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` a `TRANSLATOR_TEXT_ENDPOINT` . Pokud nejste obeznámeni s proměnnými prostředí, můžete nastavit `subscriptionKey` a `endpoint` jako řetězce a komentovat podmíněné příkazy.

Zkopírujte do svého projektu tento kód:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>Konfigurace požadavku

Metoda `request()` zpřístupněná prostřednictvím modulu požadavků nám umožňuje předat metodu HTTP, adresu URL, parametry požadavku, hlavičky a text JSON jako objekt `options`. V tomto fragmentu kódu nakonfigurujeme požadavek:

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```
Nejjednodušším způsobem, jak ověřit požadavek, je předat klíč předplatného jako hlavičku `Ocp-Apim-Subscription-Key`, což děláme i v této ukázce. Alternativně můžete klíč předplatného vyměnit za přístupový token a k ověření požadavku předat přístupový token jako hlavičku `Authorization`.

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout do `Ocp-Apim-Subscription-Region` hlaviček vaší žádosti.

Další informace najdete v tématu [Ověřování](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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

To je to, že jste připravili jednoduchý program, který bude volat překladatele a vrátil odpověď JSON. Teď je čas program spustit:

```console
node detect.js
```

## <a name="sample-response"></a>Ukázková odpověď

Po spuštění ukázky by se měla zobrazit následující tištěná do terminálu:

> [!NOTE]
> V tomto [seznamu jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)Najděte zkratku země/oblasti.

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste do svého programu pevně zakódovali klíč předplatného, nezapomeňte po dokončení tohoto rychlého startu tento klíč předplatného odebrat.

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s překladatelem dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
