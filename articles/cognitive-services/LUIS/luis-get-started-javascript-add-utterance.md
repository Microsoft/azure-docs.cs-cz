---
title: Rychlý start pro JavaScript – změna modelu a trénování aplikace LUIS
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte, jak volat aplikaci LUIS pomocí JavaScriptu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 38e25b0634b53f4fcc0507091e78ab49b29c8d38
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033392"
---
# <a name="quickstart-change-model-using-javascript"></a>Rychlý start: Změna modelu pomocí JavaScriptu

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Vytvoření kódu rychlého startu

Vytvořte soubor `add-utterances.html` a přidejte do něj následující kód:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Spuštění kódu

1. Otevřete soubor v prohlížeči.

2. Přidejte ID vytváření obsahu služby LUIS, ID aplikace LUIS.

3. Upravte **pole promluv**, které se mají přidat do vaší aplikace. Promluvy jsou uložené v proměnné utteranceJSON. Změňte tyto hodnoty pro vaši vlastní doménu a potřeby promluv. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Vyberte tlačítko `Upload utterance`. Pod tlačítky se zobrazí výsledky ze služby LUIS.

5. Vyberte tlačítko `Train model`, aby se vaše aplikace natrénovala s použitím těchto nových promluv.

6. Výběrem tlačítka `Train Status` zobrazte stav trénování. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile tento rychlý start dokončíte, odeberte všechny soubory, které jste v něm vytvořili. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Integrace služby LUIS s chatbotem](luis-csharp-tutorial-build-bot-framework-sample.md)
