---
title: Rychlý start cURL rozpoznávání emocí úrovně rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít, pomocí rozhraní API pro rozpoznávání emocí úrovně cURL v kognitivní služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: d36a191d3589702d676e694715f34ea1c924e2d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342699"
---
# <a name="emotion-api-curl-quick-start"></a>Rozpoznávání emocí úrovně rozhraní API cURL rychlý Start

> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [rozpoznávání emocí úrovně rozhraní API rozpoznat metoda](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s cURL rozpoznat emoce vyjádřená jeden nebo více osob v obraze. 

## <a name="prerequisite"></a>Požadavek
* Získat klíč bezplatné předplatné [sem](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Rozpoznat, že cURL emoce příklad požadavku

> [!NOTE]
> Ve volání REST jako jste použili k získání klíče pro odběr je nutné použít stejné umístění. Například pokud vaše předplatné klíče se získají z westcentralus, nahraďte "westus" v adrese URL níže "westcentralus".

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
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

