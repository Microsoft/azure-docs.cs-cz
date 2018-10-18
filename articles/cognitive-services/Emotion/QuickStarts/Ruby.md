---
title: 'Rychlý start: Rozpoznávání emocí ve výrazech tváří na obrázku – rozhraní API pro rozpoznávání emocí, Ruby'
titlesuffix: Azure Cognitive Services
description: Získejte informace a vzorky kódu, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí pomocí Ruby.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: bcab24334c1ee4e47061ce6ea28bd60039e17b3f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239025"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvoření aplikace pro rozpoznávání emocí ve výrazech tváří na obrázku

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou rychle začít používat [metodu Recognize rozhraní API pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s Ruby k rozpoznávání emocí vyjadřovaných jedním člověkem nebo několika lidmi na obrázku.

## <a name="prerequisite"></a>Požadavek
* [Tady](https://azure.microsoft.com/try/cognitive-services/) získejte klíč svého bezplatného předplatného.

## <a name="recognize-emotions-ruby-example-request"></a>Ukázka požadavku na rozpoznávání emocí s Ruby

Změňte adresu URL REST na umístění, ve kterém jste získali klíče předplatného, hodnotu „Ocp-Apim-Subscription-Key“ nahraďte platným klíčem předplatného a do proměnné `body` přidejte adresu URL nějaké fotografie.

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

```

## <a name="recognize-emotions-sample-response"></a>Ukázka odpovědi rozpoznávání emocí
Úspěšné volání vrátí pole položek tváří s přidruženým skóre rozpoznávání emocí, které bude seřazené podle velikosti obdélníku s tváří v sestupném pořadí. Prázdná odpověď znamená, že se nezjistily žádné tváře. Položka rozpoznávání emocí obsahuje následující pole:
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
