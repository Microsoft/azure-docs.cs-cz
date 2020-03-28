---
title: 'Kurz: Entita regulárního výrazu – LUIS'
titleSuffix: Azure Cognitive Services
description: Extrahujte konzistentně formátovaná data z utterance pomocí entity Regulární výraz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 0ca6f2a67e01e4c604c2dcc8f8eaa9ffe8bad045
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75381508"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Kurz: Získejte dobře formátovaná data z utterance
V tomto kurzu vytvořte entitu regulárních výrazů pro extrahování konzistentně formátovaných dat z utterance.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat aplikaci
> * Přidat záměr
> * Přidání entity regulárního výrazu
> * Trénování, publikování a dotazování aplikace získat extrahovaná data

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entity regulárního výrazu

Použijte entitu regulárního výrazu k vytažení dobře formátovaného textu z utterance. Když se záměr promluvy vždy určuje pomocí strojového učení, tento konkrétní typ entity se pomocí strojového učení nezískává. Dobrým použitím entity regulárního výrazu je libovolný text, který může být konzistentně reprezentován [regulárním výrazem](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Tento příklad používá _krátký kód_ pro odesílání textových zpráv. Tento krátký kód je 5 nebo 6místný číselný kód s předponou x `x\d{5,6}`a lze jej popsat regulárním výrazem .

Když přidáte entitu regulárního výrazu do aplikace LUIS, nemusíte [označovat](label-entity-example-utterance.md) text běžnou expresní entitou. Je použita pro všechny projevy ve všech záměrech.

## <a name="import-example-json-to-begin-app"></a>Import příkladu json pro spuštění aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Vytvořit záměr pro odesílání potvrzovacích textových zpráv

1. Vyberte **+ Vytvořit,** chcete-li vytvořit nový záměr pro klasifikaci záměru utterance pro odeslání potvrzovacího textu.

1. V automaticky otevíraném dialogovém okně zadejte `ConfirmationText` a pak vyberte **Done** (Hotovo).

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Poslat pizzu dodací lhůta x123432|
    |Txt x234567 pro čas|
    |x23987 pro oznámení|

    Chcete-li extrahovat entity získané počítačem, měli byste poskytnout příklady, které zahrnují entitu v různých projevech, ale s touto entitou nenabytou počítače, varianta není důležitá. Dokud se text bude shodovat s regulárním výrazem, bude extrahován.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Použití entity regulárního výrazu pro dobře formátovaná data
Vytvořte entitu regulárního výrazu tak, aby odpovídala textovému číslu. Tento regulární výraz odpovídá textu, ale ignoruje varianty případu a jazykové verze.

1. Na levém panelu vyberte **Entities** (Entity).

1. Na stránce se seznamem Entity vyberte **+ Vytvořit.**

1. V rozbalovacím dialogovém okně zadejte název nové entity `ConfirmationTextRegEx`, jako typ entity vyberte **RegEx** a pak vyberte **Další**.

    > [!div class="mx-imgBorder"]
    > ![Zahájit kroky vytvoření entity pro entitu regulárního výrazu](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. V **entitě Vytvořit regulární výraz** `x\d{5,6}` zadejte jako hodnotu **Regex** a pak vyberte **Vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Zadání regulárního výrazu pro extrahování dat z ukázkové utterance](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Vyberte **záměry** z levé nabídky, pak **ConfirmationText** záměr zobrazit regulární výraz označený v projevy.

    > [!div class="mx-imgBorder"]
    > ![Zobrazit regulární výraz označený v příkladech promluv](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Vzhledem k tomu, že entita není entita učenaná počítačem, entita se použije na projevy a zobrazí se na portálu LUIS, jakmile je vytvořena.

## <a name="train-the-app-before-testing-or-publishing"></a>Trénování aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace k dotazování z koncového bodu

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a predikce entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Přejděte na konec adresy URL v adrese a zadejte následující promluvu:

    `Text my pizza delivery to x23456 x234567 x12345`

    Poslední parametr querystring `query`je , **utterance dotazu**.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Pomocí entity regulárního výrazu služba LUIS extrahuje pojmenovaná data, což usnadňuje programové použití v klientské aplikaci, která přijímá odpověď JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Koncepce - entity](luis-concept-entity-types.md)
* [JSON odkaz entity regulárního výrazu](reference-entity-regular-expression.md?tabs=V3)
* [Jak přidat entity pro extrahování dat](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili nový záměr, přidali příklady promluv a pak vytvořili entitu regulárního výrazu k extrahování správně formátovaných data z promluv. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Informace o entitě seznamu](tutorial-list-entity.md)

