---
title: Rychlý start k doménovému modelu s využitím počítačového zpracování obrazu s Pythonem | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu použijete doménové modely k identifikaci celebrit a památek na obrázku pomocí počítačového zpracování obrazu s Pythonem ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 357cab72c0a6c9a2254350c84cda91c366ac685a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769240"
---
# <a name="quickstart-use-a-domain-model---rest-python"></a>Rychlý start: Použití doménového modelu – REST, Python

V tomto rychlém startu použijete doménové modely k identifikaci celebrit a památek na obrázku pomocí počítačového zpracování obrazu.

Tento rychlý start můžete spustit jako podrobný návod pomocí Jupyter Notebooku na webu [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="identify-celebrities-and-landmarks"></a>Identifikace celebrit a památek

Pomocí [metody Recognize Domain Specific Content](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) můžete identifikovat konkrétní sadu objektů v obrázku. Momentálně jsou k dispozici dva modely specifické pro doménu: _celebrities_ a _landmarks_.

Pokud chcete spustit ukázku, postupujte takto:

1. Následující kód zkopírujte do nového souboru pythonového skriptu.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `vision_base_url` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete hodnotu `image_url` změnit na jiný obrázek.
1. Spusťte skript.

Následující kód používá knihovnu `requests` Pythonu k volání rozhraní API pro analýzu obrázku počítačového zpracování obrazu. Výsledky vrátí jako objekt JSON. Klíč rozhraní API se předává prostřednictvím slovníku `headers`. Model, který se má použít, se předává prostřednictvím slovníku `params`.

## <a name="landmark-identification"></a>Identifikace památky

### <a name="recognize-landmark-request"></a>Žádost Recognize Landmark

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
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

landmark_analyze_url = vision_base_url + "models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'landmarks'}
data    = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

### <a name="recognize-landmark-response"></a>Odpověď metody Recognize Landmark

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="celebrity-identification"></a>Identifikace celebrit

### <a name="recognize-celebrity-request"></a>Žádost Recognize Celebrity

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'celebrities'}
data    = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

### <a name="recognize-celebrity-response"></a>Odpověď metody Recognize Celebrity

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v Pythonu používající počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytváření chytře oříznutých miniatur, zjišťování, kategorizaci, označení a popis vizuálních prvků, včetně obličejů, v obrázku. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)
