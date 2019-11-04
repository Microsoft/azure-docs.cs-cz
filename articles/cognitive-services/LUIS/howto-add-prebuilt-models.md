---
title: Předem připravené modely pro Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS zahrnuje sadu předem připravených modelů pro rychlé přidávání běžných scénářů pro konverzační uživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507778"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Přidat předem připravené modely pro běžné scénáře použití 

LUIS zahrnuje sadu předem připravených záměrů z předem připravených domén pro rychlé přidávání běžných záměrů a projevy. Toto je rychlý a snadný způsob, jak přidat možnosti do vaší konverzační klientské aplikace, aniž byste museli navrhovat modely pro tyto možnosti. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény

1. Na stránce **Moje aplikace** vyberte svou aplikaci. Tím se aplikace otevře v části **Build (sestavení** ) aplikace. 

1. Na stránce **záměry** vyberte **Přidat předem sestavené domény** z dolního a levého panelu nástrojů. 

1. Vyberte záměr **kalendáře** a pak vyberte tlačítko **Přidat doménu** .

    ![Přidat předem vytvořenou doménu kalendáře](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. V levém navigačním panelu vyberte **záměry** k zobrazení záměrů kalendáře. Každý záměr z této domény je předponou `Calendar.`. Společně s projevy se do aplikace přidají dvě entity pro tuto doménu: `Calendar.Location` a `Calendar.Subject`. 

### <a name="train-and-publish"></a>Trénování a publikování

1. Po přidání domény výuku aplikace vyberete tak, že v horním a pravém panelu nástrojů vyberete **vlak** . 

1. Na horním panelu nástrojů vyberte **publikovat**. Publikování do **produkčního**prostředí. 

1. Po zobrazení zeleného oznámení o úspěchu zaškrtněte políčko odkaz na **seznam koncových bodů** pro zobrazení koncových bodů.

1. Vyberte koncový bod. Do tohoto koncového bodu se otevře nová karta prohlížeče. Nechejte kartu prohlížeč otevřenou a pokračujte v části **test** .

### <a name="test"></a>Testování

Otestujte nový záměr na koncovém bodu tím, že přidáte hodnotu parametru **q** : `Schedule a meeting with John Smith in Seattle next week`.

LUIS vrátí správný záměr a předmět schůzky:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Přidat předem sestavený záměr

1. Na stránce **Moje aplikace** vyberte svou aplikaci. Tím se aplikace otevře v části **Build (sestavení** ) aplikace. 

1. Na stránce **záměry** vyberte **Přidat předem sestavený záměr** z panelu nástrojů nad seznamem záměry. 

1. V místním dialogovém okně vyberte možnost **nástroje. zrušit** záměr. 

    ![Přidat předdefinovaný záměr](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Vyberte tlačítko **Hotovo** .

### <a name="train-and-test"></a>Výuka a testování

1. Po přidání záměru výuku aplikace vyberete tak, že na horním panelu nástrojů kliknete na tlačítko **výuka** . 

1. Otestujte nový záměr výběrem **testu** na pravém panelu nástrojů. 

1. Do textového pole zadejte projevy pro zrušení:

    |Utterance testu|Skóre předpovědi|
    |--|:--|
    |Chci zrušit svůj let.|0,67|
    |Zrušte nákup.|0,52|
    |Zrušit schůzku.|0,56|

    ![Předdefinovaný záměr testu](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Přidat předem vytvořenou entitu

1. Otevřete aplikaci kliknutím na její název na stránce **Moje aplikace** a potom klikněte na **entity** na levé straně. 

1. Na stránce **entity** klikněte na **Přidat předem vytvořenou entitu**.

1. V dialogovém okně **Přidat předem připravené entity** vyberte předem vytvořenou entitu datetimeV2. 

    ![Dialogové okno Přidat předem vytvořenou entitu](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Vyberte **Done** (Hotovo). Po přidání entity není nutné tuto aplikaci podávat. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikování pro zobrazení představěného modelu z předpovědi koncového bodu

Nejjednodušší způsob, jak zobrazit hodnotu předem vytvořeného modelu, je dotaz z publikovaného koncového bodu. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Označení entit obsahujících předem sestavený token entity
 Pokud máte text, například `HH-1234`, který chcete označit jako vlastní entitu _a_ přidáte do modelu [předem připravené číslo](luis-reference-prebuilt-number.md) , nebudete moct vlastní entitu označit na portálu Luis. Můžete ho označit pomocí rozhraní API. 

 Aby bylo možné označit tento typ tokenu, pokud je jeho součást již označena předem vytvořenou entitou, odeberte předem vytvořenou entitu z aplikace LUIS. Tuto aplikaci nemusíte proškolit. Pak označte token vlastní entitou. Pak přidejte předem vytvořenou entitu zpátky do aplikace LUIS.

 Pro jiný příklad zvažte utterance jako seznam předvoleb třídy: `I want first year spanish, second year calculus, and fourth year english lit.` Pokud má aplikace LUIS přidané pořadové číslo předbuildu, `first`, `second`a `fourth` již bude označena pořadovým číslem. Pokud chcete zachytit ordinální číslo a třídu, můžete vytvořit složenou entitu a zabalit ji kolem předem sestavené pořadové číslo a vlastní entity pro název třídy.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Sestavení modelu ze souboru. csv s rozhraními REST API](./luis-tutorial-node-import-utterances-csv.md)
