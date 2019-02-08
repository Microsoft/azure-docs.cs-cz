---
title: 'Kurz: Rozpoznávání emocí na tváří v obrázku – rozhraní API pro rozpoznávání Emocí, Python'
titlesuffix: Azure Cognitive Services
description: Pomocí poznámkového bloku Jupyter zjistíte, jak používat rozhraní API pro rozpoznávání emocí s Pythonem. K vizualizaci výsledků můžete použít oblíbené knihovny.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 3339d22c0568352df503be90c6eb094a963978e2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877673"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Kurz: Pomocí rozhraní API pro rozpoznávání Emocí Poznámkový blok Jupyter a Pythonu.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Pro usnadnění začátků s rozhraním API pro rozpoznávání emocí ukazuje níže uvedený poznámkový blok Jupyter, jak toto rozhraní API používat v Pythonu a jak vizualizovat výsledky s využitím několika oblíbených knihoven.

[Odkaz na poznámkový blok na GitHubu](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Použití poznámkového bloku Jupyter

Pokud chcete poznámkový blok používat interaktivně, budete ho muset naklonovat a spustit v Jupyteru. Další informace o tom, jak začít používat interaktivní poznámkové bloky Jupyter, najdete v pokynech na webu http://jupyter.readthedocs.org/en/latest/install.html.

K použití poznámkového bloku potřebujete klíč předplatného k rozhraní API pro rozpoznávání emocí. Navštivte [stránku předplatného](https://azure.microsoft.com/try/cognitive-services/) a zaregistrujte se. Na přihlašovací stránce se přihlaste pomocí svého účtu Microsoft. Pak si budete moct objednat předplatné a získat bezplatné klíče. Jakmile dokončíte registraci, vložte svůj klíč do oddílu proměnných, jak je vidět níže. Funguje primární i sekundární klíč.

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
