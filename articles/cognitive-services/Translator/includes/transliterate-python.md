---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 1e9c3ccac9205e38c8df341a6f7ca286811d759b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586492"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt pomocí oblíbeného integrovaného vývojového prostředí (IDE) nebo nové složky se souborem s názvem `transliterate-text.py` na vašem počítači. Potom tento fragment kódu zkopírujte do svého projektu nebo souboru:

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests uuid`.

První komentář říká interpretu Pythonu, že má použít kódování UTF-8. Pak se naimportují požadované moduly, abyste si přečetli klíč předplatného z proměnné prostředí, sestavíte požadavek HTTP, vytvoříte jedinečný identifikátor a zpracujete odpověď JSON vrácenou překladatelem.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Nastavení klíče předplatného, koncového bodu a cesty

Tato ukázka se pokusí přečíst klíč a koncový bod předplatného překladatele z proměnných prostředí: `TRANSLATOR_TEXT_KEY` a `TRANSLATOR_TEXT_ENDPOINT` . Pokud nejste obeznámeni s proměnnými prostředí, můžete nastavit `subscription_key` a `endpoint` jako řetězce a přidat komentář k podmíněným příkazům.

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

Globální koncový bod překladatele je nastaven jako `endpoint` . `path` nastaví trasu `transliterate` a určuje, že chceme cílit na rozhraní API verze 3.

`params` slouží k nastavení jazyka vstupu a vstupního a výstupního skriptu. V této ukázce provádíme transkripci z japonštiny do latinky.

>[!NOTE]
> Další informace o koncových bodech, trasách a parametrech požadavků naleznete v tématu [Translator 3,0: repřepised](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = endpoint + path + params
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

To je to, že jste připravili jednoduchý program, který bude volat překladatele a vrátil odpověď JSON. Teď je čas program spustit:

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

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s překladatelem dělat.

> [!div class="nextstepaction"]
> [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
