---
title: Kurz vize API Python počítače | Microsoft Docs
description: Naučte se používat rozhraní API vize počítače s Pythonem pomocí Jupyter notebooks v kognitivní služby společnosti Microsoft. Vizualizace výsledky pomocí Oblíbené knihovny.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342696"
---
# <a name="computer-vision-api-python-tutorial"></a>Počítač vize API Python kurzu

Tento kurz ukazuje, jak používat rozhraní API vize počítače v Pythonu a jak vizualizovat výsledky použití některých oblíbených knihoven. Použijte Jupyter ke spuštění tohoto kurzu. Zjistěte, jak začít pracovat s interaktivní poznámkové bloky Jupyter, najdete v tématu: [Jupyter Documementation](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otevření kurzu poznámkového bloku v Jupyter 

1. Přejděte na [kurz poznámkového bloku v Githubu](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Kliknutím na zelené tlačítko klonovat nebo stáhnout kurz. 
3. Otevřete příkazový řádek a přejděte do složky _Cognitive vize Python master\Jupyter Poznámkový blok_. 
4. Spusťte příkaz **Poznámkový blok jupyter** z příkazového řádku. Tato akce spustí Jupyter.
5. V okně Jupyter klikněte na _počítače vize rozhraní API Example.ipynb_ otevřete kurz poznámkového bloku 

### <a name="running-the-tutorial"></a>Spuštění tohoto kurzu

Pokud chcete použít tento poznámkový blok, budete potřebovat klíč předplatného pro rozhraní API vize počítače. Přejděte [stránku odběru](https://azure.microsoft.com/try/cognitive-services/) k registraci. Na stránce "Přihlášení" přihlášení pomocí účtu Microsoft a bude moci přihlásit a získat volné klíče. Po dokončení procesu registrace, vložte klíč do části proměnné poznámkového bloku (uvedeny níže). Primární nebo sekundární klíč funguje. Ujistěte se, že uzavřete klíč v uvozovkách, aby řetězec.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
