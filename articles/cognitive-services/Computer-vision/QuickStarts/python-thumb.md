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
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9962874600e259a639c70b7b180e5fc2a940461f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401132"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Rychlý start: Generování miniatur pomocí rozhraní REST API a Python v počítačové zpracování obrazu

V tomto rychlém startu vygeneruje miniaturu z image pomocí rozhraní REST API pro počítačové zpracování obrazu. S [získat miniaturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metodu, můžete určit požadovanou výšku a šířku a pro počítačové zpracování obrazu používá inteligentní oříznutí inteligentně identifikovat oblasti zájmu a generovat oříznutí souřadnice založené na danou oblast.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Můžete získat bezplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k přihlášení pro počítačové zpracování obrazu a získejte klíč.
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
