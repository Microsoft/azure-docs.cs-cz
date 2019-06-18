---
title: Použití služby Azure Storage analytics ke shromažďování dat protokolů a metrik | Dokumentace Microsoftu
description: Analýza úložiště umožňuje sledovat data metriky pro všechny služby storage a shromáždí protokoly pro úložiště objektů Blob, Queue a Table.
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: b588ebe577e61014c6c2bbeaae751b2783dd6f80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153926"
---
# <a name="storage-analytics"></a>Storage Analytics

Analýza Azure Storage provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště.

Chcete-li použít funkci analýzy úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [webu Azure portal](https://portal.azure.com). Podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Použití [nastavit vlastnosti služby Blob Service](/rest/api/storageservices/set-blob-service-properties), [nastavit vlastnosti služby Queue](/rest/api/storageservices/set-queue-service-properties), [nastavovat vlastnosti služby tabulky](/rest/api/storageservices/set-table-service-properties), a [nastavit File Service Properties](/rest/api/storageservices/Get-File-Service-Properties)operace povolující analytika úložiště pro každou službu.

Agregovaná data se ukládají v dobře známý objekt blob (pro protokolování) a dobře známých tabulek (pro metriky), které lze získat přístup pomocí služby Blob service a Table service rozhraní API.

Analýza úložiště má limit 20 TB na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Další informace o omezeních účtů úložišť najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md).

Podrobný průvodce k identifikaci, diagnostice a řešit problémy související s Azure Storage pomocí Storage Analytics a další nástroje, najdete v části [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Fakturace za Storage Analytics

Všechna data metriky se zapisují službami účtu úložiště. V důsledku toho se dají fakturovat každou operaci zápisu provádět analýzy úložiště. Kromě toho je velikost úložiště využitá službou můžete například měřená data také fakturovatelný.

Následující akce prováděné analytika úložiště se účtují:

* Požadavky na vytvoření objektů blob pro protokolování.
* Požadavky na vytvoření entity tabulky pro metriky.

Pokud jste nakonfigurovali zásady uchovávání dat, se neúčtuje transakce odstranění odstraní stará data protokolování a metrik Storage Analytics. Transakce odstranění z klienta se ale účtují. Další informace o zásadách uchovávání dat najdete v tématu [nastavení zásady uchovávání dat Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Principy fakturovatelných požadavků

Každou žádost odeslanou účet služby storage je fakturovatelná nebo Nefakturovatelný. Analýza úložiště protokolů každý jednotlivý požadavek na službu, včetně stavovou zprávu, která určuje, jak se zpracovává žádost. Podobně analytika úložiště ukládá metriky pro službu a rozhraní API operace této služby, včetně procenta a počet určitých stavových zpráv. Společně tyto funkce vám pomohou analyzovat fakturovatelných požadavků, vylepšování na vaše aplikace a diagnostikovat problémy s žádostí o služby. Další informace o fakturaci najdete v tématu [Principy Azure Storage fakturace – šířka pásma, transakce a kapacita](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Při prohlížení dat Storage Analytics, můžete použít tabulky v [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) tématu můžete určit, jaké požadavky se účtují. Pak můžete porovnat vaše protokoly a data metriky pro stavové zprávy zobrazíte, pokud se vám účtovat určité žádosti. Tabulky v předchozím tématu můžete použít také k nalezení dostupnosti pro službu úložiště nebo jednotlivé operace rozhraní API.

## <a name="next-steps"></a>Další postup
* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Metrikách Storage Analytics](storage-analytics-metrics.md)
* [Protokolování Storage Analytics](storage-analytics-logging.md)
