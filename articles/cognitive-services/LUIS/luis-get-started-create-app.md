---
title: 'Rychlý Start: Vytvoření aplikace – LUIS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci LUIS, která používá předem vytvořenou doménu `HomeAutomation` pro zapnutí a vypnutí světla a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 28bf79b61c0278a3f45820a23cd2c69f0b609700
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316477"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace domácí automatizace

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace).

1. V seznamu Moje aplikace vyberte **+ Nová aplikace pro konverzaci**a potom v seznamu možností vyberte **+ Nová aplikace pro konverzaci** znovu.

1. Do dialogového okna zadejte název aplikace `Home Automation` .
1. Jako jazykovou verzi vyberte **angličtinu** .
1. Zadejte volitelný popis.
1. Nevybírejte prostředek předpovědi, pokud jste ještě nevytvořili prostředek. Chcete-li použít koncový bod předpovědi vaší aplikace (pracovní nebo produkční), je nutné přiřadit prostředek předpovědi.
1. Vyberte **Hotovo**.

    LUIS vytvoří aplikaci.

    ![V dialogovém okně pojmenujte svou aplikaci jako domovskou automatizaci.](./media/create-new-app-details.png)

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi.

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

1. V levém navigačním panelu vyberte předem **sestavené domény**.
1. Vyhledejte **HomeAutomation**.
1. Na kartě HomeAutomation vyberte **Přidat doménu** .

    > [!div class="mx-imgBorder"]
    > ![Vyberte předem sestavené domény a vyhledejte "HomeAutomation". Na kartě HomeAutomation vyberte Přidat doménu.](media/luis-quickstart-new-app/home-automation.png)

    Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

## <a name="intents-and-entities"></a>Záměry a entity

1. Pokud chcete zkontrolovat záměry domény HomeAutomation, vyberte **záměry** . Předem sestavené záměry domény mají například projevy.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky seznamu HomeAutomation záměrů](media/luis-quickstart-new-app/home-automation-intents.png "Snímek obrazovky seznamu HomeAutomation záměrů")

    > [!NOTE]
    > **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací.

1. Vyberte záměr **HomeAutomation.TurnOff**. Záměr obsahuje seznam příkladů projevyů, které jsou označeny entitami.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky záměru HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Snímek obrazovky záměru HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat.

1. Vyberte **test** z pravé horní navigační části.

1. Zadejte utterance testu jako `Turn off the lights` do interaktivního testovacího podokna a stiskněte klávesu ENTER.

    ```
    Turn off the lights
    ```

    V tomto příkladu `Turn off the lights` je správně identifikován jako nejlepší vyhodnocovací záměr **HomeAutomation. TurnOff**.

    ![Snímek obrazovky testovacího panelu se zvýrazněnou promluvou](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Pokud chcete zobrazit další informace o předpovědi, vyberte **zkontrolovat** .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky testovacího panelu s informacemi o kontrole](media/luis-quickstart-new-app/test.png)

1. Zavřete podokno test.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Dotaz na koncový bod prediktivního rozhraní API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. V adresním řádku prohlížeče pro řetězec dotazu se ujistěte, že jsou v adrese URL následující řádky názvů a hodnot. Pokud nejsou v řetězci dotazu, přidejte je:

    |Dvojice název/hodnota|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. V adresním řádku prohlížeče přejděte na konec adresy URL a zadejte `turn off the living room light` hodnotu _dotazu_ a potom stiskněte klávesu ENTER.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
