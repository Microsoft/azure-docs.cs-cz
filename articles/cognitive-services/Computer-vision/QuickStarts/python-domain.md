---
title: 'Rychlý Start: obsah specifický pro doménu – REST, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete doménové modely k identifikaci celebrit a památek na obrázku pomocí rozhraní API pro počítačové zpracování obrazu a Pythonu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8aa49b044abcbd65a67a854a9da85cbc1471cda6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404428"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Rychlý start: Použití doménového modelu pomocí rozhraní REST API a Pythonu v počítačovém zpracování obrazu

V tomto rychlém startu použijete doménový model k identifikaci orientačních bodů nebo volitelně celebrit na vzdáleně uloženou Image pomocí REST API Počítačové zpracování obrazu. K rozpoznávání obsahu na obrázku pomocí metody [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) můžete využít model specifický pro doménu.

Tento rychlý start můžete spustit jako podrobný návod pomocí Jupyter Notebooku na webu [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Vazba](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Pokud chcete spustit tuto ukázku v místním prostředí, musíte mít nainstalovaný jazyk [Python](https://www.python.org/downloads/).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s `COMPUTER_VISION_SUBSCRIPTION_KEY` názvem `COMPUTER_VISION_ENDPOINT`a v uvedeném pořadí.

## <a name="create-and-run-the-landmarks-sample"></a>Vytvoření a spuštění ukázky s památkami

Pokud chcete vytvořit a spustit ukázku s památkami, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Volitelně můžete hodnotu `image_url` nahradit adresou URL jiného obrázku, na kterém chcete detekovat památky.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-landmarks.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-landmarks.py`.

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

landmark_analyze_url = endpoint + "vision/v2.1/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
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

## <a name="examine-the-response-for-the-landmarks-sample"></a>Prozkoumání odpovědi pro ukázku s památkami

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková webová stránka provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

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

## <a name="create-and-run-the-celebrities-sample"></a>Vytvoření a spuštění ukázky s celebritami

Pokud chcete vytvořit a spustit ukázku s památkami, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Proveďte v kódu na příslušných místech následující změny:
    1. Hodnotu `subscription_key` nahraďte klíčem předplatného.
    1. Hodnotu `vision_base_url` nahraďte adresou URL koncového bodu prostředku počítačového zpracování obrazu z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Volitelně můžete hodnotu `image_url` nahradit adresou URL jiného obrázku, na kterém chcete detekovat celebrity.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-celebrities.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
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

## <a name="examine-the-response-for-the-celebrities-sample"></a>Prozkoumání odpovědi pro ukázku s celebritami

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková webová stránka provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:


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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tyto ukázky nepotřebujete, odstraňte soubory s oběma ukázkami.

## <a name="next-steps"></a>Další kroky

Dále Prozkoumejte aplikaci Python, která používá Počítačové zpracování obrazu k provádění optického rozpoznávání znaků (OCR); vytvořit miniatury s inteligentním oříznutím; a k detekci, kategorizaci, označení a popisu vizuálních funkcí v obrázcích.

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)

* Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
