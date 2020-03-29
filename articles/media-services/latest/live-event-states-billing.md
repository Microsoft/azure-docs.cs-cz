---
title: Stavy LiveEvent a fakturace ve službě Azure Media Services | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled stavů A fakturace Azure Media Services LiveEvent.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543071"
---
# <a name="live-event-states-and-billing"></a>Stavy živých událostí a fakturace

Ve službě Azure Media Services začne živá událost účtovat, jakmile její stav přejde na **Spuštěno**. Bude vám účtováno i v případě, že službou neprotéká žádné video. Chcete-li zastavit fakturaci živé události, musíte ji zastavit. Živý přepis se účtuje stejným způsobem jako živá událost.

Když **liveEventEncodingType** na [live událost](https://docs.microsoft.com/rest/api/media/liveevents) je nastavena na standardní nebo Premium1080p, Media Services automaticky vypne všechny živé události, která je stále ve stavu **Spuštění** 12 hodin po ztrátě vstupního kanálu a nejsou spuštěny žádné **živé výstupy.** Bude vám však stále účtována doba, po kterou byla živá událost ve stavu **Spuštěno.**

> [!NOTE]
> Předávací živé události nejsou automaticky vypnuty a musí být explicitně zastaveny prostřednictvím rozhraní API, aby se zabránilo nadměrné fakturaci. 

## <a name="states"></a>Stavy

Živá událost může být v jednom z následujících stavů.

|Stav|Popis|
|---|---|
|**Zastaveno**| Toto je počáteční stav živé události po vytvoření (pokud automatické spuštění bylo nastaveno na hodnotu true.) V tomto stavu nedojde k žádné fakturaci. V tomto stavu live událost vlastnosti mohou být aktualizovány, ale streamování není povoleno.|
|**Spuštění**| Živá událost se schylovala a přidělují se prostředky. V tomto stavu nedojde k žádné fakturaci. Aktualizace nebo streamování nejsou v tomto stavu povoleny. Pokud dojde k chybě, live událost vrátí do zastaveného stavu.|
|**Spuštěno**| Prostředky živých událostí byly přiděleny, byly generovány adresy URL ingestování a náhled a je schopen přijímat živé datové proudy. V tomto okamžiku je fakturace aktivní. Chcete-li zastavit další fakturaci, je nutné explicitně volat Stop na prostředku živé události.|
|**Zastavování**| Živá událost je zastavena a prostředky jsou de-provisioned. V tomto přechodném stavu nedojde k žádné fakturaci. Aktualizace nebo streamování nejsou v tomto stavu povoleny.|
|**Odstranění**| Živá událost je smazána. V tomto přechodném stavu nedojde k žádné fakturaci. Aktualizace nebo streamování nejsou v tomto stavu povoleny.|

Při vytváření živé události můžete povolit živé přepisy. Pokud tak učiníte, bude vám účtována živá přepisy vždy, když je živá událost ve **stavu Spuštění.** Všimněte si, že vám bude účtována i v případě, že neexistuje žádný zvuk proudící přes live události.

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování tutorial](stream-live-tutorial-with-api.md)
