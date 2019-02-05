---
title: 'Rychlý start: Získání délky věty, Python – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak pomocí Pythonu a rozhraní REST API služby Translator Text určit délku věty (ve znacích).
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: erhopf
ms.openlocfilehash: ad5af3e43e1419246d1692c5028cb2453b04516c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696132"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-python"></a>Rychlý start: Použití rozhraní Translator Text API k určení délky větu pomocí Pythonu

V tomto rychlém startu se dozvíte, jak pomocí Pythonu a rozhraní REST API služby Translator Text určit délku věty (ve znacích).

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x
* Klíč předplatného Azure pro službu Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `sentence-length.py`, zkopírujte tento fragment kódu.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
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

Translator Text globální koncový bod je nastaven jako `base_url`. `path` nastaví trasu `breaksentence` a určuje, že chceme cílit na rozhraní API verze 3.

Hodnota `params` v této ukázce slouží k nastavení jazyka zadaného textu. Pro trasu `breaksentence` není hodnota `params` potřeba. Pokud ji v požadavku vynecháte, rozhraní API se pokusí rozpoznat jazyk zadaného textu a uvede tuto informaci společně se skóre spolehlivosti v odpovědi.

>[!NOTE]
> Další informace o koncových bodech, cesty a parametry požadavku najdete v tématu [Translator Text API 3.0: Jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'
params = '&language=en'
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

## <a name="create-a-request-to-determine-sentence-length"></a>Vytvoření požadavku na určení délky věty

Definujte větu (nebo věty), u které chcete určit délku:

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
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
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

To je vše, sestavili jste jednoduchý program, který zavolá službu Translator Text API a vrátí odpověď JSON. Teď je čas program spustit:

```console
python sentence-length.py
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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

> [!div class="nextstepaction"]
> [Prozkoumejte příklady Pythonu na GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Další informace najdete v tématech

Další informace o použití rozhraní Translator Text API na:

* [Překlad textu](quickstart-python-translate.md)
* [Transliterace textu](quickstart-python-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-python-detect.md)
* [Získání alternativních překladů](quickstart-python-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-python-languages.md)
