---
title: Referenční informace k datům monitorování úložiště fronty Azure | Microsoft Docs
description: Referenční informace o protokolech a metrikách pro monitorování dat z Azure Queue Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 52ee08e0cce23aebbac1564a5c3ed894d0e487d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711266"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Referenční informace o monitorování úložiště fronty Azure

Podrobnosti o shromažďování a analýze dat monitorování pro Azure Storage najdete v tématu [monitorování Azure Storage](monitor-queue-storage.md) .

## <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro Azure Storage. 

### <a name="capacity-metrics"></a>Kapacitní metriky

Hodnoty metrik kapacity se odesílají do Azure Monitor každou hodinu. Hodnoty se aktualizují každý den. Časové rozpětí definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaná časová zrna pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje v Azure Monitor následující metriky kapacity.

#### <a name="account-level"></a>Úroveň účtu

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Queue Storage

Tato tabulka zobrazuje [metriky úložiště fronty](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices).

| Metrika | Popis |
| ------------------- | ----------------- |
| QueueCapacity | Velikost úložiště fronty, kterou účet úložiště používá <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| QueueCount   | Počet front v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| QueueMessageCount | Přibližný počet zpráv ve frontě v Služba front účtu úložiště. <br/><br/>Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

### <a name="transaction-metrics"></a>Transakční metriky

Metriky transakcí jsou vydávány při každém požadavku na účet úložiště z Azure Storage do Azure Monitor. V případě, že váš účet úložiště neobsahuje žádné aktivity, nebudou v obdobích metrik transakcí žádná data. Všechny transakční metriky jsou k dispozici na úrovni služby úložiště účtů a front. Časové rozpětí definuje časový interval, po který jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny transakční metriky jsou PT1H a PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Storage podporuje pro metriky v Azure Monitor následující dimenze.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Protokoly prostředků (Preview)

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek a účtů Premium Storage v účtech úložiště pro obecné účely V1 a obecné účely v2. Klasické účty úložiště se nepodporují.

V následující tabulce jsou uvedeny vlastnosti Azure Storagech protokolů prostředků při jejich shromažďování v protokolech Azure Monitor nebo Azure Storage. Vlastnosti popisují operaci, službu a typ autorizace, které byly použity k provedení operace.

### <a name="fields-that-describe-the-operation"></a>Pole, která popisují operaci

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pole, která popisují, jak byla operace ověřena

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pole, která popisují službu

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Viz také

- Popis monitorování Azure Storage najdete v tématu [monitorování Azure Queue Storage](monitor-queue-storage.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) .