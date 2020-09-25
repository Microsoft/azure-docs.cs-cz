---
title: 'Rychlý Start: detekce ploch v imagi pomocí REST API Azure a Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API Azure Face s Node.js k detekci ploch v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: f9ba2decf051bc21f91058e67fea8677b24714a6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322916"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Rychlý Start: detekce plošek v obrázku pomocí REST API obličeje a Node.js

V tomto rychlém startu použijete REST API Azure Face s Node.js k detekci lidských plošek v obraze.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
- Editor kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="set-up-the-node-environment"></a>Nastavení prostředí uzlu

Přejít do složky, ve které chcete vytvořit projekt, a vytvořit nový soubor *facedetection.js*. Pak modul nainstalujte `axios` do tohoto projektu. To umožňuje skriptům vytvářet požadavky HTTP.

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>Zápis Node.js skriptu

Do *facedetection.js*vložte následující kód. Tato pole určují, jak se připojit ke službě obličeje a kde získat vstupní data. [Vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) a přidejte do nich klíč předplatného a koncový bod pro svůj obličej. Možná budete chtít změnit `imageUrl` pole tak, aby odkazovalo na vlastní vstupní image.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```javascript
'use strict';

const axios = require('axios').default;

// Add a valid subscription key and endpoint to your environment variables.
let subscriptionKey = process.env['FACE_SUBSCRIPTION_KEY']
let endpoint = process.env['FACE_ENDPOINT'] + '/face/v1.0/detect'

// Optionally, replace with your own image URL (for example a .jpg or .png URL).
let imageUrl = 'https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/faces.jpg'
```

Pak přidejte následující kód, který volá Face API a získá data atributu Face ze vstupní image. `returnFaceAttributes`Pole určuje, které atributy tváře mají být načteny. Tento řetězec možná budete chtít změnit v závislosti na zamýšleném použití.


```javascript
// Send a POST request
axios({
    method: 'post',
    url: endpoint,
    params : {
        returnFaceId: true,
        returnFaceLandmarks: false,
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
    },
    data: {
        url: imageUrl,
    },
    headers: { 'Ocp-Apim-Subscription-Key': subscriptionKey }
}).then(function (response) {
    console.log('Status text: ' + response.status)
    console.log('Status text: ' + response.statusText)
    console.log()
    //console.log(response.data)
    response.data.forEach((face) => {
      console.log('Face ID: ' + face.faceId)
      console.log('Face rectangle: ' + face.faceRectangle.top + ', ' + face.faceRectangle.left + ', ' + face.faceRectangle.width + ', ' + face.faceRectangle.height)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Head pose: ' + JSON.stringify(face.faceAttributes.headPose))
      console.log('Gender: ' + face.faceAttributes.gender)
      console.log('Age: ' + face.faceAttributes.age)
      console.log('Facial hair: ' + JSON.stringify(face.faceAttributes.facialHair))
      console.log('Glasses: ' + face.faceAttributes.glasses)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Emotion: ' + JSON.stringify(face.faceAttributes.emotion))
      console.log('Blur: ' + JSON.stringify(face.faceAttributes.blur))
      console.log('Exposure: ' + JSON.stringify(face.faceAttributes.exposure))
      console.log('Noise: ' + JSON.stringify(face.faceAttributes.noise))
      console.log('Makeup: ' + JSON.stringify(face.faceAttributes.makeup))
      console.log('Accessories: ' + JSON.stringify(face.faceAttributes.accessories))
      console.log('Hair: ' + JSON.stringify(face.faceAttributes.hair))
      console.log()
    });
}).catch(function (error) {
    console.log(error)
});
```

## <a name="save-and-run-the-script"></a>Uložení a spuštění skriptu

Po provedení změn otevřete příkazový řádek a spusťte soubor pomocí `node` příkazu.

```
node facedetection.js
```

Tady je kompletní data JSON z `response.data` . Příklad:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste napsali skript Node.js, který volá službu Azure Face k detekci ploch v obrázku a vrátí jejich atributy. Dále si Projděte referenční dokumentaci Face API, kde najdete další informace.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
