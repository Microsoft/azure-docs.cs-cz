---
title: PHP rychlý úvodní kurz pro Azure kognitivní služby, analýza textu rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API Analytics Text v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
documentationcenter: ''
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 837a9bab4a4b20be95f03bea0cc97b0b3f414d82
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342806"
---
# <a name="quickstart-for-text-analytics-api-with-php"></a>Rychlý start pro Analýza textu rozhraní API s PHP 
<a name="HOLTop"></a>

Tento článek ukazuje, jak k [zjistit jazyk](#Detect), [analyzovat postojích](#SentimentAnalysis), [extrahovat klíče frází](#KeyPhraseExtraction), a [identifikovat propojených entit](#Entities) pomocí [rozhraní API Analytics Text](//go.microsoft.com/fwlink/?LinkID=759711) s PHP.

Odkazovat [definice rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) pro technická dokumentace k rozhraní API.

## <a name="prerequisites"></a>Požadavky

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **Text Analytics API**. Můžete použít **úroveň free pro 5 000 transakce za měsíc** dokončete tento rychlý start.

Je také nutné mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) který byl vygenerován pro vás při přihlašování nahoru. 

<a name="Detect"></a>

## <a name="detect-language"></a>Zjištění jazyka

Rozhraní API jazyka detekce zjistí jazyk textového dokumentu, pomocí [metoda zjistit jazyk](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

1. Vytvoření nového projektu PHP v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `accessKey` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Nahraďte umístění v `host` (aktuálně `westus`) pro danou oblast registraci aplikace.
5. Spusťte program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.0/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Jazyk detekce odpovědi**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analýza mínění

Rozhraní API Analysis postojích detexts postojích sadu záznamů text, pomocí [postojích metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Následující příklad skóre dva dokumenty, jeden v angličtině a druhý ve španělštině.

1. Vytvoření nového projektu PHP v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `accessKey` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Nahraďte umístění v `host` (aktuálně `westus`) pro danou oblast registraci aplikace.
5. Spusťte program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.0/sentiment';

function GetSentiment ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Postojích analýzy odpovědi**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrakce klíčových frází

Rozhraní API extrakce frázi klíč extrahuje frází klíč z textového dokumentu, pomocí [klíč frází metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Následující příklad extrahuje klíče frází pro angličtinu a slovenštinu dokumenty.

1. Vytvoření nového projektu PHP v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `accessKey` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Nahraďte umístění v `host` (aktuálně `westus`) pro danou oblast registraci aplikace.
5. Spusťte program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.0/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>

```

**Klíče frázi extrakce odpovědi**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities"></a>Identifikovat propojených entit

Rozhraní API propojení entita identifikuje dobře známé entity v textového dokumentu, pomocí [Entity propojení metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Následující příklad určuje entity pro angličtinu dokumenty.

1. Vytvoření nového projektu PHP v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `accessKey` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Nahraďte umístění v `host` (aktuálně `westus`) pro danou oblast registraci aplikace.
5. Spusťte program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.0/entities';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'en', 'text' => 'The Seattle Seahawks won the Super Bowl in 2014.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>

```

**Odpověď serveru linking entity**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu pomocí Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)
