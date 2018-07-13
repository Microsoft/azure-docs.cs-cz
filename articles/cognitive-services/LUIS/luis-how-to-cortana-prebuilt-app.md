---
title: Použití předem připravené aplikace Cortana služby luis | Dokumentace Microsoftu
description: Použití osobního asistenta Cortany, předem připravené aplikace z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/03/2018
ms.author: v-geberr
ms.openlocfilehash: f5cc97e49d3c127ffe7f53a315c42ab872782a49
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968720"
---
# <a name="cortana-prebuilt-app"></a>Předem připravené aplikace Cortana

> [!IMPORTANT]
> Doporučujeme použít [předem připravených domén](./luis-how-to-use-prebuilt-domains.md), namísto předem připravené aplikace Cortana. Například namísto z **builtin.intent.calendar.create_calendar_entry**, použijte **Calendar.Add** z **kalendáře** předem připravených domény.
> Předem připravených domén poskytují tyto výhody: 
> * Poskytují balíčky předem připravené a které je předem vytrénované záměry a entity, které jsou navrženy pro práci s mezi sebou. Předem připravené domény můžete integrovat přímo do vaší aplikace. Například pokud sestavujete vhodnosti sledování, můžete přidat **vhodnosti** domény a mít celou řadu záměry a entity pro sledování aktivity vhodnosti, včetně záměrů pro sledování váha a plánování jídla, zbývající čas nebo vzdálenost a ukládá vhodnosti aktivity poznámky.
> * Záměry předem připravených domény jsou přizpůsobitelné. Například pokud chcete poskytnout revize hotelů, můžete trénování a vlastní nastavení **Places.GetReviews** záměru od **míst** domény k rozpoznání žádosti o revize hotelu.
> * Předem připravených domén je rozšiřitelné. Například, pokud chcete použít **míst** předem připravených domény v robota, který vyhledá restaurace a potřebu záměru získávání typu kuchyni, můžete vytvářet a trénování **Places.GetCuisine** záměr.

Mimo možnosti sestavování vlastních aplikací, LUIS také poskytuje záměry a entity z osobního asistenta Microsoft Cortana jako předem připravené aplikace. Chování této veřejně dostupné aplikace LUIS nelze změnit. Záměry a entity v této aplikaci nelze upravovat nebo integrovaná do jiných aplikací LUIS. Pokud chcete vaše klientská aplikace bude mít přístup k této předem připravené aplikace a aplikace LUIS, pak klientské aplikace musí odkazovat na obě aplikace LUIS.

Předem připravené osobního asistenta aplikace je dostupná v těchto jazykové verze (národní prostředí): angličtina, francouzština, italština, španělština a čínština.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Získejte koncový bod pro předem připravená aplikace Cortana

Je možné otevřít předem připravené aplikace Cortana pomocí následujících koncových bodů: 

| Jazyk | Koncový bod|
|--------| ------------------|
| Angličtina| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Čínština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Francouzština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Španělština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    italština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


Koncový bod adresy URL jsou také k dispozici [aplikace – získání osobního asistenta aplikací](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) rozhraní API.

## <a name="try-out-the-personal-assistant-app"></a>Vyzkoušejte si app osobního asistenta
Například pokud utterance chcete interpretovat je "vytvořit schůzku s cílem schůzka s týmem", pak můžete připojit tuto utterance na URL koncového bodu. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key=YOUR-SUBSCRIPTION-KEY&q=create%20an%20appointment%20for%20team%20meeting
```

Vložte adresu URL do webového prohlížeče a klíče vašeho koncového bodu pro nahrazení `YOUR-SUBSCRIPTION-KEY` pole.

V prohlížeči uvidíte, že předem připravené aplikace Cortana identifikuje `builtin.intent.calendar.create_calendar_entry` jako záměr, a `builtin.calendar.title` jako typ entity a utterance `create an appointment for team meeting`.

```JSON
{
  "query": "create an appointment for team meeting",
  "topScoringIntent": {
    "intent": "builtin.intent.calendar.create_calendar_entry"
  },
  "entities": [
    {
      "entity": "team meeting",
      "type": "builtin.calendar.title"
    }
  ]
}
```

Předem připravené aplikace Cortana, protože se používají služby LUIS skóre nevrací. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Odkazovat na předem připravené aplikace Cortana](./luis-reference-cortana-prebuilt.md)

