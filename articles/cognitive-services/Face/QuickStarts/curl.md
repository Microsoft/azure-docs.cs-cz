---
title: 'Rychlý start: Rozpoznávání tváří v obrázku se cURL a rozhraní Azure REST API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní Azure REST API pro rozpoznávání tváře pomocí cURL rozpoznávání tváří v obrázku.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: b6c05382a3a40aa9157fd64a7f0192c58e79b0ee
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853774"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Rychlý start: Rozpoznávání tváří v obrázku pomocí rozhraní REST API pro rozpoznávání tváře a cURL

V tomto rychlém startu použijete rozhraní Azure REST API pro rozpoznávání tváře pomocí cURL detekovat lidské tváře v obrázku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.

## <a name="write-the-command"></a>Zápis příkazu
 
Použijete příkaz podobný tomuto volání rozhraní API pro rozpoznávání tváře a získání dat pro rozpoznávání tváře atribut z image. Nejprve zkopírujte kód do textového editoru&mdash;budete muset změnit některé části příkazu, než budete moct spustit.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Klíč předplatného
Nahraďte `<Subscription Key>` platný klíč předplatného pro rozpoznávání tváře.

### <a name="face-endpoint-url"></a>Adresa URL koncového bodu pro rozpoznávání tváře

Adresa URL `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` Určuje koncový bod Azure pro rozpoznávání tváře do dotazu. Může být nutné změnit první část této adresy URL tak, aby odpovídaly oblasti, která odpovídá váš klíč předplatného (najdete v článku [dokumenty k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) seznam všechny koncové body oblasti).

### <a name="url-query-string"></a>Řetězec dotazu adresy URL

Řetězec dotazu adresy URL koncového bodu pro rozpoznávání tváře Určuje, které pro rozpoznávání tváře atributy pro načtení. Můžete chtít změnit tento řetězec v závislosti na zamýšlený účel použití.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Zdrojová adresa URL obrázku
Zdrojová adresa URL určuje image chcete použít jako vstup. Můžete změnit to tak, aby odkazoval na všechny image, kterou chcete analyzovat.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Spusťte příkaz

Po provedení změny, otevřete příkazový řádek a zadejte nový příkaz. Rozpoznávání tváře informace zobrazené jako data JSON v okně konzoly byste měli vidět. Příklad:

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

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste napsali příkaz cURL, který volá rozhraní API pro rozpoznávání tváře Azure rozpoznávání tváří v obrázku a vrátíte se jejich atributy. Dále prozkoumejte referenční dokumentaci rozhraní API pro rozpoznávání tváře na další informace.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)