---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123215"
---
### <a name="general-availability-and-preview-comparison"></a>Obecné dostupnosti a porovnání náhledů

Následující tabulka shrnuje několik klíčových rozdílů mezi instancemi Azure Time Series Insights obecné dostupnosti (GA) a náhledu.

| | GA | Preview |
| --- | --- | ---|
| Prvotřídní občan | Zaměřené na události | Centrické časové řady |
| Sémantické uvažování | Nízká úroveň (referenční údaje) | Na vysoké úrovni (modely) |
| Kontextová souvislost dat | Úroveň mimo zařízení | Úroveň zařízení a jiného zařízení |
| Úložiště výpočetní logiky | Ne | Uloženo v typových proměnných, které jsou součástí modelu |
| Řízení úložiště a přístupu | Ne | Povoleno pomocí modelu |
| Agregace/vzorkování | Ne | Vážená událost a vážený čas |
| Rekonstrukce signálu | Ne | Interpolace |
| Výroba odvozených časových řad | Ne | Ano, sloučení a spojení |
| Jazyková flexibilita | Nekomposable | Komodasable |
| Jazyk výrazů | Predikátový řetězec | Výrazy časových řad (predikátové řetězce, hodnoty, výrazy a funkce) |

### <a name="property-limits"></a>Omezení vlastností

Limity vlastností Time Series Insights se zvýšily na 1 000 z maximálního limitu 800 v GA. Zadané vlastnosti událostí mají odpovídající sloupce JSON, CSV a grafu, které lze zobrazit v [průzkumníku náhledu přehledů časové řady](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Skladová jednotka (SKU) | Maximální vlastnosti |
| --- | --- |
| Náhled PAYG | 1 000 vlastností (sloupců) |
| GA S1 | 600 vlastností (sloupců) |
| GA S2 | 800 vlastností (sloupce) |

### <a name="event-sources"></a>Zdroje událostí

Je podporovány maximálně dva zdroje událostí na instanci. 

* Přečtěte si, jak [přidat zdroj centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurace [zdroje centra IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Ve výchozím nastavení [podporují prostředí Preview rychlost příchozího přenosu dat](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) až 1 **mb/s na prostředí**. Zákazníci mohou v případě potřeby škálovat prostředí preview až na **16 MB/s.** K dispozici je také limit pro oddíl **0,5 MB/s**. 

### <a name="api-limits"></a>Limity rozhraní API

Omezení rozhraní API doby pro náhled přehledů časových řad jsou specifikována v [referenční dokumentaci rozhraní REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
