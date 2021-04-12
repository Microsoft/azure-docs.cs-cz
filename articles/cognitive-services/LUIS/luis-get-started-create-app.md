---
title: 'Rychlý Start: Vytvoření aplikace – LUIS'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci LUIS, která používá předem vytvořenou doménu `HomeAutomation` pro zapnutí a vypnutí světla a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: e0972998fad647c1c70be814cb7c13f14a6e1141
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277024"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace domácí automatizace

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace).

### <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete vytvořit aplikaci, klikněte na  **+ Nová aplikace**. 

V okně, které se zobrazí, zadejte následující informace:

|Název  |Description  |
|---------|---------|
|AName     | Název vaší aplikace. Například "domácí automatizace".        |
|Kultura     | Jazyk, který vaše aplikace chápe a mluví.   |
|Description | Popis vaší aplikace
|Prostředek předpovědi | Předpověď prostředku, který bude přijímat dotazy. |

Vyberte **Hotovo**.

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

1. V navigační nabídce vlevo vyberte **záměry** , abyste viděli záměry domény HomeAutomation. Obsahuje příklad projevy, například `HomeAutomation.QueryState` a     `HomeAutomation.SetDevice` .

    > [!NOTE]
    > **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací.

1. Vyberte záměr **HomeAutomation.TurnOff**. Záměr obsahuje seznam příkladů projevyů, které jsou označeny entitami.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky záměru HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Snímek obrazovky záměru HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Pokud chcete zobrazit entity pro aplikaci, vyberte **entity**. Pokud kliknete na jednu z entit, například **HomeAutomation. název_zařízení** , zobrazí se seznam hodnot, které jsou k němu přidružené. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Alternativní text obrázku" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat.

1. Vyberte **test** z pravé horní navigační části.

1. Zadejte utterance testu jako `Turn off the lights` do interaktivního testovacího podokna a stiskněte klávesu ENTER. Například vypněte *světla*.

    V tomto příkladu `Turn off the lights` je správně identifikován jako nejlepší vyhodnocovací záměr **HomeAutomation. TurnOff**.

    ![Snímek obrazovky testovacího panelu se zvýrazněnou promluvou](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Pokud chcete zobrazit další informace o předpovědi, vyberte **zkontrolovat** .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky testovacího panelu s informacemi o kontrole](media/luis-quickstart-new-app/test.png)

1. Zavřete podokno test.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

Aby bylo možné získat LUIS předpověď v robotu chatu nebo v jiné klientské aplikaci, musíte aplikaci publikovat do koncového bodu předpovědi.

1. V pravém horním rohu okna vyberte **publikovat** .

1. Vyberte **produkční** slot a potom vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s LUIS publikováním do koncového bodu](media/howto-publish/publish-app-popup.png)

1. Vyberte odkaz **přístup k adresám URL koncových bodů** v oznámení, abyste přešli na stránku **prostředků Azure** . Adresa URL koncového bodu je uvedena jako **vzorový dotaz**.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

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

> [!div class="nextstepaction"]
> [Vývoj iterativních aplikací pro LUIS](./luis-concept-app-iteration.md)