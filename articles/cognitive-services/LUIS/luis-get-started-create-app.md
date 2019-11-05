---
title: 'Rychlý Start: Vytvoření aplikace – LUIS'
titleSuffix: Azure Cognitive Services
description: Vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 4acf6e4df978ffee6e0f8320bafbb64994aa0639
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495433"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace domácí automatizace

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto článku potřebujete bezplatný účet LUIS, vytvořený na portálu LUIS na webu [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace). 

1. Na portálu LUIS v seznamu Moje aplikace vyberte **+ vytvořit**.

    ![Na portálu LUIS vyberte v seznamu Moje aplikace možnost + vytvořit.](./media/create-app-in-portal.png)

1. V dialogovém okně pojmenujte aplikaci `Home Automation` potom vyberte **Hotovo**. LUIS vytvoří aplikaci.

    ![V dialogovém okně pojmenujte svou aplikaci jako domovskou automatizaci.](./media/create-new-app-details.png)

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

Vyberte **předem připravené domény** a pak vyhledejte **HomeAutomation**. Na kartě HomeAutomation vyberte **Přidat doménu** .

![Vyberte předem sestavené domény a vyhledejte "HomeAutomation". Na kartě HomeAutomation vyberte Přidat doménu.](media/luis-quickstart-new-app/home-automation.png)

Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

## <a name="intents-and-entities"></a>Záměry a entity

Pokud chcete zkontrolovat záměry domény HomeAutomation, vyberte **záměry** . Předem sestavené záměry domény mají ukázkovou projevy.

![Snímek obrazovky seznamu HomeAutomation záměrů](media/luis-quickstart-new-app/home-automation-intents.png "Snímek obrazovky seznamu HomeAutomation záměrů")

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací. 

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![Snímek obrazovky HomeAutomation. TurnOff – záměr](media/luis-quickstart-new-app/home-automation-turnoff.png "Snímek obrazovky HomeAutomation. TurnOff – záměr")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat. Vyberte **test**. Do interaktivního testovacího podokna zadejte utterance testu, jako je `Turn off the lights`, a stiskněte klávesu ENTER. 

```
Turn off the lights
```

Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

V tomto příkladu je `Turn off the lights` správně identifikovaný jako nejlepší vyhodnocovací záměr **HomeAutomation. TurnOff**.

![Snímek obrazovky testovacího panelu se zvýrazněným utterance](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Pokud si chcete prohlédnout Další informace o předpovědi, vyberte **zkontrolovat** .

![Snímek obrazovky testovacího panelu s informacemi o kontrole](media/luis-quickstart-new-app/test.png)

Znovu vyberte **Test** (Testovat) a sbalte podokno testování. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Dotaz na koncový bod prediktivního rozhraní API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)] 

1. V adresním řádku prohlížeče pro řetězec dotazu se ujistěte, že jsou v adrese URL následující řádky názvů a hodnot. Pokud nejsou v řetězci dotazu, přidejte je:

    |Dvojice název/hodnota|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. V adresním řádku prohlížeče přejděte na konec adresy URL a jako hodnotu _dotazu_ zadejte `turn off the living room light` a potom stiskněte klávesu ENTER.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.967174649
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
                            "score": 0.9494325,
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
