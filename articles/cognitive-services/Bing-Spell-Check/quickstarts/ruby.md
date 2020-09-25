---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a Ruby-Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat Kontrola pravopisu Bingu REST API a Ruby pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 0b29ba1b09123784bfbac6fda4f5a1c6953f4e49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327387"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Ruby

Pomocí tohoto rychlého startu můžete provést vaše první volání Kontrola pravopisu Bingu REST API pomocí Ruby. Tato jednoduchá aplikace pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. 

I když je tato aplikace napsaná v Ruby, je rozhraní API webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb) .

## <a name="prerequisites"></a>Požadavky

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených editorech nebo v integrovaném vývojovém prostředí vytvořte nový soubor Ruby a přidejte následující požadavky: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Vytvořte proměnné pro svůj klíč předplatného, identifikátor URI koncového bodu a cestu. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek. Vytvořte parametry žádosti:

   1. Přiřaďte svůj kód na trhu k `mkt` parametru pomocí `=` operátoru. Kód trhu je kód země nebo oblasti, ze které provedete požadavek. 

   1. Přidejte `mode` parametr s `&` operátorem a potom přiřaďte režim kontroly pravopisu. Režim může být buď `proof` (catch nejvíc pravopisné/gramatické chyby) `spell` , nebo (catch nejvíc pravopisných chyb, ale ne tolik gramatických chyb). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Odeslat žádost o kontrolu pravopisu

1. Vytvořte identifikátor URI z identifikátoru URI hostitele, cesty a řetězce parametrů. Nastavte dotaz tak, aby obsahoval text, který chcete kontrolovat pravopis.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Vytvořte požadavek pomocí dříve vytvořeného identifikátoru URI. Přidejte svůj klíč do `Ocp-Apim-Subscription-Key` záhlaví.

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

4. Získejte odpověď JSON a vytiskněte ji do konzoly. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Sestavte a spusťte projekt. Pokud používáte příkazový řádek, spusťte aplikaci pomocí následujícího příkazu:

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
- [Odkaz na rozhraní API Bingu pro kontrolu pravopisu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
