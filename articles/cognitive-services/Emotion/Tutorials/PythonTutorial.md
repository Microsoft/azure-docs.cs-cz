---
title: 'Kurz: Rozpoznávání emocí ve výrazech tváří na obrázku – rozhraní API pro rozpoznávání emocí, Python'
titlesuffix: Azure Cognitive Services
description: Pomocí poznámkového bloku Jupyter zjistíte, jak používat rozhraní API pro rozpoznávání emocí s Pythonem. K vizualizaci výsledků můžete použít oblíbené knihovny.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 31e346cd9a3f43f8181ebee4474ae6c9ee2cc6fc
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237853"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Kurz: Použití rozhraní API pro rozpoznávání emocí s poznámkovým blokem Jupyter a Pythonem

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
