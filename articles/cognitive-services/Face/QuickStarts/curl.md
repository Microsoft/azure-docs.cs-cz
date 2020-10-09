---
title: 'Rychlý Start: detekce plošek v obrázku pomocí REST API a kudrlinkou Azure'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API Azure Face s kudrlinkou k detekci ploch v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: cd7d1a476f09a2fbfffa687a28616c8faeaae22c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858264"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Rychlý Start: detekce plošek v obrázku pomocí REST API obličeje a kudrlinkou

V tomto rychlém startu použijete REST API Azure Face s kudrlinkou k detekci lidských plošek v obraze.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="write-the-command"></a>Zápis příkazu
 
Použijete příkaz podobný následujícímu pro volání Face API a získání dat atributu Face z obrázku. Nejprve zkopírujte kód do textového editoru &mdash; . před spuštěním příkazu je třeba provést změny určitých částí příkazu.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_2":::

### <a name="subscription-key"></a>Klíč předplatného
Nahraďte `<Subscription Key>` platným klíčem pro odběr obličeje.

### <a name="face-endpoint-url"></a>Adresa URL koncového bodu obličeje

Adresa URL `https://<My Endpoint String>.com/face/v1.0/detect` indikuje koncový bod Azure Face pro dotazování. Možná budete muset změnit první část této adresy URL tak, aby odpovídala koncovému bodu, který odpovídá vašemu klíči předplatného.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="image-source-url"></a>Adresa URL zdroje obrázku
Zdrojová adresa URL označuje obrázek, který se má použít jako vstup. Tuto změnu můžete změnit tak, aby odkazovala na libovolný obrázek, který chcete analyzovat.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Spuštěním příkazu

Po provedení změn otevřete příkazový řádek a zadejte nový příkaz. V okně konzoly byste měli vidět informace o tváři jako data JSON. Příklad:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

## <a name="extract-face-attributes"></a>Extrahovat atributy obličeje
 
Chcete-li extrahovat atributy obličeje, použijte model detekce 1 a přidejte `returnFaceAttributes` parametr dotazu. Příkaz by měl nyní vypadat takto. Stejně jako dřív vložte klíč předplatného a koncový bod pro svůj obličej.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_1":::

Vracené informace o tváři teď obsahují atributy obličeje. Příklad:

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

V tomto rychlém startu jste napsali příkaz složeného příkazu, který volá službu Azure Face k detekci ploch v obrázku a vrácení jejich atributů. Dále si Projděte referenční dokumentaci Face API, kde najdete další informace.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
