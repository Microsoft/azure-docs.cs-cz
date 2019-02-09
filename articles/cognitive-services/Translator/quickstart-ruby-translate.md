---
title: 'Rychlý start: Přeložení textu, Ruby – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu přeložíte text z jednoho jazyka do jiného pomocí služby Translator Text API s Ruby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: erhopf
ms.openlocfilehash: f54421460e3af46570b67bb541df3afb755a1347
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979288"
---
# <a name="quickstart-translate-text-with-the-translator-text-rest-api-ruby"></a>Rychlý start: Přeložení textu s rozhraním Translator Text REST API (Ruby)

V tomto rychlém startu přeložíte text z jednoho jazyka do jiného pomocí služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, budete potřebovat [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) nebo novější.

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="translate-request"></a>Žádost Translate

Následující kód překládá zdrojový text z jednoho jazyka do jiného pomocí metody [Translate](./reference/v3-0-translate.md).

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Ruby.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/translate?api-version=3.0'

# Translate to German and Italian.
params = '&to=de&to=it'

uri = URI (host + path + params)

text = 'Hello, world!'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="translate-response"></a>Odpověď metody Translate

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další postup

Prozkoumejte vzorový kód pro tento a další rychlé starty, včetně transkripce a identifikace jazyka a také dalších vzorových projektů služby Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady Ruby na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=ruby)
