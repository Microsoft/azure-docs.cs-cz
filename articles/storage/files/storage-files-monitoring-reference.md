---
title: Referenční informace o datech monitorování služby Azure Files | Microsoft Docs
description: Reference k protokolům a metrikám pro monitorování dat ze souborů Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 4575709a4fa7067b02228036fb2e2b4a60844e21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592313"
---
# <a name="azure-files-monitoring-data-reference"></a>Referenční informace o datech monitorování služby Azure Files

Podrobnosti o shromažďování a analýze dat monitorování pro soubory Azure najdete v tématu [monitorování služby Azure Files](storage-files-monitoring.md) .

## <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro soubory Azure. 

### <a name="capacity-metrics"></a>Kapacitní metriky

Hodnoty metrik kapacity se aktualizují každý den (až 24 hodin). Časové rozpětí definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaná časová zrna pro všechny metriky kapacity je jedna hodina (PT1H).

Služby soubory Azure poskytují v Azure Monitor následující metriky kapacity.

#### <a name="account-level"></a>Úroveň účtu

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

Tato tabulka zobrazuje [metriky souborů Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Metric | Popis |
| ------------------- | ----------------- |
| Kapacita zařízení | Velikost úložiště souborů, kterou používá účet úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| FileCount   | Počet souborů v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| FileShareCapacityQuota | Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024|
| FileShareCount | Počet sdílených složek v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| FileShareProvisionedIOPS | Počet zřízených vstupně-výstupních operací ve sdílené složce souborů. Tato metrika se vztahuje pouze na prémiové úložiště souborů. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr |
| FileShareSnapshotCount | Počet snímků, které se nachází ve sdílené složce ve službě Azure Files v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr | 
|FileShareSnapshotSize|Velikost úložiště využitá snímky ve službě soubory Azure v účtu úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr|

### <a name="transaction-metrics"></a>Transakční metriky

Metriky transakcí jsou vydávány při každém požadavku na účet úložiště z Azure Storage do Azure Monitor. V případě, že váš účet úložiště neobsahuje žádné aktivity, nebudou v obdobích metrik transakcí žádná data. Všechny transakční metriky jsou k dispozici na úrovni služby a Azure Files. Časové rozpětí definuje časový interval, po který jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny transakční metriky jsou PT1H a PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimenze metrik

Soubory Azure podporují pro metriky v Azure Monitor následující dimenze.

> [!NOTE] 
> Dimenze sdílení souborů není dostupná pro standardní sdílené složky (jenom pro soubory úrovně Premium). Pokud používáte standardní sdílené složky, jsou dostupné metriky pro všechny sdílené složky v účtu úložiště. Pokud chcete získat metriky pro standardní sdílené složky, vytvořte jednu sdílenou složku pro každý účet úložiště.

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

- Popis monitorování Azure Storage najdete v tématu [monitorování služby soubory Azure](storage-files-monitoring-reference.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .