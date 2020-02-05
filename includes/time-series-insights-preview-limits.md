---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 8537db1e5703abd8df92a2950455bee556731d08
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013874"
---
### <a name="general-availability-and-preview-comparison"></a>Obecná dostupnost a porovnání verze Preview

Následující tabulka shrnuje několik hlavních rozdílů mezi Azure Time Series Insights obecné dostupnosti (GA) a instance Preview.

| | GA | Preview |
| --- | --- | ---|
| Občan první třídy | Orientované na události | Orientované na časovou řadu |
| Sémantický důvod | Nízká úroveň (referenční data) | Nejvyšší úroveň (modely) |
| Kontextová data | Úroveň bez zařízení | Na úrovni zařízení a mimo zařízení |
| Úložiště COMPUTE Logic | Ne | Uloženo v části typu proměnné modelu |
| Úložiště a řízení přístupu | Ne | Povoleno přes model |
| Agregace a vzorkování | Ne | Vážené a časově vážené události |
| Rekonstrukce signálu | Ne | Interpolace |
| Výroba odvozených časových řad | Ne | Ano, sloučit a spojení |
| Flexibilita jazyka | Bez možnosti složení | Sestavitelné |
| Jazyk výrazů | Řetězec predikátu | Výrazy časových řad (řetězce predikátů, hodnoty, výrazy a funkce) |

### <a name="property-limits"></a>Omezení vlastností

Time Series Insights limity vlastností se zvýšily na 1 000 z maximálního limitu 800 v GA. Zadané vlastnosti události mají odpovídající sloupce JSON, CSV a grafu, které můžete zobrazit v [průzkumníkovi Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Skladová položka | Maximální vlastnosti |
| --- | --- |
| Náhled PAYG | 1 000 vlastnosti (sloupce) |
| GA S1 | 600 vlastnosti (sloupce) |
| GA S2 | 800 vlastnosti (sloupce) |

### <a name="event-sources"></a>Zdroje událostí

Podporuje se maximálně dva zdroje událostí na instanci. 

* Naučte se [Přidat zdroj centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Nakonfigurujte [Zdroj služby IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Ve výchozím nastavení [podporují prostředí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress) pro příchozí přenosy až **1 megabajt za sekundu (MB/s) na prostředí**. Zákazníci můžou v případě potřeby škálovat v prostředích ve verzi Preview až o **16 MB/s** . K dispozici je také omezení počtu **0,5 MB/s**na oddíly. 

### <a name="api-limits"></a>Omezení rozhraní API

Omezení REST API pro Time Series Insights Preview jsou uvedena v [referenční dokumentaci k REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
