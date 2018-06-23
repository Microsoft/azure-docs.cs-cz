---
title: Předkompilované entity v LEOŠ | Microsoft Docs
description: Tento článek obsahuje seznam přednastavených entitami, které jsou zahrnuty v jazyk Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: 4858de8b8517016ca385e9105ca8948b66aa683b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322048"
---
# <a name="prebuilt-entities"></a>Předkompilované entity

LEOŠ zahrnuje sadu předem entity pro rozpozná běžné typy informací, jako kalendářních dat, časů, čísla, měření a měny. Podpora předem entity se liší podle jazykové verze LEOŠ aplikace. Úplný seznam předem entitami, které podporuje LEOŠ, včetně podpory podle jazykové verze, najdete v článku [odkaz předem entity](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **Builtin.DateTime** je zastaralý. Je nahrazena [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), který poskytuje rozpoznávání datum a čas rozsahů, a také vylepšené rozpoznávání nejednoznačný data a časy.

## <a name="add-a-prebuilt-entity"></a>Přidat předem entity

1. Otevřete aplikaci klepnutím na její název v **Moje aplikace** a pak klikněte na tlačítko **entity** na levé straně. 
2. Na **entity** klikněte na tlačítko **spravovat předem entity**.

    ![Stránka entity - spravovat předem entity](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. V **přidat předem entity** dialogovém okně klikněte na předem entity, které chcete přidat (například "datetimeV2"). Potom klikněte na **Uložit**.

    ![Předkompilované entity dialogové okno Přidat](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Použití předem číslo entity
Předkompilované entity je obsažen v aplikaci, její předpovědi jsou součástí k publikované aplikaci. Chování předem entit je předem vyškolení a **nelze** upravit. Postupujte podle těchto kroků najdete v článku Jak funguje předem entity:

1. Přidat **číslo** entity do vaší aplikace, pak [Train](interactive-test.md) a [publikování](PublishApp.md) aplikace.
2. Klikněte na adresu URL koncového bodu v **publikovat aplikace** stránky otevřete koncový bod LEOŠ ve webovém prohlížeči. 
3. Připojte utterance na adresu URL, který obsahuje číselný výraz. Například můžete zadat v `buy two plane ticktets`a zjistit, který identifikuje LEOŠ `two` jako `builtin.number` entity a identifikuje `2` jako její hodnota v `resolution` pole. `resolution` Pole pomáhá přeložit čísel a dat na kanonický tvar, který je pro klientskou aplikaci používat. 

    ![utterance v prohlížeči obsahující číslo entity](./media/luis-use-prebuilt-entity/browser-query.png)

LEOŠ inteligentně poznáte čísla, která nejsou v jiných standardní formuláře. Vyzkoušejte jiné číselné výrazy ve vaší utterances a zjistit, co LEOŠ vrací.

Následující příklad ukazuje odpověď JSON z LEOŠ, překlad hodnota 24, utterance "dvacet", který obsahuje.

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Použití předem datetimeV2 entity
**DatetimeV2** předem entity rozpozná kalendářních dat, časů, rozsahy dat a dobách trvání. Postupujte podle těchto kroků v tématu Jak `datetimeV2` předem entity funguje:

1. Přidat **datetimeV2** entity do vaší aplikace, pak [Train](interactive-test.md) a [publikování](PublishApp.md) aplikace.
2. Klikněte na adresu URL koncového bodu v **publikovat aplikace** stránky otevřete koncový bod LEOŠ ve webovém prohlížeči. 
3. Připojte utterance na adresu URL, která obsahuje rozsah dat. Například můžete zadat v `book a flight tomorrow`a zjistit, který identifikuje LEOŠ `tomorrow` jako `builtin.datetimeV2.date` entity a identifikuje datum následujícího dne jako její hodnota v `resolution` pole. 

Následující příklad ukazuje, může vypadat odpověď JSON LEOŠ 31 2017 říjen kdyby dnešní datum.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Odkaz na předem entity](./luis-reference-prebuilt-entities.md)
