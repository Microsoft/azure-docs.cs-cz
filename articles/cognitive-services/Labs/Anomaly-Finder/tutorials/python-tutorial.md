---
title: Detekce anomálií aplikace Python - kognitivní služby Microsoft | Microsoft Docs
description: Prozkoumejte Python Poznámkový blok, který používá rozhraní API detekce anomálií v kognitivní služby společnosti Microsoft. Odesílat původní datové body k rozhraní API a očekávaná hodnota a body anomálií.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343216"
---
# <a name="anomaly-detection-python-application"></a>Aplikace Python detekce anomálií

Tento kurz ukazuje, jak používat rozhraní API detekce anomálií v Pythonu a postup vizualizovat výsledky pomocí Oblíbené knihovny. Pomocí Jupyter ke spuštění tohoto kurzu a zkusit svoje vlastní data s svůj klíč předplatného. Zjistěte, jak začít pracovat s interaktivní poznámkové bloky Jupyter, najdete v tématu [Jupyter dokumentaci](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlášení k odběru detekce anomálií a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Stáhněte si ukázkový kód

1. Přejděte na [kurz poznámkového bloku v Githubu](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kliknutím na zelené tlačítko klonovat nebo stáhnout kurz. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otevření kurzu poznámkového bloku v Jupyter

1. Otevřete příkazový řádek a přejděte do složky python-sample.
2. Poznámkového bloku Jupyter příkaz spusťte z příkazového řádku, čímž se spustí Jupyter.
3. V okně Jupyter klikněte na <em>Example.ipynb rozhraní API detekce anomálií</em> otevřete kurz poznámkového bloku.   

## <a name="running-the-tutorial"></a>Spuštění tohoto kurzu

Pokud chcete použít tento poznámkový blok, budete potřebovat klíč předplatného pro rozhraní API detekce anomálií. Navštivte stránku předplatné a zaregistrujte. Na stránce "Přihlášení" přihlášení pomocí účtu Microsoft a bude moci přihlásit a získat klíče. Po dokončení procesu registrace, vložte klíč do části proměnné poznámkového bloku (uvedeny níže). Primární nebo sekundární klíč funguje. Ujistěte se, že uzavřete klíč v uvozovkách, aby řetězec.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
