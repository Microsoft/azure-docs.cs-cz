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
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585517"
---
# <a name="liveevent-states-and-billing"></a>Stavy Livestream a fakturace

Ve službě Azure Media Services Livestream začne fakturace jako jeho stav přejde do **systémem**. Pokud chcete zastavit Livestream z fakturace, je nutné zastavit Livestream.

Při **LiveEventEncodingType** na vaše [Livestream](https://docs.microsoft.com/rest/api/media/liveevents) se nastaví na standardní (Basic), automaticky nevypne jakékoli Livestream, který je stále v Media Services **systémem** stavu 12 hodin poté, co dojde ke ztrátě vstupní kanál a neexistují žádné **LiveOutput**s systémem. Však budou pořád se vám účtovat čas Livestream se **systémem** stavu.

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
