---
title: 'Rychlý start: Vytvoření miniatury - REST, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a Pythonu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c29ea46513624215421845b99bd8306b73a5a9ac
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309008"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Rychlý start: Generování miniatur pomocí rozhraní REST API a Python v počítačové zpracování obrazu

V tomto rychlém startu vygeneruje miniaturu z image pomocí rozhraní REST API pro počítačové zpracování obrazu. S [získat miniaturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metodu, můžete určit požadovanou výšku a šířku a pro počítačové zpracování obrazu používá inteligentní oříznutí inteligentně identifikovat oblasti zájmu a generovat oříznutí souřadnice založené na danou oblast.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).
- Editor kódu, jako [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Vytvoření a spuštění ukázky, zkopírujte následující kód do editoru kódu. 

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
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Potom postupujte takto:
1. Hodnotu `subscription_key` nahraďte klíčem předplatného.
1. Hodnotu `vision_base_url` nahraďte adresou URL koncového bodu prostředku počítačového zpracování obrazu z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
1. Volitelně můžete hodnotu `image_url` nahradit adresou URL jiného obrázku, pro který chcete vygenerovat miniaturu.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-thumbnail.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí jako binární data, která představuje data bitové kopie pro miniaturu. Ukázka by se zobrazit tuto bitovou kopii. Pokud požadavek selže, odpověď se zobrazí v okně příkazového řádku a může obsahovat kód chyby.

## <a name="run-in-jupyter-optional"></a>Spuštění v Jupyter (volitelné)

V tomto rychlém startu můžete případně spustit krok za krokem způsobem pomocí poznámkového bloku Jupyter na [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Další postup

Dále si můžete přečíst podrobnější informace o funkci generování miniatur.

> [!div class="nextstepaction"]
> [Generování miniatur](../concept-generating-thumbnails.md)
