---
title: 'Kurz: Detekce a orámování tváří na obrázku – rozhraní API pro rozpoznávání tváře, Python'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí rozhraní API pro rozpoznávání tváře v sadě Python SDK rozpoznat na obrázku lidské tváře.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cc3ac25d2196c0275b445503b79b9ac06a791d3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127733"
---
# <a name="tutorial-detect-and-frame-faces-with-the-face-api-and-python"></a>Kurz: Detekce a orámování tváří pomocí rozhraní API pro rozpoznávání tváře a Pythonu 

V tomto kurzu se naučíte vyvolat rozhraní API pro rozpoznávání tváře přes sadu Python SDK a detekovat tak v obrázku lidské tváře.

## <a name="prerequisites"></a>Požadavky

K použití tohoto kurzu budete potřebovat:

- Nainstalovat Python 2.7+ nebo Python 3.5+.
- Nainstalovat pip.
- Sadu Python SDK pro rozhraní API pro rozpoznávání tváře nainstalujete takto:

```bash
pip install cognitive_face
```

- Získejte [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/) služeb Microsoft Cognitive Services. V tomto kurzu můžete použít buď svůj primární, nebo sekundární klíč. (Abyste mohli použít jakékoli rozhraní API pro rozpoznávání tváře, musíte mít platný klíč předplatného.)

## <a name="detect-a-face-in-an-image"></a>Detekce tváře na obrázku

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Níže najdete příklad výsledku. Jde o `list` detekovaných tváří. Každá položka v tomto seznamu je instance `dict`, kde `faceId` je jedinečné ID detekované tváře a `faceRectangle` popisuje pozici detekované tváře. Platnost ID tváře vyprší za 24 hodin.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Nakreslení obdélníků kolem tváří

Pomocí souřadnic JSON, které jste získali z předchozího příkazu, můžete nakreslením obdélníků do obrázku vizuálně znázornit jednotlivé tváře. Budete potřebovat příkaz `pip install Pillow`, abyste mohli použít modul zpracování obrazu `PIL`.  Na začátek souboru přidejte následující kód:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Za příkaz `print(faces)` pak do skriptu přidejte následující kód:

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name="further-exploration"></a>Další zkoumání

Abyste mohli rozhraní API pro rozpoznávání tváře dále prozkoumat, obsahuje tento kurz ukázkové grafické uživatelské rozhraní. Před jeho spuštěním napřed nainstalujte [wxPython](https://wxpython.org/pages/downloads/) a pak spusťte následující příkazy.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a>Souhrn

V tomto kurzu jste se naučili základní postup používání rozhraní API pro rozpoznávání tváře vyvoláním přes sadu Python SDK. Další podrobnosti o rozhraní API najdete v návodech a [referenčních informacích k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Související témata

- [Začínáme s rozhraním API pro rozpoznávání tváře v jazyce CSharp](FaceAPIinCSharpTutorial.md)
- [Začínáme s rozhraním API pro rozpoznávání tváře v Javě pro Android](FaceAPIinJavaForAndroidTutorial.md)
