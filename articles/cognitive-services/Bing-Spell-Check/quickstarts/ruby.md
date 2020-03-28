---
title: 'Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API a Ruby – kontrola pravopisu bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat rozhraní REST API kontroly pravopisu Bingu ke kontrole pravopisu a gramatiky pomocí tohoto rychlého startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 89a2a345e2a4e3ca1be31297e614e86f800e6316
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448421"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API pro kontrolu pravopisu Bingu a ruby

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API REST PRO KONTROLU pravopisu Bingu pomocí ruby. Tato jednoduchá aplikace odešle požadavek do rozhraní API a vrátí seznam slov, která nepoznala, následovaná navrhovanými opravami. Aplikace je sice napsaná v Ruby, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Požadavky

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Ruby ve svém oblíbeném editoru nebo prostředí IDE a přidejte následující požadavky. 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Vytvořte proměnné pro klíč předplatného, identifikátor URI koncového bodu a cestu. Vytvořte parametry požadavku připojením parametru k vašemu `mkt=` trhu a `&mode` do režimu důkazu. `proof` Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Odeslání žádosti o kontrolu pravopisu

1. Vytvořte identifikátor URI z řetězce uri hostitele, cesty a parametrů. Nastavte jeho dotaz tak, aby obsahoval text, který chcete zkontrolovat pravopisem.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Vytvořte požadavek pomocí identifikátoru URI vytvořeného výše. Přidejte klíč `Ocp-Apim-Subscription-Key` do záhlaví.

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

4. Získejte odpověď JSON a vytiskněte ji do konzole. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Sestavení a spuštění projektu.

Pokud používáte příkazový řádek, spusťte aplikaci pomocí následujícího příkazu.

```bash
ruby <FILE_NAME>.rb
```

## <a name="example-json-response"></a>Příklad odpovědi JSON

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
