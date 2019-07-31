---
title: Automatické škálování běžných metrik
description: Zjistěte, které metriky se běžně používají k automatickému škálování Cloud Services, Virtual Machines a Web Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 9da8e5fb88ff34e561b579b760973ecd23c884a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129735"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor automatické škálování běžných metrik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor automatické škálování umožňuje škálovat počet spuštěných instancí nahoru nebo dolů na základě dat telemetrie (metriky). Tento dokument popisuje běžné metriky, které byste mohli chtít použít. V Azure Portal můžete zvolit metriku prostředku, podle které se má škálovat. Můžete ale také zvolit libovolnou metriku z jiného prostředku a škálovat ji.

Automatické škálování Azure Monitor platí jenom pro služby [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Jiné služby Azure používají jiné metody škálování.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Výpočetní metriky pro virtuální počítače založené na Správce prostředků
Ve výchozím nastavení Správce prostředků Virtual Machines a Virtual Machine Scale Sets emitují metriky Basic (na úrovni hostitele). Kromě toho, když konfigurujete shromažďování dat diagnostiky pro virtuální počítač Azure a VMSS, rozhraní pro diagnostiku Azure také generuje čítače výkonu host-OS (obvykle označované jako metriky host-OS).  Všechny tyto metriky použijete v pravidlech automatického škálování.

Pomocí `Get MetricDefinitions` rozhraní API/PoSH/CLI můžete zobrazit metriky, které jsou k dispozici pro váš prostředek VMSS.

Pokud používáte službu VM Scale Sets a v seznamu není uvedena konkrétní metrika, je pravděpodobně *zakázána* v diagnostickém rozšíření.

Pokud není určitá metrika vzorkovaná nebo převedená podle četnosti, kterou chcete, můžete aktualizovat konfiguraci diagnostiky.

Pokud má předchozí případ hodnotu true, přečtěte si téma [použití PowerShellu k povolení Azure Diagnostics ve virtuálním počítači s Windows](../../virtual-machines/extensions/diagnostics-windows.md) o PowerShellu ke konfiguraci a aktualizaci rozšíření DIAGNOSTIKy virtuálního počítače Azure, aby se aktivovala Tato metrika. Tento článek také obsahuje ukázkový konfigurační soubor diagnostiky.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metriky hostitele pro virtuální počítače s Windows a Linuxem na Správce prostředků
Následující metriky na úrovni hostitele se ve výchozím nastavení generují pro virtuální počítače Azure a VMSS v instancích systému Windows i Linux. Tyto metriky popisují váš virtuální počítač Azure, ale jsou shromážděny z hostitele virtuálního počítače Azure, nikoli prostřednictvím agenta nainstalovaného na virtuálním počítači hosta. Tyto metriky můžete používat v pravidlech automatického škálování.

- [Metriky hostitele pro virtuální počítače s Windows a Linuxem na Správce prostředků](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Metriky hostitele pro Správce prostředků se systémem Windows a Linux VM Scale Sets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Metriky hostovaného operačního systému Správce prostředků virtuálních počítačů s Windows na bázi
Když vytvoříte virtuální počítač v Azure, diagnostika je povolená pomocí diagnostického rozšíření. Diagnostické rozšíření emituje sadu metrik, které se provedou uvnitř virtuálního počítače. To znamená, že můžete automaticky škálovat metriky, které nejsou ve výchozím nastavení emitované.

Seznam metrik můžete vygenerovat pomocí následujícího příkazu v PowerShellu.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percent |
| \Processor (_Total)\% privilegovaného času |Percent |
| \Processor (_Total)\% uživatelského času |Percent |
| \Processor informace (_Total) \Processor frekvence |Count |
| \System\Processes |Count |
| \Process (_Total) \Thread počet |Count |
| \Process (_Total) \Handle počet |Count |
| \Memory\% potvrzené používané bajty |Percent |
| \Memory\Available Bytes |B |
| \Memory\Committed bajty |B |
| \Memory\Commit limit |B |
| Bajty stránkovaného \Memory\Pool |B |
| \Memory\Pool nestránkované bajty |B |
| \PhysicalDisk (_Total)\% čas disku |Percent |
| \PhysicalDisk (_Total)\% doba čtení disku |Percent |
| \PhysicalDisk (_Total)\% doba zápisu na disk |Percent |
| \PhysicalDisk (_Total) \ přenosy za sekundu |CountPerSecond |
| \PhysicalDisk (_Total) \ čtení za sekundu |CountPerSecond |
| \PhysicalDisk (_Total) \ zápisu za sekundu |CountPerSecond |
| \PhysicalDisk (_Total) \ bajty/s |BytesPerSecond |
| \PhysicalDisk (_Total) \ čtení v bajtech/s |BytesPerSecond |
| \PhysicalDisk (_Total) \ zapsané bajty/s |BytesPerSecond |
| \PhysicalDisk (_Total) \ prům Délka fronty disku |Count |
| \PhysicalDisk (_Total) \ prům Délka fronty čtení disku |Count |
| \PhysicalDisk (_Total) \ prům Délka fronty zápisu na disk |Count |
| \ Logický disk (_Total)\% volného místa |Percent |
| \ Logický disk (_Total) \ volné megabajtů |Count |

### <a name="guest-os-metrics-linux-vms"></a>Virtuální počítače s metrikami hostovaného operačního systému Linux
Když vytvoříte virtuální počítač v Azure, diagnostika je ve výchozím nastavení povolená pomocí diagnostického rozšíření.

Seznam metrik můžete vygenerovat pomocí následujícího příkazu v PowerShellu.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Memory\AvailableMemory |B |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |B |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |B |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |B |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Sekundy |
| \PhysicalDisk\AverageWriteTime |Sekundy |
| \PhysicalDisk\AverageTransferTime |Sekundy |
| \PhysicalDisk\AverageDiskQueueLength |Count |
| \NetworkInterface\BytesTransmitted |B |
| \NetworkInterface\BytesReceived |B |
| \NetworkInterface\PacketsTransmitted |Count |
| \NetworkInterface\PacketsReceived |Count |
| \NetworkInterface\BytesTotal |B |
| \NetworkInterface\TotalRxErrors |Count |
| \NetworkInterface\TotalTxErrors |Count |
| \NetworkInterface\TotalCollisions |Count |

## <a name="commonly-used-web-server-farm-metrics"></a>Metriky běžně používaného webu (serverové farmy)
Automatické škálování můžete provádět i na základě běžných metrik webového serveru, jako je délka fronty http. Název metriky je **HttpQueueLength**.  V následující části jsou uvedeny dostupné metriky serverové farmy (Web Apps).

### <a name="web-apps-metrics"></a>Web Apps metriky
Seznam metrik Web Apps můžete vygenerovat pomocí následujícího příkazu v PowerShellu.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pomocí těchto metrik můžete výstrahy zapnout nebo škálovat.

| Název metriky | Jednotka |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Count |
| HttpQueueLength |Count |
| BytesReceived |B |
| BytesSent |B |

## <a name="commonly-used-storage-metrics"></a>Běžně používané metriky úložiště
Můžete škálovat podle délky fronty úložiště, což je počet zpráv ve frontě úložiště. Délka fronty úložiště je speciální metrika a prahová hodnota je počet zpráv na instanci. Například pokud existují dvě instance a pokud je prahová hodnota nastavená na 100, škálování se objeví, pokud je celkový počet zpráv ve frontě 200. Může to být 100 zpráv na instanci, 120 a 80 nebo jakákoli jiná kombinace, která přidává až 200 nebo více.

Nakonfigurujte toto nastavení v okně Azure Portal v okně **Nastavení** . Pro službu VM Scale Sets můžete aktualizovat nastavení automatického škálování v šabloně Správce prostředků tak, aby se použila hodnota *metric* jako *ApproximateMessageCount* , a předat ID fronty úložiště jako *metricResourceUri*.

Například s klasickým účtem úložiště může metricTrigger nastavení automatického škálování zahrnovat:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

V případě účtu úložiště (neklasického) by měl metricTrigger zahrnovat:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Běžně používané Service Bus metriky
Můžete škálovat podle Service Bus délky fronty, což je počet zpráv ve frontě Service Bus. Service Bus délka fronty je speciální metrika a prahová hodnota je počet zpráv na instanci. Například pokud existují dvě instance a pokud je prahová hodnota nastavená na 100, škálování se objeví, pokud je celkový počet zpráv ve frontě 200. Může to být 100 zpráv na instanci, 120 a 80 nebo jakákoli jiná kombinace, která přidává až 200 nebo více.

Pro službu VM Scale Sets můžete aktualizovat nastavení automatického škálování v šabloně Správce prostředků tak, aby se použila hodnota *metric* jako *ApproximateMessageCount* , a předat ID fronty úložiště jako *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Pro Service Bus koncept skupiny prostředků neexistuje, ale Azure Resource Manager vytvoří výchozí skupinu prostředků pro oblast. Skupina prostředků je obvykle ve formátu výchozí-ServiceBus-[region]. Například "default-ServiceBus-EastUS", "default-ServiceBus-WestUS", "default-ServiceBus-AustraliaEast" atd.
>
>

