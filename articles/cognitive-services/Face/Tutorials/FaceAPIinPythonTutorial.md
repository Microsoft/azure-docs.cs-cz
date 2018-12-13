---
title: 'Rychlý start: Zjišťování a rámečku tváří v obrázku pomocí sady Python SDK'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchý skript Pythonu, který se používá ke zjišťování a snímků tváří v bitové kopii vzdálené rozhraní API pro rozpoznávání tváře.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: sbowles
ms.openlocfilehash: 1e35d650f6fc99bff5bf49e517e2b38fcdc58dde
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076997"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Rychlý start: Vytvoření skriptu Pythonu, který zjišťuje a rámce čelí v obrázku

V tomto rychlém startu vytvoříte jednoduchý skript Pythonu, který používá rozhraní API pro rozpoznávání tváře Azure pomocí sady Python SDK k detekci lidských tváří na vzdálené bitové kopie. Aplikace zobrazí vybrané bitové kopie a nakreslí rámeček kolek každou zjištěnou plochu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- [Python 2.7+ nebo 3.5+](https://www.python.org/downloads/)
- Nástroj [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-face-sdk"></a>Získání sady SDK pro rozpoznávání tváře

Nainstalujte Python SDK pro rozpoznávání tváře otevřením příkazového řádku a spuštěním následujícího příkazu:

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří v obrázku

Vytvořit nový skript Pythonu s názvem _FaceQuickstart.py_ a přidejte následující kód. Toto je základní funkce rozpoznávání tváře. Budete muset nahradit `<Subscription Key>` s hodnotou klíče. Také může být nutné změnit hodnotu `BASE_URL` použít identifikátor oblasti správná pro váš klíč (najdete v článku [dokumenty k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) seznam všechny koncové body oblasti). Bezplatné předplatné zkušební verze klíče jsou generovány v **westus** oblasti. Volitelně můžete nastavit `img_url` na adresu URL žádné image chcete použít.

Skript se rozpoznávání tváří voláním **cognitive_face.face.detect** metoda, která zabalí [rozpoznat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní REST API a vrátí seznam tváří.

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

### <a name="try-the-app"></a>Zkuste aplikaci

Spusťte aplikaci příkazem `python FaceQuickstart.py`. Text odpovědi by měla získat v okně konzoly podobný tomuto:

```shell
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

Toto je seznam zjištěných tváří. Každá položka v seznamu je **dict** instance where `faceId` je jedinečné ID pro zjištěné rozpoznávání tváře a `faceRectangle` popisuje pozice zjištěné rozpoznávání tváře. 

> [!NOTE]
> Face ID platnost vyprší po uplynutí 24 hodin je potřeba explicitně ukládání dat pro rozpoznávání tváře, pokud chcete zachovat dlouhodobé.

## <a name="draw-face-rectangles"></a>Kreslení obdélníků pro rozpoznávání tváře

Pomocí souřadnic, které jste získali z předchozího příkazu, lze nakreslit obdélníky na vizuálně znázornit každou tvář na obrázku. Budete muset nainstalovat Poduškový (`pip install pillow`) chcete použít modul Poduškový bitové kopie. V horní části *FaceQuickstart.py*, přidejte následující kód:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

V dolní části skriptu přidejte následující kód. Vytvoří jednoduchou funkci k analýze souřadnic obdélník a používá Poduškový kreslení obdélníků v původní bitové kopie. Potom zobrazí obrázek ve výchozí prohlížeč obrázků.

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

## <a name="run-the-app"></a>Spuštění aplikace

Můžete být vyzváni k nejdřív vyberte výchozí prohlížeč obrázků. Potom byste měli vidět image takto. Také byste měli vidět data obdélník vytištěn v okně konzoly.

![Mladé ženu s červeným rámečkem vykreslen kolem typ písma](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu se naučili základní postup pro používání Python SDK pro rozhraní API pro rozpoznávání tváře a vytvořil skript k detekci a snímků tváří v obrázku. Pomocí sady Python SDK v složitější příklad dále prozkoumejte. Přejděte k ukázce Cognitive Python pro rozpoznávání tváře na Githubu, naklonovat ho do složky vašeho projektu a postupujte podle pokynů _README.md_ souboru.

> [!div class="nextstepaction"]
> [Ukázka cognitive Pythonu pro rozpoznávání tváře](https://github.com/Microsoft/Cognitive-Face-Python)