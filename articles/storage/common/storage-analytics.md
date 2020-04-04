---
title: Pomocí analýzy Azure Storage můžete shromažďovat protokoly a data metrik | Dokumenty společnosti Microsoft
description: Storage Analytics umožňuje sledovat data metrik pro všechny služby úložiště a shromažďovat protokoly pro úložiště objektů Blob, Fronta a Tabulka.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 4ad9f13bcdf36b67400adb62d58ee260ff256bb3
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637150"
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics provádí protokolování a poskytuje data metrik pro účet úložiště. Tato data můžete použít ke sledování požadavků, analýze trendů využití a diagnostice problémů s účtem úložiště.

Chcete-li používat Storage Analytics, musíte ji povolit individuálně pro každou službu, kterou chcete sledovat. Můžete ji povolit z [webu Azure Portal](https://portal.azure.com). Podrobnosti najdete [v tématu Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md). Analýzu úložiště můžete také povolit programově prostřednictvím rozhraní REST API nebo klientské knihovny. Pomocí nástrojů [Nastavit vlastnosti služby objektů blob](/rest/api/storageservices/set-blob-service-properties), [Nastavit vlastnosti služby fronty](/rest/api/storageservices/set-queue-service-properties), Nastavit [vlastnosti služby table service](/rest/api/storageservices/set-table-service-properties)a Nastavit [vlastnosti služby souboru](/rest/api/storageservices/Get-File-Service-Properties) povolte analýzu úložiště pro každou službu.

Agregovaná data jsou uložena ve známém objektu blob (pro protokolování) a ve známých tabulkách (pro metriky), ke kterým lze přistupovat pomocí řešení API služby Blob a table service.

Storage Analytics má limit 20 TB na množství uložených dat, které je nezávislé na celkovém limitu pro váš účet úložiště. Další informace o omezení účtu úložiště najdete v tématu [Škálovatelnost a cíle výkonu pro účty standardního úložiště](scalability-targets-standard-account.md).

Podrobný průvodce používáním služby Storage Analytics a dalších nástrojů k identifikaci, diagnostice a řešení problémů souvisejících s úložištěm Azure najdete v tématu [Sledování, diagnostika a řešení potíží s úložištěm Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Fakturace pro analýzu úložiště

Všechna data metrik se zapisují služby účtu úložiště. V důsledku toho je každá operace zápisu provedená službou Storage Analytics fakturovatelná. Kromě toho je možné také fakturovat velikost úložiště používaného daty metrik.

Následující akce prováděné službou Storage Analytics jsou fakturovatelné:

* Požadavky na vytvoření objektů BLOB pro protokolování.
* Požadavky na vytvoření entit tabulky pro metriky.

Pokud jste nakonfigurovali zásady uchovávání dat, nebudou vám účtovány poplatky za odstranění transakcí, když Služba Storage Analytics odstraní stará data protokolování a metriky. Odstranění transakcí z klienta je však fakturovatelné. Další informace o zásadách uchovávání informací naleznete [v tématu Nastavení zásad uchovávání dat služby Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Principy fakturovatelných požadavků

Každá žádost o úložiště účtu je fakturovatelná nebo nefakturovatelná. Storage Analytics protokoluje každý jednotlivý požadavek na službu, včetně zprávy o stavu, která označuje, jak byl požadavek zpracován. Podobně Storage Analytics ukládá metriky pro službu i operace rozhraní API této služby, včetně procent a počtu určitých stavových zpráv. Tyto funkce vám společně mohou pomoci analyzovat fakturovatelné požadavky, vylepšovat aplikaci a diagnostikovat problémy s požadavky na vaše služby. Další informace o fakturaci najdete [v tématu Principy fakturace úložiště Azure – šířka pásma, transakce a kapacita](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

Při pohledu na data Služby Analýzy úložiště můžete pomocí tabulek v tématu [Zaznamenávat operace a stavové zprávy analýzy úložiště](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) k určení, jaké požadavky jsou fakturovatelné. Potom můžete porovnat protokoly a data metrik se stavové zprávy a zjistit, zda vám byly účtovány poplatky za konkrétní požadavek. Tabulky v předchozím tématu můžete také použít k prozkoumání dostupnosti služby úložiště nebo operace individuálního rozhraní API.

## <a name="next-steps"></a>Další kroky
* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Metriky analýzy úložiště](storage-analytics-metrics.md)
* [Protokolování Analýzy úložiště](storage-analytics-logging.md)
