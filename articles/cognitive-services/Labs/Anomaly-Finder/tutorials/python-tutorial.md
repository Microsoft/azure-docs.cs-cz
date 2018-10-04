---
title: Detekce anomálií aplikace v Pythonu – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Prozkoumejte Poznámkový blok Python, který používá rozhraní API pro detekci anomálií v Microsoft Cognitive Services. Odeslat původní datové body k rozhraní API a získat očekávanou hodnotu a anomálií body.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 87cd9e976d231291ad13acecf188cfd668d692b6
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248223"
---
# <a name="anomaly-detection-python-application"></a>Aplikace v Pythonu detekce anomálií

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Tento kurz ukazuje, jak používat rozhraní API pro detekci anomálií v Pythonu a tom, jak vizualizovat výsledky pomocí oblíbených knihoven. Použití aplikace Jupyter ke spuštění tohoto kurzu a zkusit svoje vlastní data s klíči předplatného. Zjistěte, jak začít pracovat s interaktivní poznámkové bloky Jupyter, najdete v tématu [Jupyter dokumentaci](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlaste se k detekci anomálií odběru a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Stáhněte si ukázkový kód

1. Přejděte [výukový program Poznámkový blok v Githubu](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Klikněte na zelené tlačítko naklonujte nebo stáhněte si kurz. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otevírání výukový program Poznámkový blok v Jupyter

1. Otevřete příkazový řádek a přejděte do složky python-sample.
2. Spusťte Poznámkový blok Jupyter příkazu z příkazového řádku, který se spustí Jupyter.
3. V okně Jupyter, klikněte na <em>Example.ipynb API detekce anomálií</em> otevřete výukový program Poznámkový blok.   

## <a name="running-the-tutorial"></a>Spuštění výukového programu

Pokud chcete použít tento poznámkový blok, potřebujete klíč předplatného pro rozhraní API pro detekci anomálií. Navštivte stránku předplatné k registraci. Na stránce "Odebrat" přihlaste se pomocí svého účtu Microsoft a bude možné zaregistrovat a získávat klíče. Po dokončení procesu registrace, vložte svůj klíč do části proměnné poznámkového bloku (uvedeno níže). Primární nebo sekundární klíč funguje. Ujistěte se, že k uzavření klíč v uvozovkách, aby byl řetězec.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
