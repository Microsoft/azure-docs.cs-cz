---
title: Provádění operací image – Python
titlesuffix: Azure Cognitive Services
description: Přečtěte si, jak používat rozhraní API pro počítačové zpracování obrazu v Pythonu s poznámkovými bloky Jupyter. K vizualizaci výsledků můžete použít oblíbené knihovny.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604153"
---
# <a name="computer-vision-api-jupyter-notebook"></a>Poznámkový blok Jupyter rozhraní API pro zpracování obrazu počítače

Tento průvodce vám ukáže, jak používat rozhraní API pro počítačové zpracování obrazu v Pythonu a tom, jak vizualizovat výsledky pomocí oblíbených knihoven. V tomto kurzu použijete Jupyter. Zjistěte, jak začít pracovat s interaktivní poznámkové bloky Jupyter, najdete v tématu [Jupyter dokumentaci](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Požadavky

- [Python 2.7+ nebo 3.5+](https://www.python.org/downloads/)
- Nástroj [pip](https://pip.pypa.io/en/stable/installing/)
- Nainstalovaný [poznámkový blok Jupyter](https://jupyter.org/install)

## <a name="open-the-notebook-in-jupyter"></a>Otevřete Poznámkový blok v Jupyter 

1. Přejděte do úložiště [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python) na GitHubu. 
2. Po kliknutí na zelené tlačítko můžete úložiště naklonovat nebo stáhnout. 
3. Otevřete příkazový řádek a přejděte do složky **Cognitive-Vision-Python\Jupyter Notebook**.
1. Spuštěním příkazu `pip install requests opencv-python numpy matplotlib` na příkazovém řádku se ujistěte, že máte všechny požadované knihovny.
1. Spuštěním příkazu `jupyter notebook` na příkazovém řádku spusťte Jupyter.
1. V okně Jupyteru klikněte na _Computer Vision API Example.ipynb_. Otevře se poznámkový blok s kurzem.

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

K použití poznámkového bloku potřebujete klíč předplatného k rozhraní API pro počítačové zpracování obrazu. Navštivte [stránku předplatného](https://azure.microsoft.com/try/cognitive-services/) a zaregistrujte se. Na **přihlašovací** stránce se přihlaste pomocí svého účtu Microsoft. Pak si budete moct objednat předplatné a získat bezplatné klíče. Jakmile dokončíte registraci, vložte svůj klíč do části `Variables` v poznámkovém bloku (viz níže). Funguje primární i sekundární klíč. Nezapomeňte dát klíč do uvozovek jako řetězec.

Také budete muset ověřit, že se pole `_region` shoduje s oblastí odpovídající vašemu předplatnému.

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

Po spuštění tohoto kurzu budete moct přidat obrázky k analýze z adresy URL i z místního úložiště. Skript zobrazí obrázky a informace o analýze v poznámkovém bloku.
