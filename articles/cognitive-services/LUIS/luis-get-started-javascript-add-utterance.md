---
title: Kurz přidávání promluv do aplikace LUIS pomocí JavaScriptu | Microsoft Docs
description: V tomto kurzu zjistíte, jak volat aplikaci LUIS pomocí JavaScriptu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265455"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Kurz: Přidávání promluv do aplikace pomocí JavaScriptu
V tomto kurzu napíšete program, který do záměru přidá promluvu pomocí rozhraní API pro vytváření v JavaScriptu.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření projektu konzoly v sadě Visual Studio 
> * Přidání metody pro volání rozhraní API služby LUIS za účelem přidání promluvy a trénování aplikace
> * Přidání souboru JSON s ukázkovými promluvami pro záměr BookFlight (Rezervovat let)
> * Spuštění konzoly a zobrazení stavu trénování pro promluvy

Další informace najdete v technické dokumentaci k rozhraním API pro [přidání ukázkové promluvy do záměru](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a [stav trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="prerequisites"></a>Požadavky
* [**Klíč pro vytváření**](luis-concept-keys.md#authoring-key) pro službu LUIS. 
* **ID stávající aplikace** LUIS a její **ID verze**. 
* Ve VSCode vytvořte nový soubor `add-utterances.html`.

> [!NOTE] 
> Kompletní soubor `add-utterances.html` je dostupný v [úložišti **LUIS-Samples** na GitHubu](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>Psaní kódu
Vytvořte soubor `add-utterances.html` a přidejte do něj následující kód:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Zobrazení v prohlížeči
1. Otevřete soubor v prohlížeči.

2. Přidejte své ID pro vytváření pro službu LUIS, ID vaší aplikace LUIS a změňte verzi, pokud není `0.1`.

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
Jakmile budete s kurzem hotovi, odeberte sadu Visual Studio a konzolovou aplikaci, pokud už je nepotřebujete. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Integrace služby LUIS s chatbotem](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website