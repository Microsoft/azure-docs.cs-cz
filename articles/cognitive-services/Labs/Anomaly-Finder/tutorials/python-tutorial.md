---
title: 'Kurz: Detekce anomálií, Python'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte notebook Pythonu, který používá rozhraní API pro detekci anomálií. Odešlete původní datové body do rozhraní API a získejte očekávanou hodnotu a body anomálie.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2df1e2c261069473a02b40e4a7e45895eadea42d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219392"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Kurz: Detekce anomálií pomocí aplikace v Pythonu

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

V tomto kurzu se dozvíte, jak používat rozhraní API pro detekci anomálií v Pythonu a jak používat k vizualizaci výsledků oblíbené knihovny. Použijte u tohoto kurzu Jupyter a zkuste vlastní data se svým klíčem předplatného. Další informace o tom, jak začít používat interaktivní notebooky Jupyteru, najdete v tématu [Dokumentace k Jupyteru](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Předplaťte si službu detekce anomálií a získejte klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Stáhněte si ukázkový kód

1. Přejděte k [poznámkovému bloku s kurzem v GitHubu](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Po kliknutí na zelené tlačítko můžete kurz klonovat nebo stáhnout. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otevření notebooku s kurzem v Jupyteru

1. Otevřete příkazový řádek a přejděte do složky python-sample.
2. Na příkazovém řádku spusťte příkaz Jupyter notebook, čímž spustíte Jupyter.
3. V okně Jupyteru klikněte na položku <em>Anomaly Detection API Example.ipynb</em>. Otevře se notebook s kurzem.   

## <a name="running-the-tutorial"></a>Spuštění kurzu

K použití notebooku potřebujete klíč předplatného k rozhraní API pro detekci anomálií. Navštivte stránku předplatného a zaregistrujte se. Na přihlašovací stránce se přihlaste pomocí svého účtu Microsoft. Pak si budete moct objednat předplatné a získat klíče. Jakmile dokončíte registraci, zkopírujte a vložte klíč do oddílu proměnných v notebooku (viz níže). Funguje primární i sekundární klíč. Nezapomeňte dát klíč do uvozovek jako řetězec.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
