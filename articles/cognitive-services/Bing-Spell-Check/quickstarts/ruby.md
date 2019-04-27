---
title: 'Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Ruby'
titlesuffix: Azure Cognitive Services
description: Začněte používat rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d488923f38a9c65cb117b4535b50bb9fdff2dbfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384836"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Ruby

V tomto rychlém startu můžete provést první volání do Bingu pravopisu zkontrolujte REST API pomocí Ruby. Tato jednoduchá aplikace odešle požadavek na rozhraní API a vrátí seznam slov, nerozpozná, za nímž následuje navrhovaných oprav. Aplikace je sice napsaná v Ruby, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Požadavky

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Ruby v oblíbeném editoru nebo prostředí IDE a přidejte následující požadavky. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Vytváření proměnných pro váš klíč předplatného, identifikátor URI koncového bodu a cestu. Vytvoření parametry požadavku připojením `mkt=` parametr uvedení na trh, a `&mode` k `proof` důkazu režimu.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Odeslat žádost o kontrolu pravopisu

1. Vytvořte identifikátor URI z řetězce identifikátoru uri, cestu a parametry vašeho hostitele. Sada je dotaz, který obsahuje text, že který chcete kontrola pravopisu.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Vytvořte žádost o použitím tohoto identifikátoru URI vytvořený výše. Přidejte klíč k `Ocp-Apim-Subscription-Key` záhlaví.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Odešlete požadavek.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Získání odpovědi JSON a vypíše do konzoly. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="example-json-response"></a>Příklad JSON odpovědi

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové – aplikace](../tutorials/spellcheck.md)

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
