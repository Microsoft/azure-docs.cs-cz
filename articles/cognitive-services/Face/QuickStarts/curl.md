---
title: 'Úvodní příručka: Detekce tváří v bitové kopii pomocí rozhraní Azure REST API a cURL'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní AZURE Face REST API s cURL k detekci tváří v bitové kopii.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 6a1a6d1fdce4853a2ac73f10eb4cf0a0505fa4c7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165896"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Úvodní příručka: Detekce tváří v obraze pomocí rozhraní API FACE REST a cURL

V tomto rychlém startu použijete rozhraní AZURE Face REST API s cURL k detekci lidských tváří v bitové kopii.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Face. Můžete získat bezplatný klíč zkušebního předplatného od [společnosti Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili ke službě Face a získali klíč.

## <a name="write-the-command"></a>Napsat příkaz
 
Pomocí příkazu, jako je následující, zavoláte rozhraní API pro rozpoznávání tváře a získáte data atributů obličeje z obrázku. Nejprve zkopírujte kód do&mdash;textového editoru, který budete muset před spuštěním provést změny v určitých částech příkazu.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Klíč předplatného
Nahraďte `<Subscription Key>` je platným klíčem předplatného face.

### <a name="face-endpoint-url"></a>Adresa URL koncového bodu obličeje

Adresa `https://<My Endpoint String>.com/face/v1.0/detect` URL označuje koncový bod Azure Face k dotazu. Možná budete muset změnit první část této adresy URL tak, aby odpovídala koncovému bodu, který odpovídá klíči předplatného.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Řetězec dotazu URL

Řetězec dotazu adresy URL koncového bodu Plocha určuje, které atributy čela mají být načteny. Tento řetězec můžete změnit v závislosti na zamýšleném použití.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Adresa URL zdroje obrázku
Zdrojová adresa URL označuje obrázek, který má být používán jako vstup. Můžete to změnit tak, aby ukazoval na libovolný obrázek, který chcete analyzovat.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Spuštěním příkazu

Po provedení změn otevřete příkazový řádek a zadejte nový příkaz. V okně konzoly byste měli vidět informace o ploše zobrazené jako data JSON. Například:

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

V tomto rychlém startu jste napsali příkaz cURL, který volá službu Azure Face ke zjištění tváří v bitové kopii a vrácení jejich atributů. Další informace najdete v dokumentaci k rozhraní API pro rozpoznávání tváře.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
