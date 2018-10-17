---
title: 'Rychlý start: Získání podporovaných jazyků – Translator Text, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady s použitím služby Translator Text API a Javy.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: a68a1b31cb844a4873bf34c6a292beeb2a4b61c0
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121732"
---
# <a name="quickstart-get-supported-languages-with-python"></a>Rychlý start: Získání podporovaných jazyků s Pythonem

V tomto rychlém startu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady s použitím služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, budete potřebovat [Python 3.x](https://www.python.org/downloads/).

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si, [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Žádost Languages

Pomocí následujícího kódu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady pomocí metody [Languages](./reference/v3-0-languages.md).

1. V oblíbeném editoru kódu vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

output_path = 'output.txt'

def get_languages ():

    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}

    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_languages ()

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
json = json.dumps(json.loads(result), indent=4, ensure_ascii=False).encode('utf-8')

f = open(output_path, 'wb')
f.write (json)
f.close
```

## <a name="languages-response"></a>Odpověď metody Languages

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte vzorový kód pro tento rychlý start a další, včetně překladu a transkripce, a také další vzorové projekty Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady Pythonu na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=python)
