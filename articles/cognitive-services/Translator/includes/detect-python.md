---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9207814c921f51b10939c6e9d1747e1e124f9890
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907189"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `detect.py`, zkopírujte tento fragment kódu.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests uuid`.

První komentář říká interpretu Pythonu, že má použít kódování UTF-8. Pak se importují požadované moduly pro čtení klíče předplatného z proměnné prostředí, vytvoření požadavku HTTP, vytvoření jedinečného identifikátoru a zpracování odpovědi JSON vrácené službou Translator Text API.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Nastavení klíče předplatného, koncového bodu a cesty

Tato ukázka se pokusí přečíst klíč předplatného Translator text a koncový bod z proměnných prostředí: `TRANSLATOR_TEXT_KEY` a `TRANSLATOR_TEXT_ENDPOINT`. Pokud nejste obeznámeni s proměnnými prostředí, můžete nastavit `subscription_key` a `endpoint` jako řetězce a přidat komentář k podmíněným příkazům.

Zkopírujte do svého projektu tento kód:

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

Translator Text globální koncový bod je nastaven jako `endpoint`. `path` nastaví trasu `detect` a určuje, že chceme cílit na rozhraní API verze 3.

>[!NOTE]
> Další informace o koncových bodech, trasách a parametrech požadavků [najdete v článku Translator text API 3,0: Zjistit](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```python
path = '/detect?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Přidání hlaviček

Nejjednodušším způsobem, jak ověřit požadavek, je předat klíč předplatného jako hlavičku `Ocp-Apim-Subscription-Key`, což děláme i v této ukázce. Alternativně můžete klíč předplatného vyměnit za přístupový token a k ověření požadavku předat přístupový token jako hlavičku `Authorization`. Další informace najdete v tématu [Ověřování](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Zkopírujte do svého projektu tento fragment kódu:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-detect-text-language"></a>Vytvoření požadavku na rozpoznání jazyka textu

Definujte řetězec (nebo řetězce), u kterého chcete rozpoznat jazyk:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
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
python detect.py
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Ukázková odpověď

V tomto [seznamu jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)Najděte zkratku země/oblasti.

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

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
