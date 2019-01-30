---
title: 'Rychlý start: Na tváří v jazyce Java obrázek – rozhraní API pro rozpoznávání Emocí, rozpoznávání emocí'
description: Získáte informace a ukázku kódu, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí založené na Javě pro Android.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: a3250771c51495890c54060dc46b8bd88a8f54af
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228674"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvářejte aplikace pro rozpoznávání emocí na tváří v obrázku.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Tento článek obsahuje informace a ukázku kódu, které vám pomůžou rychle začít pracovat s [metodou Recognize pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) v klientské knihovně rozhraní API pro rozpoznávání emocí pro Android. Ukázka předvádí, jak použít jazyk Java k rozpoznávání emocí vyjadřovaných různými lidmi.

## <a name="prerequisites"></a>Požadavky
* Rozhraní API pro rozpoznávání emocí v Javě pro sadu Android SDK najdete [tady](https://github.com/Microsoft/Cognitive-emotion-android).
* Klíč předplatného můžete bezplatně získat [tady](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-java-for-android-example-request"></a>Příklad požadavku na rozpoznávání emocí v Javě pro Android

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
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
