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
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543071"
---
# <a name="live-event-states-and-billing"></a>Stavy a fakturace živých událostí

V Azure Media Services zahájí živá událost účtování, jakmile se změní stav na **spuštěno**. Bude se vám účtovat i v případě, že se ve službě neprovádí tok videa. Chcete-li zastavit živou událost z fakturace, je nutné zastavit živou událost. Živý přepis se účtuje stejným způsobem jako živá událost.

Když je **LiveEventEncodingType** na [živé události](https://docs.microsoft.com/rest/api/media/liveevents) nastavené na Standard nebo Premium1080p, Media Services automaticky vypne veškerou živou událost, která je stále ve **spuštěném** stavu, 12 hodin po ztrátě vstupního kanálu a neběží **živý výstup**. Stále se ale bude účtovat čas, kdy byla živá událost ve stavu **spuštěno** .

> [!NOTE]
> Průchozí živé události se automaticky nevypnou a musí se explicitně zastavit přes rozhraní API, aby se předešlo nadměrnému vyúčtování. 

## <a name="states"></a>Stavy

Živá událost může být v jednom z následujících stavů.

|Stav|Popis|
|---|---|
|**Zastaveno**| Toto je počáteční stav události živého vysílání po vytvoření (pokud nebyl nastaven autostart na hodnotu true). V tomto stavu nedochází k žádnému fakturaci. V tomto stavu je možné aktualizovat vlastnosti živé události, ale streamování se nepovoluje.|
|**Začátek**| Spustí se živá událost a prostředky se přidělují. V tomto stavu nedochází k žádnému fakturaci. V tomto stavu nejsou povoleny aktualizace nebo streamování. Pokud dojde k chybě, živá událost se vrátí do stavu Zastaveno.|
|**Spuštěno**| Prostředky živé události byly přiděleny, byly vygenerovány adresy URL ingestování a verze Preview a jsou schopny přijímat živé streamy. V tomto okamžiku je fakturace aktivní. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události.|
|**Zastavování**| Dojde k zastavení živé události a probíhá rušení prostředků. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povoleny aktualizace nebo streamování.|
|**Odstraňuje**| Dojde k odstranění živé události. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povoleny aktualizace nebo streamování.|

Při vytváření živé události se můžete rozhodnout povolit živý přepis. Pokud to uděláte, budou se vám účtovat živý přepisy, kdykoli je živá událost ve stavu **spuštěno** . Všimněte si, že se vám bude účtovat i v případě, že nedojde k žádnému toku zvuku přes živou událost.

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](live-streaming-overview.md)
- [Kurz živého streamování](stream-live-tutorial-with-api.md)
