---
title: 'Rychlý start: Rozpoznávání tváří na obrázku – rozhraní API pro rozpoznávání tváře, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete rozpoznávat tváře na obrázku pomocí rozhraní API pro rozpoznávání tváře s Pythonem.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: pafarley
ms.openlocfilehash: 581c2a7d4508833647d4dbb9861000fddd75cde8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339577"
---
# <a name="quickstart-detect-faces-in-an-image-using-python"></a>Rychlý start: Rozpoznávání tváří na obrázku pomocí Pythonu

V tomto rychlém startu budete pomocí služby Rozpoznávání tváře rozpoznávat lidské tváře na vzdáleném obrázku. Rozpoznané tváře se ohraničí obdélníky a překryjí pohlavím a věkem každé osoby. Pokud chcete použít místní obrázek, podívejte se na syntaxi v článku [Počítačové zpracování obrazu: Analýza místního obrázku pomocí Pythonu](../../Computer-vision/QuickStarts/python-disk.md).

Tento rychlý start můžete otevřít jako poznámkový blok Jupyter na webu [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

Ke spuštění této ukázky budete potřebovat klíč předplatného. Klíče bezplatného zkušebního předplatného můžete získat v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Pomocí metody [Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozpoznejte tváře na obrázku a vraťte atributy tváře, včetně:

* Face ID: jedinečná hodnota ID používaná v několika scénářích rozhraní API pro rozpoznávání tváře
* Obdélník tváře: umístění tváře na obrázku vlevo a nahoře a šířka a výška obličeje
* Rysy: soustava 27 bodů obličejových rysů odkazující na důležité polohy součástí obličeje.
* Atributy obličeje včetně věku, pohlaví, intenzity úsměvu, pozice hlavy a vousů.

Pokud chcete spustit ukázku, postupujte takto:

1. Následující kód zkopírujte do nového souboru pythonového skriptu.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `face_api_url` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete hodnotu `image_url` změnit na jiný obrázek.
1. Spusťte skript.

### <a name="face---detect-request"></a>Žádost Face - Detect

Následující kód používá knihovnu `requests` Pythonu k volání rozhraní API Face Detect. Výsledky vrátí jako objekt JSON. Klíč rozhraní API se předává prostřednictvím slovníku `headers`. Typy rozeznávaných funkcí se předávají prostřednictvím slovníku `params`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from matplotlib import patches
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
face_api_url = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect'

# Set image_url to the URL of an image that you want to analyze.
image_url = 'https://how-old.net/Images/faces2/main007.jpg'

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
    'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
}
data = {'url': image_url}
response = requests.post(face_api_url, params=params, headers=headers, json=data)
faces = response.json()

# Display the original image and overlay it with the face information.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.figure(figsize=(8, 8))
ax = plt.imshow(image, alpha=0.6)
for face in faces:
    fr = face["faceRectangle"]
    fa = face["faceAttributes"]
    origin = (fr["left"], fr["top"])
    p = patches.Rectangle(
        origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    plt.text(origin[0], origin[1], "%s, %d"%(fa["gender"].capitalize(), fa["age"]),
             fontsize=20, weight="bold", va="bottom")
_ = plt.axis("off")
```

### <a name="face---detect-response"></a>Odpověď metody Face - Detect

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
[
  {
    "faceId": "35102aa8-4263-4139-bfd6-185bb0f52d88",
    "faceRectangle": {
      "top": 208,
      "left": 228,
      "width": 91,
      "height": 91
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 4.3,
        "yaw": -0.3
      },
      "gender": "female",
      "age": 27,
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
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.65
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
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.5
          },
          {
            "color": "black",
            "confidence": 0.34
          },
          {
            "color": "red",
            "confidence": 0.32
          },
          {
            "color": "gray",
            "confidence": 0.14
          },
          {
            "color": "other",
            "confidence": 0.03
          }
        ]
      }
    }
  },
  {
    "faceId": "42502166-31bb-4ac8-81c0-a7adcb3b3e70",
    "faceRectangle": {
      "top": 109,
      "left": 125,
      "width": 79,
      "height": 79
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 1.7,
        "yaw": 2.1
      },
      "gender": "male",
      "age": 32,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.4
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.11
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.74
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.02,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.94
          },
          {
            "color": "red",
            "confidence": 0.76
          },
          {
            "color": "gray",
            "confidence": 0.2
          },
          {
            "color": "other",
            "confidence": 0.03
          },
          {
            "color": "black",
            "confidence": 0.01
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
