---
title: 'Rychlý start: Rozpoznávání emocí na tváří v obrázku – rozhraní API pro rozpoznávání Emocí, PHP'
titlesuffix: Azure Cognitive Services
description: Získejte informace a vzorky kódu, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí pomocí PHP.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 9faf263dbc5f8c590018d1c446ba340b3b9781eb
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884065"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvářejte aplikace pro rozpoznávání emocí na tváří v obrázku.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou rychle začít používat PHP a [metodu Recognize rozhraní API pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) k rozpoznávání emocí vyjadřovaných jedním člověkem nebo několika lidmi na obrázku.

## <a name="prerequisite"></a>Požadavek
* [Tady](https://azure.microsoft.com/try/cognitive-services/) získejte klíč svého bezplatného předplatného.

## <a name="recognize-emotions-php-example-request"></a>Ukázka požadavku na rozpoznávání emocí s PHP

Změňte adresu URL REST na umístění, ve kterém jste získali klíče předplatného, tělo změňte na adresu URL obrázku, který chcete vyzkoušet, a hodnotu „Ocp-Apim-Subscription-Key“ nahraďte platným klíčem předplatného.

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

## <a name="recognize-emotions-sample-response"></a>Ukázka odpovědi rozpoznávání emocí
Úspěšné volání vrátí pole položek tváří s přidruženým skóre rozpoznávání emocí, které bude seřazené podle velikosti obdélníku tváře v sestupném pořadí. Prázdná odpověď znamená, že se nezjistily žádné tváře. Položka rozpoznávání emocí obsahuje následující pole:
* faceRectangle – umístění obdélníku tváře na obrázku.
* scores – skóre rozpoznávání emocí pro jednotlivé tváře na obrázku.

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
