---
title: 'Rychlý Start: analýza místního image – REST, Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu analyzujete místní Image pomocí rozhraní Počítačové zpracování obrazu APi pomocí Pythonu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b64711b73cf1b18636b569a21c2bc98610117cb8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176449"
---
# <a name="quickstart-analyze-a-local-image-using-the-computer-vision-rest-api-and-python"></a>Rychlý Start: analýza místní Image pomocí Počítačové zpracování obrazu REST API a Pythonu

V tomto rychlém startu analyzujete místně uloženou bitovou kopii pro extrakci vizuálních funkcí pomocí REST API Počítačové zpracování obrazu. Pomocí metody [analyzovat obrázek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální funkce založené na obsahu obrázku.

Tento rychlý Start můžete v kroku spustit pomocí poznámkového bloku Jupyter na [MyBinder](https://mybinder.org). Chcete-li spustit aplikaci Binder, vyberte následující tlačítko:

[@no__t – 1Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Pokud chcete spustit ukázku místně, musíte mít nainstalovaný [Python](https://www.python.org/downloads/) .
- Pro Počítačové zpracování obrazu musíte mít klíč předplatného. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` v uvedeném pořadí.
- Musíte mít nainstalované následující balíčky Pythonu. Pomocí [PIP](https://packaging.python.org/tutorials/installing-packages/) můžete nainstalovat balíčky Pythonu.
    - požadavky
    - [matplotlib](https://matplotlib.org/)
    - [pillow](https://python-pillow.org/)

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Chcete-li vytvořit a spustit ukázku, proveďte následující kroky:

1. Zkopírujte následující kód do textového editoru.
1. V případě potřeby nahraďte hodnotu `image_path` cestou a názvem souboru jiné image, kterou chcete analyzovat.
1. Uložte kód jako soubor s příponou `.py`. Například `analyze-local-image.py`.
1. Otevřete okno příkazového řádku.
1. Na příkazovém řádku použijte příkaz `python` ke spuštění ukázky. Například `python analyze-local-image.py`.

```python
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

analyze_url = endpoint + "vision/v2.1/analyze"

# Set image_path to the local path of an image that you want to analyze.
image_path = "C:/Documents/ImageToAnalyze.jpg"

# Read the image into a byte array
image_data = open(image_path, "rb").read()
headers = {'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-Type': 'application/octet-stream'}
params = {'visualFeatures': 'Categories,Description,Color'}
response = requests.post(
    analyze_url, headers=headers, params=params, data=image_data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(image_data))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="examine-the-response"></a>Projděte si odpověď.

Ve formátu JSON se vrátí úspěšná odpověď. Ukázková webová stránka analyzuje a zobrazuje úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v Pythonu, která používá Počítačové zpracování obrazu k provádění optického rozpoznávání znaků (OCR); vytvořit miniatury s inteligentním oříznutím; Navíc ke zjištění, kategorizaci, označení a popisu vizuálních funkcí, včetně obličeje, v obrázku. Chcete-li rychle experimentovat s rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [konzolu Open API Testing](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro počítačové zpracování obrazu Pythonu](../Tutorials/PythonTutorial.md)
