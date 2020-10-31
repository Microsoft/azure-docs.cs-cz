---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a PHP-Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak jednoduchá aplikace PHP pošle požadavek na rozhraní API Bingu pro kontrolu pravopisu a vrátí seznam navrhovaných oprav.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: e82ac36b0943a4286ac883de5b95c26e9e13a1e5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083953"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-php"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a PHP

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace PHP pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. 

I když je tato aplikace napsaná v PHP, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Předpoklady

* [PHP 5.6. x](https://php.net/downloads.php)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="get-bing-spell-check-rest-api-results"></a>Získat výsledky Kontrola pravopisu Bingu REST API

1. Ve svém oblíbeném integrovaném vývojovém prostředí vytvořte nový projekt PHP.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.
5. Spustíte program.
    
    ```php
    <?php
    
    // NOTE: Be sure to uncomment the following line in your php.ini file.
    // ;extension=php_openssl.dll
    
    // These properties are used for optional headers (see below).
    // define("CLIENT_ID", "<Client ID from Previous Response Goes Here>");
    // define("CLIENT_IP", "999.999.999.999");
    // define("CLIENT_LOCATION", "+90.0000000000000;long: 00.0000000000000;re:100.000000000000");
    
    $host = 'https://api.cognitive.microsoft.com';
    $path = '/bing/v7.0/spellcheck?';
    $params = 'mkt=en-us&mode=proof';
    
    $input = "Hollo, wrld!";
    
    $data = array (
        'text' => urlencode ($input)
    );
    
    // NOTE: Replace this example key with a valid subscription key.
    $key = 'ENTER KEY HERE';
    
    // The following headers are optional, but it is recommended
    // that they are treated as required. These headers will assist the service
    // with returning more accurate results.
    //'X-Search-Location' => CLIENT_LOCATION
    //'X-MSEdge-ClientID' => CLIENT_ID
    //'X-MSEdge-ClientIP' => CLIENT_IP
    
    $headers = "Content-type: application/x-www-form-urlencoded\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    
    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => http_build_query ($data)
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    
    if ($result === FALSE) {
        /* Handle error */
    }
    
    $json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
    echo $json;
    ?>
    ```


## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci spuštěním webového serveru a přechodem do souboru.

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
