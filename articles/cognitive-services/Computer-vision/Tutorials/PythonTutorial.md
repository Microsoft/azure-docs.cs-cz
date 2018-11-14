---
title: 'Kurz: Rozhraní API pro počítačové zpracování obrazu v Pythonu'
titlesuffix: Azure Cognitive Services
description: Přečtěte si, jak používat rozhraní API pro počítačové zpracování obrazu v Pythonu s poznámkovými bloky Jupyter. K vizualizaci výsledků můžete použít oblíbené knihovny.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 11/6/2018
ms.author: kefre
ms.openlocfilehash: 16054d19c1ce6f211ebd3e2f0bbc4d152a255dda
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276963"
---
# <a name="tutorial-computer-vision-api-python"></a>Kurz: Rozhraní API pro počítačové zpracování obrazu v Pythonu

V tomto kurzu se dozvíte, jak používat rozhraní API pro počítačové zpracování obrazu v Pythonu a jak vizualizovat výsledky s využitím oblíbených knihoven. V tomto kurzu použijete Jupyter. Informace o tom, jak začít používat interaktivní poznámkové bloky Jupyter, najdete v [dokumentaci k Jupyteru](http://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Požadavky

- [Python 2.7+ nebo 3.5+](https://www.python.org/downloads/)
- Nástroj [pip](https://pip.pypa.io/en/stable/installing/)
- Nainstalovaný [poznámkový blok Jupyter](http://jupyter.org/install)

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Otevření poznámkového bloku s kurzem v Jupyteru 

1. Přejděte do úložiště [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python) na GitHubu. 
2. Po kliknutí na zelené tlačítko můžete úložiště naklonovat nebo stáhnout. 
3. Otevřete příkazový řádek a přejděte do složky **Cognitive-Vision-Python\Jupyter Notebook**.
1. Spuštěním příkazu `pip install requests opencv-python numpy matplotlib` na příkazovém řádku se ujistěte, že máte všechny požadované knihovny.
1. Spuštěním příkazu `jupyter notebook` na příkazovém řádku spusťte Jupyter.
1. V okně Jupyteru klikněte na _Computer Vision API Example.ipynb_. Otevře se poznámkový blok s kurzem.

## <a name="run-the-tutorial"></a>Spuštění kurzu

K použití poznámkového bloku potřebujete klíč předplatného k rozhraní API pro počítačové zpracování obrazu. Navštivte [stránku předplatného](https://azure.microsoft.com/try/cognitive-services/) a zaregistrujte se. Na **přihlašovací** stránce se přihlaste pomocí svého účtu Microsoft. Pak si budete moct objednat předplatné a získat bezplatné klíče. Jakmile dokončíte registraci, vložte svůj klíč do části `Variables` v poznámkovém bloku (viz níže). Funguje primární i sekundární klíč. Nezapomeňte dát klíč do uvozovek jako řetězec.

Také budete muset ověřit, že se pole `_region` shoduje s oblastí odpovídající vašemu předplatnému.

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

Po spuštění tohoto kurzu budete moct přidat obrázky k analýze z adresy URL i z místního úložiště. Skript zobrazí obrázky a informace o analýze v poznámkovém bloku.