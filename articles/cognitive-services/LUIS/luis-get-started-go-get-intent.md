---
title: Analýza textu v přirozeném jazyce ve službě Language Understanding (LUIS) pomocí Go – Azure Cognitive Services | Microsoft Docs
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí Go odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace. Služba LUIS použije v koncovém bodě model veřejné aplikace k analýze smyslu textu v přirozeném jazyce, zjištění celkového záměru a extrakci dat, která jsou relevantní pro doménu subjektu aplikace.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: ca1cba4416f0855a5838e8f3024be87351978ac8
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769213"
---
# <a name="quickstart-call-a-luis-endpoint-using-go"></a>Rychlý start: Volání koncového bodu služby LUIS pomocí Go

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

* Programovací jazyk [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analýza textu pomocí prohlížeče

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-go"></a>Analýza textu pomocí Go

Pomocí jazyka Go můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. 

1. Vytvořte nový soubor s názvem `endpoint.go`. Přidejte následující kód:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. V příkazovém řádku ve stejném adresáři, ve kterém jste soubor vytvořili, zadejte `go build endpoint.go` pro kompilaci souboru Go. Příkazový řádek nevrací žádné informace o úspěšném sestavení.

3. Aplikaci Go spusťte z příkazového řádku zadáním následujícího textu do příkazového řádku: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Nahraďte `<add-your-key>` hodnotou klíče.  
    
    Odpověď příkazového řádku je: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>Klíče služby LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Zavřete soubor Go a odeberte ho ze systému souborů. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-go-add-utterance.md)