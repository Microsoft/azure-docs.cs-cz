---
title: Rychlý start pro Python – předpověď záměru – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte, jak volat aplikaci LUIS pomocí Pythonu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e560aeffecf63f63966a49053e0f79d012b4a0a3
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038268"
---
# <a name="quickstart-get-intent-using-python"></a>Rychlý start: Získání záměru pomocí Pythonu
V tomto rychlém startu budete do koncového bodu služby LUIS předávat promluvy a získávat zpět záměr a entity.

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

* [Python 3.6](https://www.python.org/downloads/) nebo novější.
* [Visual Studio Code](https://code.visualstudio.com/)

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Získání záměru prostřednictvím kódu programu

Pomocí Pythonu můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku.

1. Zkopírujte jeden z následujících fragmentů do souboru s názvem `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Nahraďte hodnotu v poli `Ocp-Apim-Subscription-Key` klíčem vašeho koncového bodu LUIS.

3. Nainstalujte závislosti pomocí `pip install requests`.

4. Spusťte skript pomocí `python ./quickstart-call-endpoint.py`. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

## <a name="luis-keys"></a>Klíče služby LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Odstraňte soubor Pythonu. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-python-add-utterance.md)