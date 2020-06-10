---
title: 'Rychlý start: Vygenerování miniatury – REST, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a Pythonu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 762688aaf21f88ef1404bc09be32cf4129bfd0db
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608838"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Rychlý Start: vygenerování miniatury pomocí Počítačové zpracování obrazu REST API a Pythonu

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí REST API Počítačové zpracování obrazu. Pomocí metody [získat miniatury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c) můžete určit požadovanou výšku a šířku a počítačové zpracování obrazu používat inteligentní ořezávání k inteligentně identifikaci oblasti zájmu a generování souřadnic oříznutí na základě této oblasti.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/try/cognitive-services/), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a v `COMPUTER_VISION_ENDPOINT` uvedeném pořadí.
- Editor kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Chcete-li vytvořit a spustit ukázku, zkopírujte následující kód do editoru kódu. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Dále postupujte následovně:

1. Volitelné Nahraďte hodnotu `image_url` adresou URL vlastního obrázku.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-thumbnail.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí jako binární data, která představují data obrázku pro miniaturu. Ukázka by měla zobrazit tento obrázek. Pokud se požadavek nepovede, zobrazí se odpověď v okně příkazového řádku a měla by obsahovat kód chyby.

## <a name="run-in-jupyter-optional"></a>Spustit v Jupyter (volitelné)

Tento rychlý Start můžete volitelně spustit v kroku pomocí poznámkového bloku Jupyter na [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Vazba](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Další kroky

Dále Prozkoumejte aplikaci Python, která používá Počítačové zpracování obrazu k provádění optického rozpoznávání znaků (OCR); vytvořit miniatury s inteligentním oříznutím; a k detekci, kategorizaci, označení a popisu vizuálních funkcí v obrázcích.

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)

* Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).
