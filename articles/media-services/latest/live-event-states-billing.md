---
title: Stav živé události a fakturace v Azure Media Services
description: V tomto tématu najdete přehled Azure Media Services stavů a fakturace živých událostí.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: c9fa12e1ee3778d0865c75662064bd4067e56d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897811"
---
# <a name="live-event-states-and-billing"></a>Stavy a fakturace živých událostí

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V Azure Media Services začne živá událost při přechodu stavu na **spuštěný** nebo **pohotovostní** stav účtovat ihned. Bude se vám účtovat i v případě, že se ve službě neprovádí tok videa. Chcete-li zastavit živou událost z fakturace, je nutné zastavit živou událost. Živý přepis se účtuje stejným způsobem jako živá událost.

Když je **LiveEventEncodingType** na [živé události](/rest/api/media/liveevents) nastavené na Standard nebo Premium1080p, Media Services automaticky vypne veškerou živou událost, která je stále ve **spuštěném** stavu, 12 hodin po ztrátě vstupního kanálu a neběží **živý výstup**. Stále se ale bude účtovat čas, kdy byla živá událost ve stavu **spuštěno** .

> [!NOTE]
> Průchozí živé události se automaticky nevypnou a musí se explicitně zastavit přes rozhraní API, aby se předešlo nadměrnému vyúčtování.

## <a name="states"></a>Stavy

Živá událost může být v jednom z následujících stavů.

|State|Popis|
|---|---|
|**Zastaveno**| Toto je počáteční stav události živého vysílání po vytvoření (pokud nebyl nastaven autostart na hodnotu true). V tomto stavu nedochází k žádnému fakturaci. Živá událost nemůže přijmout žádný vstup. |
|**Spouštění**| Spustí se živá událost a načítají se prostředky. V tomto stavu nedochází k žádnému fakturaci.  Pokud dojde k chybě, živá událost se vrátí do stavu Zastaveno.|
| **Mnohokrát** | Akce přidělení byla volána pro živou událost a prostředky jsou zřízeny pro tuto živou událost. Po úspěšném provedení této operace se živá událost převede do úsporného režimu.
|**StandBy**| prostředky živé události jsou zřízené a připravené ke spuštění. V tomto stavu dojde k fakturaci.  Většinu vlastností se pořád dají aktualizovat, ale během tohoto stavu se nepovolují ingestování nebo streamování.
|**Spuštěno**| Prostředky živé události byly přiděleny, byly vygenerovány adresy URL ingestování a verze Preview a jsou schopny přijímat živé streamy. V tomto okamžiku je fakturace aktivní. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události.|
|**Zastavování**| Dojde k zastavení živé události a probíhá rušení prostředků. V tomto přechodném stavu nedochází k žádnému účtování. |
|**odstraňování**| Dojde k odstranění živé události. V tomto přechodném stavu nedochází k žádnému účtování. |

Při vytváření živé události se můžete rozhodnout povolit živý přepis. Pokud to uděláte, budou se vám účtovat živý přepisy, kdykoli je živá událost ve stavu **spuštěno** . Všimněte si, že se vám bude účtovat i v případě, že nedojde k žádnému toku zvuku přes živou událost.

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](live-streaming-overview.md)
- [Kurz živého streamování](stream-live-tutorial-with-api.md)
