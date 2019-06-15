---
title: Stavy Livestream a fakturace ve službě Azure Media Services | Dokumentace Microsoftu
description: Toto téma poskytuje přehled o Azure Media Services Livestream stavy a fakturaci.
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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2907b5be7f8d5fda3d510484179e80b065ab64b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074898"
---
# <a name="live-event-states-and-billing"></a>Živé události stavů a fakturace

Ve službě Azure Media Services, živá událost začne fakturace jako jeho stav přejde do **systémem**. Pokud chcete zastavit živá událost z fakturace, budete muset zastavit živá událost.

Při **LiveEventEncodingType** na vaše [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) nastavená na úroveň Standard nebo Premium1080p, automaticky nevypne živá událost, která je stále v Media Services **systémem** stavu 12 hodin poté, co dojde ke ztrátě vstupní kanál a neexistují žádné **Live výstup**s systémem. Však budou i nadále se vám účtovat čas živá událost byla v **systémem** stavu.

## <a name="states"></a>Stavy

Živá událost může být v jednom z následujících stavů.

|Stav|Popis|
|---|---|
|**Zastavení**| Toto je počáteční stav živá událost po vytvoření (Pokud autostart byl nastaven na hodnotu true.) Vyvolá se v tomto stavu bez fakturace. V tomto stavu je možné aktualizovat vlastnosti živá událost, ale streamování není povolené.|
|**Spuštění**| Živá událost se spouští a přidělování prostředků. Vyvolá se v tomto stavu bez fakturace. Při tomto stavu nejsou povoleny aktualizace ani streamování. Pokud dojde k chybě, živá událost se vrátí do stavu Zastaveno.|
|**Spuštění**| Živá událost prostředky byly přiděleny, ingestování a zobrazit jejich náhled adresy URL byly vytvořeny, a je schopná přijmout živých streamů. V tomto okamžiku fakturace je aktivní. Musíte explicitně volat Stop prostředku živá událost, která zastaví další fakturace.|
|**Zastavení**| Živá událost se zastavuje a prostředky se zruší. Vyvolá se v tomto přechodném stavu bez fakturace. Při tomto stavu nejsou povoleny aktualizace ani streamování.|
|**Odstraňuje se**| Živá událost se odstraňuje. Vyvolá se v tomto přechodném stavu bez fakturace. Při tomto stavu nejsou povoleny aktualizace ani streamování.|

## <a name="next-steps"></a>Další postup

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)
