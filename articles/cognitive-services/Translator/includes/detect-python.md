---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 6591fd6eb232bf5fb242c9e08830324f864dac2f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837498"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt Pythonu pomocí oblíbeného integrovaného vývojového prostředí (IDE) nebo editoru. Potom tento fragment kódu zkopírujte do projektu v souboru s názvem `detect.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Pokud jste tyto moduly nepoužívali, budete je muset nainstalovat před spuštěním programu. Chcete-li nainstalovat tyto balíčky, spusťte příkaz: `pip install requests uuid`.

První komentář oznamuje Překladači Pythonu, aby používal kódování UTF-8. Požadované moduly se pak naimportují pro čtení klíče předplatného z proměnné prostředí, sestavení požadavku HTTP, vytvoření jedinečného identifikátoru a zpracování odpovědi JSON vrácené Translator Text API.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Nastavení klíče předplatného, koncového bodu a cesty

Tato ukázka se pokusí přečíst klíč předplatného Translator Text a koncový bod z proměnných prostředí: `TRANSLATOR_TEXT_KEY` a `TRANSLATOR_TEXT_ENDPOINT`. Pokud nejste obeznámeni s proměnnými prostředí, můžete nastavit `subscription_key` a `endpoint` jako řetězce a přidat komentář k podmíněným příkazům.

Zkopírujte tento kód do projektu:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Translator Text globální koncový bod je nastaven jako `endpoint`. `path` nastaví trasu `detect` a určí, že chceme mít k dispozice verzi 3 rozhraní API.

>[!NOTE]
> Další informace o koncových bodech, trasách a parametrech požadavků naleznete v tématu [Translator Text API 3,0: detekce](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```python
path = '/detect?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Přidat záhlaví

Nejjednodušší způsob, jak požadavek ověřit, je předat klíč předplatného jako hlavičku `Ocp-Apim-Subscription-Key`, což je to, co v této ukázce používáme. Alternativně můžete vyměňovat klíč předplatného pro přístupový token a přístupový token předat společně jako hlavičku `Authorization` a ověřit vaši žádost. Další informace najdete v tématu [ověřování](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Zkopírujte tento fragment kódu do projektu:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Pokud používáte Cognitive Services předplatné s více službami, musíte do parametrů žádosti zahrnout taky `Ocp-Apim-Subscription-Region`. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-detect-text-language"></a>Vytvoření požadavku na detekci jazyka textu

Definujte řetězec (nebo řetězce), pro který chcete detekovat jazyk:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
}]
```

V dalším kroku vytvoříme žádost POST pomocí modulu `requests`. Přebírá tři argumenty: zřetězenou adresu URL, hlavičku požadavku a text žádosti:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Tisk odpovědi

Posledním krokem je vytisknutí výsledků. Tento fragment kódu prettifies výsledky seřazením klíčů, nastavením odsazení a deklarací položek a oddělovačů klíčů.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Umístit vše dohromady

To je to, že jste připravili jednoduchý program, který bude volat Translator Text API a vrátil odpověď JSON. Nyní je čas spustit program:

```console
python detect.py
```

Pokud byste chtěli porovnat kód s pozdravem, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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

Pokud jste svůj klíč předplatného pevně zakódované do svého programu, nezapomeňte po dokončení tohoto rychlého startu odebrat klíč předplatného.

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
