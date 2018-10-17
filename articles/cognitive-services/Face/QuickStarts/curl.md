---
title: 'Rychlý start: Rozpoznávání tváří na obrázku – rozhraní API pro rozpoznávání tváře, cURL'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete rozpoznávat tváře z obrázku pomocí rozhraní API pro rozpoznávání tváře a jazyka cURL.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: 9ae8135481eb44e3b4b876fd4916e78a41c65042
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121890"
---
# <a name="quickstart-detect-faces-in-an-image-using-curl"></a>Rychlý start: Rozpoznávání tváří na obrázku pomocí cURL

V tomto rychlém startu budete rozpoznávat tváře na obrázku pomocí rozhraní API pro rozpoznávání tváře.

## <a name="prerequisites"></a>Požadavky

Ke spuštění této ukázky budete potřebovat klíč předplatného. Klíče bezplatného zkušebního předplatného můžete získat v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Pomocí metody [Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozpoznejte tváře na obrázku a vraťte atributy tváře, včetně:

* Face ID: jedinečná hodnota ID používaná v několika scénářích rozhraní API pro rozpoznávání tváře
* Obdélník tváře: umístění tváře na obrázku vlevo a nahoře a šířka a výška obličeje
* Rysy: soustava 27 bodů obličejových rysů odkazující na důležité polohy součástí obličeje.
* Atributy obličeje včetně věku, pohlaví, intenzity úsměvu, pozice hlavy a vousů.

Pokud chcete spustit ukázku, postupujte takto:

1. Otevřete příkazový řádek.
2. Místo `<Subscription Key>` použijte platný klíč předplatného.
3. V případě potřeby změňte adresu URL (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`), aby se použilo umístění, ze kterého jste získali klíče předplatného.
4. Volitelně můžete změnit obrázek (`"{\"url\":...`) pro analýzu.
5. Kód vložte do okna příkazového řádku.
6. Příkaz spusťte.

### <a name="face---detect-request"></a>Žádost Face - Detect

> [!NOTE]
> Ve volání REST musíte použít stejné umístění, které jste použili k získání klíčů předplatného. Pokud jste například získali klíče předplatného z „westus“, nahraďte westcentralus v následující adrese URL umístěním westus.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>Odpověď metody Face - Detect

Úspěšná odpověď se vrátí ve formátu JSON.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro rozpoznávání lidských tváří na obrázku, ohraničte obličeje obdélníky a vraťte atributy, jako je věk a pohlaví.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
