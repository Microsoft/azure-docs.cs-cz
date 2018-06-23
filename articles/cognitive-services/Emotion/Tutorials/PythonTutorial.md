---
title: Kurz rozpoznávání emocí úrovně rozhraní API Python | Microsoft Docs
description: Naučte se používat rozhraní API služby kognitivní rozpoznávání emocí úrovně služeb s Pythonem pomocí poznámkového bloku Jupyter. Vizualizujte výsledky pomocí Oblíbené knihovny.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342703"
---
# <a name="emotion-api-using-python-tutorial"></a>Rozpoznávání emocí úrovně rozhraní API pomocí kurzu Python

> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Chcete-li snadné začít s rozhraním API pro rozpoznávání emocí úrovně, poznámkového bloku Jupyter vedou následující odkazy ukazuje, jak používat rozhraní API v Pythonu a postup vizualizovat výsledky použití některých oblíbených knihoven. 

[Odkaz na Poznámkový blok v Githubu](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Pomocí poznámkového bloku Jupyter

Do poznámkového bloku interaktivně používat, musíte ho klonovat a spustíte ho v Jupyter. Chcete-li zjistit, jak začít pracovat s interaktivní poznámkové bloky Jupyter, postupujte podle pokynů v http://jupyter.readthedocs.org/en/latest/install.html. 

Pokud chcete použít tento poznámkový blok, budete potřebovat klíč předplatného pro rozhraní API pro rozpoznávání emocí úrovně. Přejděte [stránku odběru](https://azure.microsoft.com/try/cognitive-services/) k registraci. Na stránce "Přihlášení" přihlášení pomocí účtu Microsoft a bude moci přihlásit a získat volné klíče. Po dokončení procesu registrace, vložte klíč do části proměnné vidíte níže. Primární nebo sekundární klíč funguje.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
