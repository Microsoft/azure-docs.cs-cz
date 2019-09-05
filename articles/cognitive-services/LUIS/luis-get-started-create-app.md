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
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1704b62cae6375d376fc43fb7a2940cd9c717072
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382509"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace pro domácí automatizaci

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto článku potřebujete bezplatný účet LUIS, vytvořený na portálu LUIS na webu [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace). 

2. Vyberte **Create new app** (Vytvořit novou aplikaci).

    [![Snímek obrazovky aplikace seznam](media/luis-quickstart-new-app/app-list.png "snímek obrazovky seznamu aplikací")](media/luis-quickstart-new-app/app-list.png)

3. V dialogovém okně pojmenujte svou aplikaci „Home Automation“.

    [![Snímek obrazovky vytváření nové aplikace automaticky otevíraná okna](media/luis-quickstart-new-app/create-new-app-dialog.png "snímek obrazovky vytváření nové aplikace automaticky otevíraná okna")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Zvolte jazykovou verzi aplikace. Pro tuto aplikaci domácí automatizace vyberte angličtinu. Potom vyberte **Done** (Hotovo). LUIS vytvoří aplikaci Home Automation. 

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

V levém navigačním podokně vyberte **Prebuilt domains** (Předem připravené domény). Potom vyhledejte „Home“. Vyberte **Add domain** (Přidat doménu).

[![Snímek obrazovky z Domů automatizace domény vyznačeny v nabídce předem připravených domény](media/luis-quickstart-new-app/home-automation.png "domény – snímek obrazovky z Domů automatizace vyznačeny v nabídce předem připravených domény")](media/luis-quickstart-new-app/home-automation.png)

Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

[![Snímek obrazovky z Domů automatizace domény pomocí tlačítka odebrat](media/luis-quickstart-new-app/remove-domain.png "domény – snímek obrazovky z Domů automatizace pomocí tlačítka odebrat")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Záměry a entity

V levém navigačním podokně vyberte **Intents** (Záměry) a podívejte se na záměry domény Home Automation. Každý záměr má ukázkové promluvy.

![Snímek obrazovky seznamu HomeAutomation záměrů](media/luis-quickstart-new-app/home-automation-intents.png "Snímek obrazovky seznamu HomeAutomation záměrů")]

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací. 

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![Snímek obrazovky HomeAutomation.TurnOff záměr](media/luis-quickstart-new-app/home-automation-turnoff.png "snímek obrazovky HomeAutomation.TurnOff záměr")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat. V horní navigaci vyberte **Test** (Testovat). Do podokna Interactive Testing (Interaktivní testování) zadejte testovací promluvu, například „Turn off the lights“ (Vypnout světla), a stiskněte Enter. 

```
Turn off the lights
```

Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

V tomto příkladu `Turn off the lights` je správně identifikován jako nejlepší vyhodnocovací záměr **HomeAutomation. TurnOff**.

[![Snímek obrazovky testovací panel utterance zvýrazněnou](media/luis-quickstart-new-app/test.png "panelu testů snímek obrazovky s utterance zvýrazněnou")](media/luis-quickstart-new-app/test.png)


Pokud si chcete prohlédnout Další informace o předpovědi, vyberte **zkontrolovat** .

![Snímek obrazovky testovacího panelu se zvýrazněným utterance](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Znovu vyberte **Test** (Testovat) a sbalte podokno testování. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Dotaz na koncový bod předpovědi rozhraní v2 API

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Na konec adresy URL zadejte `turn off the living room light` a stiskněte Enter. V prohlížeči se zobrazí verze **v2 API** odpovědi na váš koncový bod HTTP.

    ```json
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.9753089
      },
      "intents": [
        {
          "intent": "HomeAutomation.TurnOff",
          "score": 0.9753089
        },
        {
          "intent": "HomeAutomation.QueryState",
          "score": 0.01027893
        },
        {
          "intent": "HomeAutomation.TurnUp",
          "score": 0.006881481
        },
        {
          "intent": "HomeAutomation.SetDevice",
          "score": 0.006786365
        },
        {
          "intent": "HomeAutomation.TurnDown",
          "score": 0.005145787
        },
        {
          "intent": "HomeAutomation.TurnOn",
          "score": 0.004114749
        },
        {
          "intent": "None",
          "score": 0.000598924
        }
      ],
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Location",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.94558233
        },
        {
          "entity": "living room light",
          "type": "HomeAutomation.DeviceName",
          "startIndex": 13,
          "endIndex": 29,
          "resolution": {
            "values": [
              "living room light"
            ]
          }
        },
        {
          "entity": "light",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 25,
          "endIndex": 29,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
## <a name="query-the-v3-api-prediction-endpoint"></a>Dotaz na koncový bod prediktivního rozhraní API V3

Pro [dotaz rozhraní API V3](luis-migration-api-v3.md)v prohlížeči změňte požadavek metody Get https a změňte hodnoty v lomených závorkách na vaše vlastní hodnoty. 

**Adresa URL v2 s metodou Get**:

https://\<region >. API. vnímání. Microsoft. com/Luis/**v 2.0**/Apps/\<AppID >? verbose = true & Subscription-Key =\<YOUR_KEY > &**q =\<User-utterance-text >**

**Adresa URL v3 s metodou Get**:

https://\<region >. API. vnímání. Microsoft. com/Luis/**v 3.0-Preview**/Apps/\<AppID >/**sloty**/**produkční**/**předpověď**? verbose = true & Subscription-Key =\<YOUR_KEY > &**dotaz =\<User-utterance-text >**

V prohlížeči se zobrazí verze **V3 rozhraní API** pro váš koncový bod HTTP s odpovědí JSON.

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
            ],
            "$instance": {
                "HomeAutomation.DeviceType": [
                    {
                        "type": "HomeAutomation.DeviceType",
                        "text": "lights",
                        "startIndex": 13,
                        "length": 6,
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

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
