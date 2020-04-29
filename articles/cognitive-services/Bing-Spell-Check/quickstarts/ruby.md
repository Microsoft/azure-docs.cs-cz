---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a Ruby-Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky pomocí tohoto rychlého startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 89a2a345e2a4e3ca1be31297e614e86f800e6316
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448421"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Ruby

Pomocí tohoto rychlého startu můžete provést vaše první volání Kontrola pravopisu Bingu REST API pomocí Ruby. Tato jednoduchá aplikace pošle požadavek do rozhraní API a vrátí seznam slov, která nebyla rozpoznána, a za ním Doporučené opravy. Aplikace je sice napsaná v Ruby, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb) .

## <a name="prerequisites"></a>Požadavky

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených editorech nebo v integrovaném vývojovém prostředí vytvořte nový soubor Ruby a přidejte následující požadavky. 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Vytvořte proměnné pro klíč předplatného, identifikátor URI a cestu koncového bodu. Vytvořte parametry žádosti připojením `mkt=` parametru k vašemu trhu a `&mode` režimu `proof` kontroly pravopisu. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

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

2. Vytvořte žádost pomocí identifikátoru URI sestaveného výše. Přidejte svůj klíč do `Ocp-Apim-Subscription-Key` záhlaví.

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

Sestavte a spusťte projekt.

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
