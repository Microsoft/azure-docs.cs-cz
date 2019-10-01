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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703244"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace domácí automatizace

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

## <a name="prerequisites"></a>Předpoklady

Pro účely tohoto článku potřebujete bezplatný účet LUIS, vytvořený na portálu LUIS na webu [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace). 

1. Vyberte **Create new app** (Vytvořit novou aplikaci).

    [![Snímek](media/luis-quickstart-new-app/app-list.png "obrazovky se") seznamem aplikací v seznamu aplikací](media/luis-quickstart-new-app/app-list.png)

1. V dialogovém okně pojmenujte svou aplikaci „Home Automation“.

    [![Snímek obrazovky s dialogovým oknem vytvořit novou aplikaci –](media/luis-quickstart-new-app/create-new-app-dialog.png "snímek obrazovky s dialogovým oknem vytvořit nové aplikace – místní obrazovka")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Zvolte jazykovou verzi aplikace. Pro tuto aplikaci domácí automatizace vyberte angličtinu. Potom vyberte **Done** (Hotovo). LUIS vytvoří aplikaci Home Automation. 

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

V levém navigačním podokně vyberte **Prebuilt domains** (Předem připravené domény). Potom vyhledejte „Home“. Vyberte **Add domain** (Přidat doménu).

[![Snímek obrazovky domovské domény služby Automation v nabídce předem připravená doména](media/luis-quickstart-new-app/home-automation.png "snímek obrazovky domácí domény automatizace s názvem v nabídce předem vytvořená") doména](media/luis-quickstart-new-app/home-automation.png)

Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

[![Snímek obrazovky domovské domény služby Automation s tlačítkem odebrat](media/luis-quickstart-new-app/remove-domain.png "snímek obrazovky domovské domény v doméně automatizace s tlačítkem odebrat")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Záměry a entity

V levém navigačním podokně vyberte **Intents** (Záměry) a podívejte se na záměry domény Home Automation. Každý záměr má ukázkové promluvy.

Snímek obrazovky se ![seznamem HomeAutomation záměrů]pro seznam záměrů(media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation")]

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací. 

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![Snímek obrazovky záměru HomeAutomation. TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "pro HomeAutomation. TurnOff záměr")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat. V horní navigaci vyberte **Test** (Testovat). Do podokna Interactive Testing (Interaktivní testování) zadejte testovací promluvu, například „Turn off the lights“ (Vypnout světla), a stiskněte Enter. 

```
Turn off the lights
```

Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

V tomto příkladu je `Turn off the lights` správně identifikovaný jako nejlepší vyhodnocovací záměr **HomeAutomation. TurnOff**.

[![Snímek obrazovky testovacího panelu se zvýrazněným utteranceem](media/luis-quickstart-new-app/test.png "obrazovky testovacího panelu se zvýrazněným utterance")](media/luis-quickstart-new-app/test.png)


Pokud si chcete prohlédnout Další informace o předpovědi, vyberte **zkontrolovat** .

![Snímek obrazovky testovacího panelu se zvýrazněným utterance](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Znovu vyberte **Test** (Testovat) a sbalte podokno testování. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Dotaz na koncový bod předpovědi rozhraní v2 API

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Na konec adresy URL zadejte `turn off the living room light` a stiskněte Enter. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Koncový bod pro předpověď v2](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    V prohlížeči se zobrazí verze **v2 API** odpovědi na váš koncový bod HTTP.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[Prediktivní koncový bod V3](#tab/V3)

    Pro [dotaz rozhraní API V3](luis-migration-api-v3.md)v prohlížeči změňte požadavek metody Get https a změňte hodnoty v lomených závorkách na vaše vlastní hodnoty.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
