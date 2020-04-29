---
title: 'Kurz: regulární výraz entity – LUIS'
description: Extrahuje konzistentně formátovaná data z utterance pomocí entity regulárního výrazu.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545841"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Kurz: získání dobře formátovaných dat z utterance
V tomto kurzu vytvoříte entitu regulárního výrazu pro extrakci konzistentně formátovaných dat z utterance.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat aplikaci
> * Přidat záměr
> * Přidání entity regulárního výrazu
> * Školení, publikování a dotazování aplikace pro získání extrahovaných dat

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entity regulárního výrazu

Použijte entitu regulárního výrazu k získání textu ve správném formátu z utterance. Když se záměr promluvy vždy určuje pomocí strojového učení, tento konkrétní typ entity se pomocí strojového učení nezískává. Dobrá možnost pro entitu regulárního výrazu je libovolný text, který lze konzistentně znázornit pomocí [regulárního výrazu](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

V tomto příkladu je použit _krátký kód_ pro posílání textových zpráv. Tento krátký kód je číselný kód 5 nebo 6 číslic, s předponou x a může být popsán pomocí regulárního výrazu `x\d{5,6}`.

Když do aplikace LUIS přidáte entitu regulárního výrazu, nemusíte text [Označit](label-entity-example-utterance.md) regulární entitou Express. Aplikuje se na všechny projevy ve všech záměrech.

## <a name="import-example-json-to-begin-app"></a>Import example. JSON pro zahájení aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Vytvoření záměru pro odesílání textových zpráv s potvrzením

1. Vyberte **+ vytvořit** k vytvoření nového záměru pro klasifikaci utteranceho záměru pro odeslání potvrzovacího textu.

1. V automaticky otevíraném dialogovém okně zadejte `ConfirmationText` a pak vyberte **Done** (Hotovo).

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Odeslání doby doručení Pizza do x123432|
    |X234567 txt pro čas|
    |x23987 pro oznámení|

    K extrakci entit zjištěných počítačem byste měli uvést příklady, které obsahují entitu v celé řadě projevy, ale s touto entitou, která se nestrojově dozvěděla, variace není důležitá. Dokud je text shodný s regulárním výrazem, bude extrahován.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Použití entity regulárního výrazu pro dobře formátovaná data
Vytvořte entitu regulárního výrazu, která bude odpovídat textovému číslu. Tento regulární výraz odpovídá textu, ale ignoruje varianty velikosti písmen a kultury.

1. Na levém panelu vyberte **Entities** (Entity).

1. Na stránce seznam entit vyberte **+ vytvořit** .

1. V automaticky otevíraném okně zadejte název `ConfirmationTextRegEx`nové entity, jako typ entity vyberte **Regex** a pak vyberte **Další**.

    > [!div class="mx-imgBorder"]
    > ![Zahájit kroky vytváření entit pro entitu regulárního výrazu](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. V **entitě vytvoření regulárního výrazu**zadejte `x\d{5,6}` jako hodnotu **regulárního výrazu** a pak vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Zadejte regulární výraz pro extrakci dat z příkladu utterance](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. V nabídce vlevo vyberte **záměry** a pak **ConfirmationText** záměr zobrazit regulární výraz označený v projevy.

    > [!div class="mx-imgBorder"]
    > ![Zobrazit regulární výraz označený jako příklad projevy](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Vzhledem k tomu, že entita není entita získaná počítačem, je entita použita na projevy a zobrazená na portálu LUIS, jakmile se vytvoří.

## <a name="train-the-app-before-testing-or-publishing"></a>Výuka aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace pro dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. V adresním řádku pokračujte na konec adresy URL a nahraďte _YOUR_QUERY_HERE_ :

    `Text my pizza delivery to x23456 x234567 x12345`

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

* [Koncept – entity](luis-concept-entity-types.md)
* [Odkaz JSON entity regulárního výrazu](reference-entity-regular-expression.md?tabs=V3)
* [Postup přidání entit pro extrakci dat](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili nový záměr, přidali příklady promluv a pak vytvořili entitu regulárního výrazu k extrahování správně formátovaných data z promluv. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Informace o entitě seznamu](tutorial-list-entity.md)

