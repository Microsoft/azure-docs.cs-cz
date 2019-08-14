---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a3f26773fe3366e5592dd5baabb6b8fa5e7544a9
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968549"
---
## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Node.js 8.12.x nebo novější](https://nodejs.org/en/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt. Pak do svého projektu, do souboru s názvem `sentence-length.js`, zkopírujte tento fragment kódu.

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
> Další informace o koncových bodech, trasách a parametrech požadavků [najdete v článku Translator text API 3,0: Přerušit](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence)větu

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'breaksentence',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'How are you? I am fine. What did you do today?'
    }],
    json: true,
};
```

Nejjednodušším způsobem, jak ověřit požadavek, je předat klíč předplatného jako hlavičku `Ocp-Apim-Subscription-Key`, což děláme i v této ukázce. Alternativně můžete klíč předplatného vyměnit za přístupový token a k ověření požadavku předat přístupový token jako hlavičku `Authorization`.

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do hlaviček vaší žádosti.

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

To je vše, sestavili jste jednoduchý program, který zavolá službu Translator Text API a vrátí odpověď JSON. Teď je čas program spustit:

```console
node sentence-length.js
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Ukázková odpověď

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste do svého programu pevně zakódovali klíč předplatného, nezapomeňte po dokončení tohoto rychlého startu tento klíč předplatného odebrat.

## <a name="next-steps"></a>Další postup

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
