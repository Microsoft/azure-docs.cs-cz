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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 588aeede123848900fac6fab663dd1f6c6c169b6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719417"
---
# <a name="liveevent-states-and-billing"></a>Stavy Livestream a fakturace

Ve službě Azure Media Services Livestream začne fakturace jako jeho stav přejde do **systémem**. Pokud chcete zastavit Livestream z fakturace, je nutné zastavit Livestream.

Při **LiveEventEncodingType** na vaše [Livestream](https://docs.microsoft.com/rest/api/media/liveevents) se nastaví na standardní, automaticky nevypne jakékoli Livestream, který je stále v Media Services **systémem** stavu 12 hodin po Vstupní kanál dojde ke ztrátě a neexistují žádné **LiveOutput**s systémem. Však budou pořád se vám účtovat čas Livestream se **systémem** stavu.

## <a name="states"></a>Stavy

Livestream může být v jednom z následujících stavů.

|Stav|Popis|
|---|---|
|**Zastavení**| Toto je počáteční stav Livestream po vytvoření (Pokud autostart byl nastaven na hodnotu true.) Vyvolá se v tomto stavu bez fakturace. V tomto stavu je možné aktualizovat vlastnosti Livestream ale streamování není povolené.|
|**Spuštění**| Livestream se spouští a přidělování prostředků. Vyvolá se v tomto stavu bez fakturace. Při tomto stavu nejsou povoleny aktualizace ani streamování. Pokud dojde k chybě Livestream se vrátí do stavu Zastaveno.|
|**Spuštění**| Bylo vygenerováno Livestream prostředky byly přiděleny, ingestování a zobrazit jejich náhled adresy URL a je schopná přijmout živých streamů. V tomto okamžiku fakturace je aktivní. Musíte explicitně volat Stop prostředku Livestream, která zastaví další fakturace.|
|**Zastavení**| Livestream se zastavuje a prostředky se zruší. Vyvolá se v tomto přechodném stavu bez fakturace. Při tomto stavu nejsou povoleny aktualizace ani streamování.|
|**Odstraňuje se**| Livestream se odstraňuje. Vyvolá se v tomto přechodném stavu bez fakturace. Při tomto stavu nejsou povoleny aktualizace ani streamování.|

## <a name="next-steps"></a>Další postup

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)
