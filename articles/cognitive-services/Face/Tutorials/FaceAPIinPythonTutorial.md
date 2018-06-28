---
title: Kurz vzhled Python rozhraní API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Naučte se používat rozhraní API setkávají s Python SDK k detekci lidského řezy v bitovou kopii v kognitivní služby.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 90d74d8df2ed59e6f3313ef7c620284d1022a667
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049107"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Začínáme s vzhled rozhraní API v kurzu Python

V tomto kurzu se dozvíte volat rozhraní API vzhled prostřednictvím Python SDK k detekci lidského tyto řezy v obraze.

## <a name="prerequisites"></a> Požadavky

Pokud chcete použít tento kurz, musíte provést následující akce:

- Nainstalujte aplikaci Python 2.7 + nebo Python 3.5 +.
- Nainstalujte pip.
- Nainstalujte Python SDK pro rozhraní API vzhled následujícím způsobem:

```bash
pip install cognitive_face
```

- Získání [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/) kognitivní služeb společnosti Microsoft. V tomto kurzu můžete použít primární nebo sekundární klíč. (Všimněte si, že použití jakéhokoli rozhraní API řez, musí mít klíč platné předplatné.)

## <a name="sdk-example"></a> Detekovat řez v obrázku

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

Níže je příklad výsledek. Je `list` zjištěné ploch. Jednotlivé položky v seznamu `dict` instance kde `faceId` je jedinečné ID pro zjištěné vzhled a `faceRectangle` popisuje pozici zjištěné písmo. ID vzhled vyprší za 24 hodin.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Kreslení obdélníků kolem řezy

Pomocí souřadnic json, které jste získali od předchozí příkaz, může kreslení obdélníků na bitovou kopii do vizuálně představují každý řez. Budete muset `pip install Pillow` používat `PIL` modulu pro vytváření bitových kopií.  Na začátek souboru přidejte následující:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Potom po `print(faces)`, zahrnout následující skript:

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

## <a name='further'></a> Další zkoumání

V tomto kurzu můžete dále prozkoumat rozhraní API řez, poskytuje ukázku grafickým uživatelským rozhraním. Chcete-li jej spustit, nejprve nainstalovat [wxPython](https://wxpython.org/pages/downloads/) spusťte následující příkazy.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> Souhrn

V tomto kurzu jste se naučili základní proces pomocí rozhraní API vzhled prostřednictvím volání Python SDK. Další informace o rozhraní API podrobnosti naleznete v postupy a [referenční dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Související témata

- [Začínáme s vzhled rozhraní API v CSharp](FaceAPIinCSharpTutorial.md)
- [Začínáme s vzhled rozhraní API v jazyce Java pro Android](FaceAPIinJavaForAndroidTutorial.md)
