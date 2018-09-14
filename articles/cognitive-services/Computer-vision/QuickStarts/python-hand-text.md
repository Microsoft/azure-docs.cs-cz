---
title: Rychlý start pro počítačové zpracování obrazu s rukou psaným textem v Pythonu | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu extrahujete rukou psaný text z obrázku pomocí počítačového zpracování obrazu s Pythonem v Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 43b541daf8632af7fb8111886b53981c4c646772
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769244"
---
# <a name="quickstart-extract-handwritten-text---rest-python"></a>Rychlý start: Extrakce rukou psaného textu – REST, Python

V tomto rychlém startu extrahujete rukou psaný text z obrázku pomocí počítačového zpracování obrazu.

Tento rychlý start můžete spustit jako podrobný návod pomocí Jupyter Notebooku na webu [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="extract-handwritten-text"></a>Extrakce rukou psaného textu

Pomocí metod pro [rozpoznávání textu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) a [získání výsledku operace rozpoznávání textu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) můžete zjistit rukou psaný text v obrázku a extrahovat rozpoznané znaky do strojově použitelného proudu znaků.

Pokud chcete spustit ukázku, postupujte takto:

1. Následující kód zkopírujte do nového souboru pythonového skriptu.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `vision_base_url` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete hodnotu `image_url` změnit na jiný obrázek.
1. Spusťte skript.

Následující kód používá knihovnu `requests` Pythonu k volání rozhraní API pro analýzu obrázku počítačového zpracování obrazu. Výsledky vrátí jako objekt JSON. Klíč rozhraní API se předává prostřednictvím slovníku `headers`.

## <a name="recognize-text-request"></a>Žádost Recognize Text

```python
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
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
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

text_recognition_url = vision_base_url + "recognizeText"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" + \
    "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
# Note: The request parameter changed for APIv2.
# For APIv1, it is 'handwriting': 'true'.
params  = {'mode': 'Handwritten'}
data    = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Extracting handwritten text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    time.sleep(1)
    if ("recognitionResult" in analysis):
        poll= False 
    if ("status" in analysis and analysis['status'] == 'Failed'):
        poll= False

polygons=[]
if ("recognitionResult" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
        for line in analysis["recognitionResult"]["lines"]]

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(15, 15))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
        for i in range(0, len(polygon[0]), 2)]
    text     = polygon[1]
    patch    = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
_ = plt.axis("off")
```

## <a name="recognize-text-response"></a>Odpověď metody Recognize Text

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
{
  "status": "Succeeded",
  "recognitionResult": {
    "lines": [
      {
        "boundingBox": [
          2,
          52,
          65,
          46,
          69,
          89,
          7,
          95
        ],
        "text": "dog",
        "words": [
          {
            "boundingBox": [
              0,
              59,
              63,
              43,
              77,
              86,
              3,
              102
            ],
            "text": "dog"
          }
        ]
      },
      {
        "boundingBox": [
          6,
          2,
          771,
          13,
          770,
          75,
          5,
          64
        ],
        "text": "The quick brown fox jumps over the lazy",
        "words": [
          {
            "boundingBox": [
              0,
              4,
              92,
              5,
              77,
              71,
              0,
              71
            ],
            "text": "The"
          },
          {
            "boundingBox": [
              74,
              4,
              189,
              5,
              174,
              72,
              60,
              71
            ],
            "text": "quick"
          },
          {
            "boundingBox": [
              176,
              5,
              321,
              6,
              306,
              73,
              161,
              72
            ],
            "text": "brown"
          },
          {
            "boundingBox": [
              308,
              6,
              387,
              6,
              372,
              73,
              293,
              73
            ],
            "text": "fox"
          },
          {
            "boundingBox": [
              382,
              6,
              506,
              7,
              491,
              74,
              368,
              73
            ],
            "text": "jumps"
          },
          {
            "boundingBox": [
              492,
              7,
              607,
              8,
              592,
              75,
              478,
              74
            ],
            "text": "over"
          },
          {
            "boundingBox": [
              589,
              8,
              673,
              8,
              658,
              75,
              575,
              75
            ],
            "text": "the"
          },
          {
            "boundingBox": [
              660,
              8,
              783,
              9,
              768,
              76,
              645,
              75
            ],
            "text": "lazy"
          }
        ]
      },
      {
        "boundingBox": [
          2,
          84,
          783,
          96,
          782,
          154,
          1,
          148
        ],
        "text": "Pack my box with five dozen liquor jugs",
        "words": [
          {
            "boundingBox": [
              0,
              86,
              94,
              87,
              72,
              151,
              0,
              149
            ],
            "text": "Pack"
          },
          {
            "boundingBox": [
              76,
              87,
              164,
              88,
              142,
              152,
              54,
              150
            ],
            "text": "my"
          },
          {
            "boundingBox": [
              155,
              88,
              243,
              89,
              222,
              152,
              134,
              151
            ],
            "text": "box"
          },
          {
            "boundingBox": [
              226,
              89,
              344,
              90,
              323,
              154,
              204,
              152
            ],
            "text": "with"
          },
          {
            "boundingBox": [
              336,
              90,
              432,
              91,
              411,
              154,
              314,
              154
            ],
            "text": "five"
          },
          {
            "boundingBox": [
              419,
              91,
              538,
              92,
              516,
              154,
              398,
              154
            ],
            "text": "dozen"
          },
          {
            "boundingBox": [
              547,
              92,
              701,
              94,
              679,
              154,
              525,
              154
            ],
            "text": "liquor"
          },
          {
            "boundingBox": [
              696,
              94,
              800,
              95,
              780,
              154,
              675,
              154
            ],
            "text": "jugs"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v Pythonu používající počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytváření chytře oříznutých miniatur, zjišťování, kategorizaci, označení a popis vizuálních prvků, včetně obličejů, v obrázku. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)
