---
title: Rychlý start rozpoznávání emocí úrovně rozhraní API Python | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít pracovat s Pythonem v kognitivní služby pomocí rozhraní API pro rozpoznávání emocí úrovně.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/05/2018
ms.author: anroth
ms.openlocfilehash: ff1f6b2ddc872d0ee63d9885b04b1f007bc86e33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342447"
---
# <a name="emotion-api-python-quickstart"></a>Rychlý start rozpoznávání emocí úrovně rozhraní API Python

> [!IMPORTANT]
> Video Preview rozhraní API skončila 30 říjen 2017. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Tento názorný postup obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [rozpoznávání emocí úrovně rozhraní API rozpoznat metoda](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s Pythonem rozpoznat emoce vyjádřená jeden nebo více osob v obraze. 

V tomto příkladu můžete spustit jako poznámkového bloku Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění vazač oznámení: [ ![vazače](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Požadavek
Získat klíč bezplatné předplatné [sem](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>Spuštění Průvodce
Chcete-li pokračovat v tomto průvodci, nahraďte `subscription_key` s klíčem rozhraní API, který jste dříve získali.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, že adresa URL služby odpovídá oblast, kterou jste použili při nastavování klíč rozhraní API. Pokud používáte zkušební klíč, není nutné žádné změny.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Tento návod používá Image, které jsou uložené na disku. Můžete také použít bitové kopie, které jsou k dispozici prostřednictvím veřejně přístupné adresy URL. Další informace najdete v tématu [dokumentace k REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Vzhledem k tomu, že data bitové kopie se předá jako část obsahu žádosti, Všimněte si, že je nutné nastavit `Content-Type` hlavičky k `application/octet-stream`. Pokud jsou předávání v bitovou kopii prostřednictvím adresy URL, nezapomeňte nastavit hlavičku na:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
Vytvořte slovník obsahující adresu URL:
```python
data = {'url': image_url}
```
a předejte to `requests` pomocí knihovny:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Nejdřív stáhnout několik ukázkových bitové kopie z [rozhraní API pro rozpoznávání emocí úrovně](https://azure.microsoft.com/services/cognitive-services/emotion/) lokality.


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



Vrácený objekt JSON obsahuje ohraničující políčka řezy, které byly rozpoznány společně s zjištěné emoce. Každý rozpoznávání emocí úrovně je přidružen skóre mezi 0 a 1, kde je vyšší skóre lépe popisuje rozpoznávání emocí úrovně než nižší skóre. 

Následující řádky kódu zjištěné emoce na tyto řezy v pomocí bitové kopie `matplotlib` knihovny. Pro lepší přehlednost jsou zobrazeny pouze nejvyšší tři emoce.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

`annotate_image` Ukazuje další funkce slouží k překrytí emoce nad souborem bitové kopie základě její cesty v systému souborů. Je založena na kód pro volání do rozhraní API rozpoznávání emocí úrovně uvedena výše.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

Nakonec `annotate_image` funkce lze volat na soubor obrázku, jak je znázorněno na následujícím řádku:


```python
annotate_image("images/emotion_2.jpg")
```
