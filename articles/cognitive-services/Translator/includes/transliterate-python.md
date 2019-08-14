---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c047d03f27325ffa133c62ef3fe945f330139656
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968584"
---
## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x
* Klíč předplatného Azure pro službu Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt pomocí oblíbeného integrovaného vývojového prostředí (IDE) nebo nové složky se souborem `transliterate-text.py` s názvem na vašem počítači. Potom tento fragment kódu zkopírujte do svého projektu nebo souboru:

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests uuid`.

První komentář říká interpretu Pythonu, že má použít kódování UTF-8. Pak se importují požadované moduly pro čtení klíče předplatného z proměnné prostředí, vytvoření požadavku HTTP, vytvoření jedinečného identifikátoru a zpracování odpovědi JSON vrácené službou Translator Text API.

## <a name="set-the-subscription-key-base-url-and-path"></a>Nastavení klíče předplatného, základní adresy URL a cesty

Tato ukázka se pokusí přečíst klíč předplatného služby Translator Text z proměnné prostředí `TRANSLATOR_TEXT_KEY`. Pokud proměnné prostředí neznáte, můžete hodnotu `subscriptionKey` nastavit jako řetězec a okomentovat podmíněný příkaz.

Zkopírujte do svého projektu tento kód:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Translator Text globální koncový bod je nastaven jako `base_url`. `path` nastaví trasu `transliterate` a určuje, že chceme cílit na rozhraní API verze 3.

`params` slouží k nastavení jazyka vstupu a vstupního a výstupního skriptu. V této ukázce provádíme transkripci z japonštiny do latinky.

>[!NOTE]
> Další informace o koncových bodech, trasách a parametrech požadavků [najdete v článku Translator text API 3,0: Přepis](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Přidání hlaviček

Nejjednodušším způsobem, jak ověřit požadavek, je předat klíč předplatného jako hlavičku `Ocp-Apim-Subscription-Key`, což děláme i v této ukázce. Alternativně můžete klíč předplatného vyměnit za přístupový token a k ověření požadavku předat přístupový token jako hlavičku `Authorization`. Další informace najdete v tématu [Ověřování](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Zkopírujte do svého projektu tento fragment kódu:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-transliterate-text"></a>Vytvoření požadavku na transkripci textu

Definujte řetězec (nebo řetězce), u kterého chcete provést transkripci:

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
}]
```

Dále pomocí modulu `requests` vytvoříme požadavek POST. Tento modul přebírá tři argumenty – zřetězenou adresu URL, hlavičky požadavku a text požadavku:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Tisk odpovědi

Posledním krokem je vytisknout výsledky. Tento fragment kódu očistí výsledky tím, že seřadí klíče, nastaví odsazení a deklaruje oddělovače položek a klíčů.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

To je vše, sestavili jste jednoduchý program, který zavolá službu Translator Text API a vrátí odpověď JSON. Teď je čas program spustit:

```console
python transliterate-text.py
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Ukázková odpověď

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste do svého programu pevně zakódovali klíč předplatného, nezapomeňte po dokončení tohoto rychlého startu tento klíč předplatného odebrat.

## <a name="next-steps"></a>Další postup

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
