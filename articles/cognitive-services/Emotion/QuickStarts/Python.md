---
title: 'Rychlý start: Rozpoznávání emocí na tváří v obrázku – rozhraní API pro rozpoznávání Emocí, Python'
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí pomocí Pythonu.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: cb22e627c8da6a5a869458980b3b98ce7f287465
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869972"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvářejte aplikace pro rozpoznávání emocí na tváří v obrázku.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Tento návod obsahuje informace a vzorové kódy, které vám pomůžou rychle začít používat [metodu Recognize rozhraní API pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s Pythonem k rozpoznávání emocí vyjadřovaných jedním člověkem nebo několika lidmi na obrázku.

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu: ![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Požadavek
[Tady](https://azure.microsoft.com/try/cognitive-services/) získejte klíč svého bezplatného předplatného.

## <a name="running-the-walkthrough"></a>Spuštění návodu
Abyste mohli pokračovat v tomto návodu, nahraďte `subscription_key` klíčem rozhraní API, který jste získali dříve.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, že adresa URL služby odpovídá oblasti, kterou jste použili při nastavování klíče rozhraní API. Pokud používáte zkušební klíč, není potřeba provádět jakékoli změny.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

V tomto návodu se používají obrázky uložené na disku. Můžete použít i obrázky, které jsou dostupné přes veřejně přístupnou adresu URL. Další informace najdete v [dokumentaci k rozhraní REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Vzhledem k tomu, že se data obrázků předávají v textu požadavku, mějte na paměti, že je potřeba nastavit hlavičku `Content-Type` na `application/octet-stream`. Pokud předáváte obrázek přes adresu URL, nezapomeňte tuto hlavičku nastavit na:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
Vytvořte slovník obsahující adresu URL:
```python
data = {'url': image_url}
```
Předejte tyto hodnoty knihovně `requests` pomocí následujícího příkazu:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Nejprve z webu [rozhraní API pro rozpoznávání emocí](https://azure.microsoft.com/services/cognitive-services/emotion/) stáhněte několik ukázkových obrázků.


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



Vrácený objekt JSON obsahuje ohraničující rámečky rozpoznaných tváří společně se zjištěnými emocemi. Ke každé emoci je přidružené skóre od 0 do 1, přičemž vyšší skóre značí spolehlivější rozpoznání emoce než nižší skóre.

Následující řádky kódu pomocí knihovny `matplotlib` označí zjištěné emoce ve výrazech tváří na obrázku. Pro lepší přehlednost se zobrazí pouze tři hlavní emoce.


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

Pomocí následující funkce `annotate_image` můžete obrázek překrýt emocemi – stačí, když funkci předáte cestu k tomuto souboru v systému souborů. Tato funkce vychází z výše uvedeného kódu pro volání rozhraní API pro rozpoznávání emocí.


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

Nakonec můžete funkci `annotate_image` zavolat pro soubor obrázku, jak je znázorněno na následujícím řádku:


```python
annotate_image("images/emotion_2.jpg")
```
