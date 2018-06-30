---
title: Používání Cortana předkompilované aplikace z LEOŠ | Microsoft Docs
description: Použijte Cortana osobní asistent předkompilované aplikace z jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: c7249cb8d8cff29f419412025c69e3b2b76b49d1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110653"
---
# <a name="cortana-prebuilt-app"></a>Předkompilované aplikace Cortany

> [!IMPORTANT]
> Doporučujeme vám, že používáte [předem domény](./luis-how-to-use-prebuilt-domains.md), místo Cortana předkompilované aplikace. Například místo z **builtin.intent.calendar.create_calendar_entry**, použijte **Calendar.Add** z **kalendáře** předem domény.
> Předkompilované domény poskytují tyto výhody: 
> * Obsahují balíčky předem a pretrained tříd Intent a entitami, které jsou navržené tak, aby dobře pracoval s navzájem. Předkompilované domény můžete integrovat přímo do vaší aplikace. Například pokud vytváříte sledovací modul vhodnosti, můžete přidat **vhodnosti** domény a mají celou sadu tříd Intent a entity pro sledování vhodnosti aktivit, včetně záměry pro sledování váhy a plánování jídlem, zbývající čas nebo vzdálenost a ukládá vhodnosti aktivity poznámky.
> * Předkompilované domény záměry lze přizpůsobit. Například pokud byste chtěli poskytnout recenze hotels, můžete trénování a přizpůsobit **Places.GetReviews** záměrné z **místech** domény rozpoznat žádosti o hotelů recenze.
> * Předkompilované domény jsou extensible. Například, pokud chcete použít **místech** předem doménu v robota, který vyhledá restaurace a nutnosti záměrem pro získávání typu kuchyni, můžete sestavit a cvičení **Places.GetCuisine** záměr.

Kromě umožňuje vytvářet své vlastní aplikace, LEOŠ také nabízí tříd Intent a entity z pomocníka osobní Microsoft Cortana jako předem aplikaci. Nelze změnit chování této veřejně dostupné aplikace LEOŠ. Tříd Intent a entity v této aplikaci nelze upravit, nebo integrovaná do jiných aplikací LEOŠ. Pokud chcete klientské aplikace tak, aby měl přístup k této předkompilované aplikace a aplikace LEOŠ, klientské aplikace má Chcete-li obě LEOŠ aplikace.

Předkompilované pomocníka osobní aplikace je k dispozici v těchto jazykové verze (národní prostředí): angličtina, francouzština, italština, španělština a čínština.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Získání koncového bodu pro předkompilované aplikace Cortany

Máte přístup Cortana předkompilované aplikace pomocí následujících koncových bodů: 

| Jazyk | Koncový bod|
|--------| ------------------|
| Angličtina| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Čínština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Francouzština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Španělština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    italština| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> Koncový bod adresy URL jsou dostupné i z [aplikace - získat pomocníka osobní aplikace](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) rozhraní API.

## <a name="try-out-the-personal-assistant-app"></a>Vyzkoušet pomocníka osobní aplikace
K volání koncový bod, můžete připojit váš koncový bod argument a dotaz řetězec klíče ke koncovému bodu. 

Pokud utterance chcete interpretovat je například "Vytvoření události pro aplikaci team meeting", pak tento utterance můžete připojit k adrese URL koncového bodu. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

Vložte adresu URL do webového prohlížeče a nahraďte klíč koncový bod pro `{YOUR-SUBSCRIPTION-KEY}` pole.

V prohlížeči uvidíte, že předkompilované aplikace Cortana identifikuje `builtin.intent.calendar.create_calendar_entry` jako záměr, a `builtin.calendar.title` jako typ entity a utterance `create an appointment for team meeting`.

![Používání Cortana předkompilované aplikace](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Předkompilované aplikace Cortana odkaz](./luis-reference-cortana-prebuilt.md)

