---
title: 'Úvodní příručka: vytvoření aplikace – LUIS'
description: Tento rychlý start ukazuje, jak vytvořit aplikaci `HomeAutomation` LUIS, která používá předem připravenou doménu pro zapnutí a vypnutí světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: de6cf5e95ee63fc9500cf1b5edab78597bdb18af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287794"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace domácí automatizace

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace).

1. V seznamu Moje aplikace vyberte **+ Nová aplikace pro konverzaci**.

1. V dialogovém okně pojmenujte aplikaci `Home Automation`. Jako **jazykovou** verzi vyberte angličtinu. Popis je volitelný a nepoužívá se pro vytváření nebo předpověď. Prostředek předpověď je také volitelné při vytváření aplikace LUIS. Vyberte **Done** (Hotovo).

    Luis vytvoří aplikaci. Když publikujete aplikaci do produkčního prostředí, měli byste přiřadit prostředek předpovědi, aby vaše aplikace mohla zpracovat mnoho požadavků.

    ![V dialogovém okně pojmenujte aplikaci "Domácí automatizace"](./media/create-new-app-details.png)

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi.

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

Vyberte **předem vytvořené domény** a vyhledejte **službu HomeAutomation**. Na kartě HomeAutomation vyberte **Přidat doménu.**

![Vyberte možnost Předem sestavené domény a vyhledejte možnost HomeAutomation. Na kartě HomeAutomation vyberte možnost Přidat doménu.](media/luis-quickstart-new-app/home-automation.png)

Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

## <a name="intents-and-entities"></a>Záměry a entity

Vyberte **záměry** ke kontrole záměrů domény HomeAutomation. Předem vytvořené záměry domény mají ukázkové projevy.

![Snímek obrazovky se seznamem záměrů homeautomation](media/luis-quickstart-new-app/home-automation-intents.png "Snímek obrazovky se seznamem záměrů homeautomation")

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací.

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![Snímek obrazovky záměru HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Snímek obrazovky záměru HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat.

1. Z navigace vpravo nahoře vyberte **Testovat.** 1. Zadejte testovací projev `Turn off the lights` jako do interaktivního testovacího podokna a stiskněte Enter.

    ```
    Turn off the lights
    ```

    Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

    V tomto `Turn off the lights` příkladu je správně identifikován jako hlavní bodování záměr **HomeAutomation.TurnOff**.

    ![Snímek obrazovky testovacího panelu se zvýrazněnou promluvou](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Chcete-li zkontrolovat další informace o předpovědi, vyberte **možnost Zkontrolovat.**

    ![Snímek obrazovky panelu Test s informacemi o kontrole](media/luis-quickstart-new-app/test.png)

1. Zavřete testovací podokno.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Dotaz na koncový bod předpovědi rozhraní API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. V adresním řádku prohlížeče se pro řetězec dotazu ujistěte, že v adrese URL jsou následující panely názvů a hodnot. Pokud nejsou v řetězci dotazu, přidejte je:

    |Dvojice název/hodnota|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. V adresním řádku prohlížeče přejděte na konec `turn off the living room light` adresy URL a zadejte hodnotu _dotazu_ a stiskněte Enter.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
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
                            "score": 0.907323956,
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

    Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
