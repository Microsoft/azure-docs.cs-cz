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
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 59e88ecb253bc6da803ddf34a4a02229b99e259d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981707"
---
# <a name="tutorial-computer-vision-api-python"></a>Kurz: Rozhraní API pro počítačové zpracování obrazu v Pythonu

V tomto kurzu se dozvíte, jak používat rozhraní API pro počítačové zpracování obrazu v Pythonu a jak používat k vizualizaci výsledků některé oblíbené knihovny. Ke spuštění tohoto kurzu použijte Jupyter. Informace o tom, jak začít používat interaktivní poznámkové bloky Jupyter, najdete v [dokumentaci k Jupyteru](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otevření poznámkového bloku s kurzem v Jupyteru 

1. Přejděte k [poznámkovému bloku s kurzem v GitHubu](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Po kliknutí na zelené tlačítko můžete kurz klonovat nebo stáhnout. 
3. Otevřete příkazový řádek a přejděte ke složce _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Na příkazovém řádku spusťte příkaz **jupyter notebook**. Tím spustíte Jupyter.
5. V okně Jupyteru klikněte na _Computer Vision API Example.ipynb_. Otevře se poznámkový blok s kurzem. 

### <a name="running-the-tutorial"></a>Spuštění kurzu

K použití poznámkového bloku potřebujete klíč předplatného k rozhraní API pro počítačové zpracování obrazu. Navštivte [stránku předplatného](https://azure.microsoft.com/try/cognitive-services/) a zaregistrujte se. Na přihlašovací stránce použijte svůj účet Microsoft, abyste se mohli přihlásit k odběru a získat bezplatné klíče. Jakmile dokončíte registraci, zkopírujte a vložte klíč do oddílu proměnných v poznámkovém bloku (viz níže). Funguje primární i sekundární klíč. Nezapomeňte dát klíč do uvozovek jako řetězec.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
