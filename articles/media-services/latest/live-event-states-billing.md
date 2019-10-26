---
title: Livestream stavy a fakturace v Azure Media Services | Microsoft Docs
description: V tomto tématu najdete přehled Azure Media Services stavů a fakturace Livestream.
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
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933684"
---
# <a name="live-event-states-and-billing"></a>Stavy a fakturace živých událostí

V Azure Media Services zahájí živá událost účtování, jakmile se změní stav na **spuštěno**. Chcete-li zastavit živou událost z fakturace, je nutné zastavit živou událost.

Když je **LiveEventEncodingType** na [živé události](https://docs.microsoft.com/rest/api/media/liveevents) nastavené na Standard nebo Premium1080p, Media Services automaticky vypne veškerou živou událost, která je stále ve **spuštěném** stavu 12 hodin po ztrátě vstupního kanálu, a nebudete mít **živý výstup** . s běží. Stále se ale bude účtovat čas, kdy byla živá událost ve stavu **spuštěno** .

> [!NOTE]
> Průchozí živé události se automaticky nevypnou a musí se explicitně zastavit přes rozhraní API, aby se předešlo nadměrnému vyúčtování. 

## <a name="states"></a>Stavy

Živá událost může být v jednom z následujících stavů.

|Stav|Popis|
|---|---|
|**Ukončen**| Toto je počáteční stav události živého vysílání po vytvoření (pokud nebyl nastaven autostart na hodnotu true). V tomto stavu nedochází k žádnému fakturaci. V tomto stavu je možné aktualizovat vlastnosti živé události, ale streamování není povolené.|
|**Začátek**| Spustí se živá událost a prostředky se přidělují. V tomto stavu nedochází k žádnému fakturaci. V tomto stavu nejsou povoleny aktualizace nebo streamování. Pokud dojde k chybě, živá událost se vrátí do stavu Zastaveno.|
|**Instalovanou**| Prostředky živé události byly přiděleny, byly vygenerovány adresy URL ingestování a verze Preview a jsou schopny přijímat živé streamy. V tomto okamžiku je fakturace aktivní. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události.|
|**Zastaví**| Dojde k zastavení živé události a probíhá rušení prostředků. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povoleny aktualizace nebo streamování.|
|**Odstraňuje**| Živá událost se odstraňuje. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povoleny aktualizace nebo streamování.|

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](live-streaming-overview.md)
- [Kurz živého streamování](stream-live-tutorial-with-api.md)
