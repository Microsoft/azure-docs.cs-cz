---
title: Referenční informace ke službě Azure Queue Storage monitoring data
description: Reference k protokolům a metrikám pro monitorování dat z Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 506f5a46688f597b8ac5db341c5bbe5eb5fb67c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763140"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Referenční informace ke službě Azure Queue Storage monitoring data

Podrobnosti o shromažďování a analýze dat monitorování pro Azure Storage najdete v tématu [monitorování Azure Storage](monitor-queue-storage.md) .

## <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro Azure Storage.

### <a name="capacity-metrics"></a>Kapacitní metriky

Hodnoty metrik kapacity se aktualizují každý den (až 24 hodin). Časové rozpětí definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaná časová zrna pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje v Azure Monitor následující metriky kapacity.

#### <a name="account-level-capacity-metrics"></a>Metriky kapacity na úrovni účtu

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage metriky

Tato tabulka ukazuje [Queue Storage metriky](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Metric | Popis |
| ------------------- | ----------------- |
| **QueueCapacity** | Množství Queue Storage používaného účtem úložiště <br><br> Jednotce `Bytes` <br> Typ agregace: `Average` <br> Příklad hodnoty: `1024` |
| **QueueCount** | Počet front v účtu úložiště. <br><br> Jednotce `Count` <br> Typ agregace: `Average` <br> Příklad hodnoty: `1024` |
| **QueueMessageCount** | Počet zpráv ve frontě s neplatným vypršením platnosti v účtu úložiště <br><br> Jednotce `Count` <br> Typ agregace: `Average` <br> Příklad hodnoty: `1024` |

### <a name="transaction-metrics"></a>Transakční metriky

Metriky transakcí jsou vydávány při každém požadavku na účet úložiště z Azure Storage do Azure Monitor. V případě, že váš účet úložiště neobsahuje žádné aktivity, nebudou v obdobích metrik transakcí žádná data. Všechny transakční metriky jsou dostupné jak na úrovni účtu, tak na úrovni služby Queue Storage. Časové rozpětí definuje časový interval, po který jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny transakční metriky jsou PT1H a PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Storage podporuje pro metriky v Azure Monitor následující dimenze.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Protokoly prostředků (Preview)

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek a účtů Premium Storage v účtech úložiště pro obecné účely V1 a obecné účely v2. Klasické účty úložiště se nepodporují.

V následující tabulce jsou uvedeny vlastnosti Azure Storagech protokolů prostředků při jejich shromažďování v protokolech Azure Monitor nebo Azure Storage. Vlastnosti popisují operaci, službu a typ autorizace, které byly použity k provedení operace.

### <a name="fields-that-describe-the-operation"></a>Pole, která popisují operaci

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pole, která popisují, jak byla operace ověřena

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pole, která popisují službu

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Viz také

- Popis monitorování Azure Queue Storage najdete v tématu [monitorování azure Queue Storage](monitor-queue-storage.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .
