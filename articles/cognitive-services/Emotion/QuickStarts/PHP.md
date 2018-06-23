---
title: Rychlý start rozpoznávání emocí úrovně rozhraní API PHP | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít pracovat s PHP v kognitivní služby pomocí rozhraní API pro rozpoznávání emocí úrovně.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: bb3f3bc1421e7c65a1fa6095f22f499415c50890
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342701"
---
# <a name="emotion-api-php-quick-start"></a>Rozpoznávání emocí úrovně rozhraní API PHP rychlý Start

> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat PHP a [rozpoznávání emocí úrovně rozhraní API rozpoznat metoda](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) rozpoznat emoce vyjádřená jeden nebo více osob v obraze. 

## <a name="prerequisite"></a>Požadavek
* Získat klíč bezplatné předplatné [sem](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-php-example-request"></a>Rozpoznat emoce PHP příklad požadavku

Změňte adresu URL REST a použít tak umístění, kde získat klíče pro předplatné, změňte obsah na adresu URL obrázku, který chcete testovat a nahraďte hodnotu "Ocp-Apim-Subscription-Key" klíč platné předplatné.

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

## <a name="recognize-emotions-sample-response"></a>Rozpoznat emoce ukázková odpověď
Úspěšné volání vrátí pole položek vzhled a jejich přidružené rozpoznávání emocí úrovně skóre podle velikosti obdélníku řez v sestupném pořadí. Prázdnou odpověď označuje, že nebyly zjištěny žádné řezy. Položka rozpoznávání emocí úrovně obsahuje následující pole:
* faceRectangle - obdélníku umístění řez v bitové kopii.
* skóre - rozpoznávání emocí úrovně skóre pro každý řez v bitové kopii. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

