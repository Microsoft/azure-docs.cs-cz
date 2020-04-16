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
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 68a6504668b9f180a421fe20c2c89d73b87bcc35
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404350"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Úvodní příručka: Generování miniatury pomocí rozhraní API PRO ZPRACOVÁNÍ POČÍTAČE A Pythonu

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API REKONČNATÉHO Počítače. Pomocí metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) můžete určit požadovanou výšku a šířku a počítačové vidění používá inteligentní oříznutí k inteligentní identifikaci oblasti zájmu a generování souřadnic oříznutí na základě této oblasti.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/try/cognitive-services/) než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Můžete získat bezplatný zkušební klíč od [try cognitive services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili k odběru počítačového vidění a získali klíč. Potom [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec koncového `COMPUTER_VISION_SUBSCRIPTION_KEY` klíče `COMPUTER_VISION_ENDPOINT`a služby s názvem a , resp.
- Editor kódu, jako je [například Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Chcete-li vytvořit a spustit ukázku, zkopírujte následující kód do editoru kódu. 

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

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Dále postupujte takto:
1. Volitelně můžete hodnotu `image_url` nahradit adresou URL jiného obrázku, pro který chcete vygenerovat miniaturu.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-thumbnail.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď je vrácena jako binární data, která představují obrazová data pro miniaturu. Ukázka by měla zobrazit tento obrázek. Pokud se požadavek nezdaří, odpověď se zobrazí v okně příkazového řádku a měla by obsahovat kód chyby.

## <a name="run-in-jupyter-optional"></a>Běh v Jupyteru (nepovinné)

Volitelně můžete spustit tento rychlý start krok-za krokem pomocí poznámkového bloku Jupyter na [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Pořadače](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Další kroky

Dále prozkoumejte aplikaci Pythonu, která používá počítačové vidění k provádění optického rozpoznávání znaků (OCR); vytvářet miniatury s inteligentním oříznutím; a zjišťovat, kategorizovat, označovat a popisovat vizuální funkce v obrázcích.

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)

* Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
