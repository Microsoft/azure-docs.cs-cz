---
title: 'Rychlý Start: odeslání žádosti o vyhledávání do REST API pomocí PHP-Vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odeslat požadavek do Vyhledávání entit Bingu REST API pomocí PHP a přijmout odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 6d994282d2c8d2b97110d57362b95254945daa14
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084691"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-php"></a>Rychlý Start: odeslání žádosti o vyhledávání do Vyhledávání entit Bingu REST API pomocí PHP

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Tento rychlý Start použijte k provedení prvního volání rozhraní API Bingu pro vyhledávání entit a zobrazení odpovědi JSON. Tato jednoduchá aplikace PHP pošle vyhledávací dotaz na zprávy do rozhraní API a zobrazí odpověď. 

I když je tato aplikace napsaná v PHP, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Předpoklady

* [PHP 5.6. x](https://php.net/downloads.php) nebo novější

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="search-entities"></a>Vyhledávání entit

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Ve svém oblíbeném integrovaném vývojovém prostředí vytvořte nový projekt PHP.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.
5. Spustíte program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// **_ Update or verify the following values. _*_
// _*********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$subscriptionKey = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsoft.com";
$path = "/bing/v7.0/entities";

$mkt = "en-US";
$query = "italian restaurants near me";

function search ($host, $path, $key, $mkt, $query) {

    $params = '?mkt=' . $mkt . '&q=' . urlencode ($query);

    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'GET'
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    return $result;
}

$result = search ($host, $path, $subscriptionKey, $mkt, $query);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

## <a name="example-json-response"></a>Příklad odpovědi JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )
* [Odkaz na rozhraní API Bingu pro vyhledávání entit](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
