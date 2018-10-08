---
title: Rychlý start pro Ruby – předpověď záměru – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí Ruby odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace. Služba LUIS použije v koncovém bodě model veřejné aplikace k analýze smyslu textu v přirozeném jazyce, zjištění celkového záměru a extrakci dat, která jsou relevantní pro doménu subjektu aplikace.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: b8ef763a0b2379115150c88becc2ad43ba9cde89
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030927"
---
# <a name="quickstart-get-intent-using-ruby"></a>Rychlý start: Získání záměru pomocí Ruby

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

* Programovací jazyk [Ruby](https://www.ruby-lang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

<a name="create-luis-subscription-key"></a>

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

Pomocí Ruby můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. 

1. Zkopírujte následující kód a uložte ho do souboru s názvem `endpoint-call.rb`:

   [!code-ruby[Ruby code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/ruby/endpoint-call.rb)]

2. Nahraďte `"YOUR-KEY"` klíčem koncového bodu.

3. Spusťte aplikaci Ruby v příkazovém řádku pomocí `ruby endpoint-call.rb`. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ```
    LUIS query: turn on the left light
    
    Request URI: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn+on+the+left+light&timezoneOffset=0&verbose=false&spellCheck=false&staging=false
    
    JSON Response:
    
    {
      "query": "turn on the left light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.933549
      },
      "entities": [
        {
          "entity": "left",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 15,
          "score": 0.540835142
        }
      ]
    }
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor Ruby.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-ruby-add-utterance.md)