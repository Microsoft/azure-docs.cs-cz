---
title: Předem připravených entit v LUIS | Dokumentace Microsoftu
description: Tento článek obsahuje seznam předem připravených entit, které jsou zahrnuty v Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: diberry
ms.openlocfilehash: 95ff173a843e7ad18ec396a1130b2e2b4035f656
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248233"
---
# <a name="prebuilt-entities"></a>Předem připravených entit

Služba LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měny. Podpora předem připravených entit se liší podle jazykové verze vaší aplikace LUIS. Úplný seznam předem připravených entit, které podporuje služba LUIS, včetně podpory podle jazykové verze, najdete v článku [předem připravených entit odkaz](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **Builtin.DateTime** je zastaralý. Se nahrazuje [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), která poskytuje rozpoznávání datum a časové rozsahy a také vylepšené rozpoznávání nejednoznačný dat a časů.

## <a name="add-a-prebuilt-entity"></a>Přidat předem připravených entit

1. Otevřete aplikaci klepnutím na jeho název na **Moje aplikace** stránce a potom klikněte na tlačítko **entity** na levé straně. 
2. Na **entity** klikněte na **spravovat předem připravených entit**.

    ![Stránka entity – Správa předem připravených entit](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. V **přidání předem připravených entit** dialogovém okně klikněte na tlačítko předem připravených entit, které chcete přidat (například "datetimeV2"). Potom klikněte na **Uložit**.

    ![Přidat – dialogové okno předem připravených entit](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Použití předem připravených číselnou entitu
Předem připravených entit je obsažen ve vaší aplikaci, jeho předpovědi jsou zahrnuty v publikované aplikaci. Chování předem připravených entit je předem vytrénovaných a **nelze** upravit. Postupujte podle těchto kroků, abyste viděli, jak funguje předem připravených entit:

1. Přidat **číslo** entity do aplikace, pak [Train](luis-interactive-test.md) a [publikovat](luis-how-to-publish-app.md) aplikace.
2. Klikněte na adresu URL koncového bodu v **publikovat aplikaci** stránky ve webovém prohlížeči otevřete koncový bod služby LUIS. 
3. Připojte utterance na adresu URL, který obsahuje číselný výraz. Například můžete zadat v `buy two plane ticktets`a zjistit, který identifikuje LUIS `two` jako `builtin.number` entity a identifikuje `2` jako svou hodnotu v `resolution` pole. `resolution` Pole pomáhá při řešení čísel a dat pro kanonický tvar, který je pro klientskou aplikaci používat. 

    ![utterance v prohlížeči obsahující číselnou entitu](./media/luis-use-prebuilt-entity/browser-query.png)

Služba LUIS inteligentně poznáte čísla, která nejsou v nestandardním formuláře. Vyzkoušejte si různých číselných výrazů ve vašich projevy a zjistit, jaké vrací LUIS.

Následující příklad ukazuje služby luis, odpověď ve formátu JSON, který obsahuje řešení hodnota 24, utterance "z dvou tuctů".

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
## <a name="use-a-prebuilt-datetimev2-entity"></a>Použití předem připravených datetimeV2 entity
**DatetimeV2** předem připravených entit rozpozná data, časy, rozsahy kalendářních dat a dob trvání. Použijte následující postup v tématu Jak `datetimeV2` předem připravených entit funguje:

1. Přidat **datetimeV2** entity do aplikace, pak [Train](luis-interactive-test.md) a [publikovat](luis-how-to-publish-app.md) aplikace.
2. Klikněte na adresu URL koncového bodu v **publikovat aplikaci** stránky ve webovém prohlížeči otevřete koncový bod služby LUIS. 
3. Připojte utterance na adresu URL, který obsahuje konkrétní období. Například můžete zadat v `book a flight tomorrow`a zjistit, který identifikuje LUIS `tomorrow` jako `builtin.datetimeV2.date` entity a identifikuje zítřejší datum jako hodnotu v `resolution` pole. 

Následující příklad ukazuje, může vypadat odpověď JSON od LUIS 31. října 2017 byly dnešní datum.

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
> [Přehled předem připravených entit](./luis-reference-prebuilt-entities.md)
