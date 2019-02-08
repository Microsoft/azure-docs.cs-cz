---
title: 'Rychlý start: Můžete tak rozpoznávat emoce na tváří v obrázku – rozhraní API pro rozpoznávání Emocí, cURL'
titlesuffix: Azure Cognitive Services
description: Získejte informace a vzorky kódu, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí pomocí nástroje cURL.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 8ecaf5e6de958a0f0af0ccccf9c712a227b504b9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857716"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvářejte aplikace pro rozpoznávání emocí na tváří v obrázku.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/).

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou rychle začít používat [metodu Recognize rozhraní API pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s nástrojem cURL k rozpoznávání emocí vyjadřovaných jedním člověkem nebo několika lidmi na obrázku.

## <a name="prerequisite"></a>Požadavek
* [Tady](https://azure.microsoft.com/try/cognitive-services/) získejte klíč svého bezplatného předplatného.

## <a name="recognize-emotions-curl-example-request"></a>Ukázka požadavku na rozpoznávání emocí s nástrojem cURL

> [!NOTE]
> Ve volání REST musíte použít stejné umístění, které jste použili k získání klíčů předplatného. Pokud jste třeba získali klíče předplatného z oblasti westcentralus, nahraďte oblast westus v následující adrese URL oblastí westcentralus.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
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
