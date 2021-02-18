---
title: Automatické škálování běžných metrik
description: Zjistěte, které metriky se běžně používají k automatickému škálování Cloud Services, Virtual Machines a Web Apps.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: f358efb8f99066dc1d7a564ad36d6ed1290853e0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610949"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor automatické škálování běžných metrik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor automatické škálování umožňuje škálovat počet spuštěných instancí nahoru nebo dolů na základě dat telemetrie (metriky). Tento dokument popisuje běžné metriky, které byste mohli chtít použít. V Azure Portal můžete zvolit metriku prostředku, podle které se má škálovat. Můžete ale také zvolit libovolnou metriku z jiného prostředku a škálovat ji.

Automatické škálování Azure Monitor platí jenom pro služby [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management](../../api-management/api-management-key-concepts.md). Jiné služby Azure používají jiné metody škálování.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Výpočetní metriky pro virtuální počítače založené na Správce prostředků
Ve výchozím nastavení Správce prostředků Virtual Machines a Virtual Machine Scale Sets emitují metriky Basic (na úrovni hostitele). Kromě toho, když konfigurujete shromažďování dat diagnostiky pro virtuální počítač Azure a VMSS, rozhraní pro diagnostiku Azure také generuje čítače výkonu host-OS (obvykle označované jako metriky host-OS).  Všechny tyto metriky použijete v pravidlech automatického škálování.

Pomocí `Get MetricDefinitions` rozhraní API/PoSH/CLI můžete zobrazit metriky, které jsou k dispozici pro váš prostředek VMSS.

Pokud používáte službu VM Scale Sets a v seznamu není uvedena konkrétní metrika, je pravděpodobně *zakázána* v diagnostickém rozšíření.

Pokud není určitá metrika vzorkovaná nebo převedená podle četnosti, kterou chcete, můžete aktualizovat konfiguraci diagnostiky.

Pokud má předchozí případ hodnotu true, přečtěte si téma [použití PowerShellu k povolení Azure Diagnostics ve virtuálním počítači s Windows](../../virtual-machines/extensions/diagnostics-windows.md) o PowerShellu ke konfiguraci a aktualizaci rozšíření DIAGNOSTIKy virtuálního počítače Azure, aby se aktivovala Tato metrika. Tento článek také obsahuje ukázkový konfigurační soubor diagnostiky.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metriky hostitele pro virtuální počítače s Windows a Linuxem na Správce prostředků
Následující metriky na úrovni hostitele se ve výchozím nastavení generují pro virtuální počítače Azure a VMSS v instancích systému Windows i Linux. Tyto metriky popisují váš virtuální počítač Azure, ale jsou shromážděny z hostitele virtuálního počítače Azure, nikoli prostřednictvím agenta nainstalovaného na virtuálním počítači hosta. Tyto metriky můžete používat v pravidlech automatického škálování.

- [Metriky hostitele pro virtuální počítače s Windows a Linuxem na Správce prostředků](../platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Metriky hostitele pro Správce prostředků se systémem Windows a Linux VM Scale Sets](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Metriky hostovaného operačního systému pro virtuální počítače s Windows založené na Správce prostředků
Když vytvoříte virtuální počítač v Azure, diagnostika je povolená pomocí diagnostického rozšíření. Diagnostické rozšíření emituje sadu metrik, které se provedou uvnitř virtuálního počítače. To znamená, že můžete automaticky škálovat metriky, které nejsou ve výchozím nastavení emitované.

Seznam metrik můžete vygenerovat pomocí následujícího příkazu v PowerShellu.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procento |
| \Processor (_Total) \% privilegovaný čas |Procento |
| Čas uživatele \Processor (_Total) \% |Procento |
| \Processor – informace o frekvenci \Processor (_Total) |Počet |
| \System\Processes |Počet |
| \Process (_Total) \Thread počet |Počet |
| \Process (_Total) \Handle počet |Počet |
| \Memory \% potvrzené používané bajty |Procento |
| \Memory\Available Bytes |Bajty |
| \Memory\Committed bajty |Bajty |
| \Memory\Commit limit |Bajty |
| Bajty stránkovaného \Memory\Pool |Bajty |
| \Memory\Pool nestránkované bajty |Bajty |
| \PhysicalDisk (_Total) \% čas disku |Procento |
| \PhysicalDisk (_Total) \% doba čtení disku |Procento |
| \PhysicalDisk (_Total) \% čas zápisu na disk |Procento |
| \PhysicalDisk (_Total) \ přenosy za sekundu |CountPerSecond |
| \PhysicalDisk (_Total) \ čtení za sekundu |CountPerSecond |
| \PhysicalDisk (_Total) \ zápisy na/s |CountPerSecond |
| \PhysicalDisk (_Total) \ bajty/s |BytesPerSecond |
| \PhysicalDisk (_Total) \ přečtené bajty/s |BytesPerSecond |
| \PhysicalDisk (_Total) \ zapsané bajty/s |BytesPerSecond |
| \PhysicalDisk (_Total) \ prům délka fronty disku |Počet |
| \PhysicalDisk (_Total) \ prům délka fronty čtení disku |Počet |
| \PhysicalDisk (_Total) \ prům délka fronty zápisu na disk |Počet |
| \ Logický disk (_Total) \% volného místa |Procento |
| \ Logický disk (_Total) \ volné megabajtů |Počet |

### <a name="guest-os-metrics-linux-vms"></a>Virtuální počítače s metrikami hostovaného operačního systému Linux
Když vytvoříte virtuální počítač v Azure, diagnostika je ve výchozím nastavení povolená pomocí diagnostického rozšíření.

Seznam metrik můžete vygenerovat pomocí následujícího příkazu v PowerShellu.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Memory\AvailableMemory |Bajty |
| \Memory\PercentAvailableMemory |Procento |
| \Memory\UsedMemory |Bajty |
| \Memory\PercentUsedMemory |Procento |
| \Memory\PercentUsedByCache |Procento |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bajty |
| \Memory\PercentAvailableSwap |Procento |
| \Memory\UsedSwap |Bajty |
| \Memory\PercentUsedSwap |Procento |
| \Processor\PercentIdleTime |Procento |
| \Processor\PercentUserTime |Procento |
| \Processor\PercentNiceTime |Procento |
| \Processor\PercentPrivilegedTime |Procento |
| \Processor\PercentInterruptTime |Procento |
| \Processor\PercentDPCTime |Procento |
| \Processor\PercentProcessorTime |Procento |
| \Processor\PercentIOWaitTime |Procento |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Sekundy |
| \PhysicalDisk\AverageWriteTime |Sekundy |
| \PhysicalDisk\AverageTransferTime |Sekundy |
| \PhysicalDisk\AverageDiskQueueLength |Počet |
| \NetworkInterface\BytesTransmitted |Bajty |
| \NetworkInterface\BytesReceived |Bajty |
| \NetworkInterface\PacketsTransmitted |Počet |
| \NetworkInterface\PacketsReceived |Počet |
| \NetworkInterface\BytesTotal |Bajty |
| \NetworkInterface\TotalRxErrors |Počet |
| \NetworkInterface\TotalTxErrors |Počet |
| \NetworkInterface\TotalCollisions |Počet |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Běžně používaná metrika App Service (serverová farma)
Automatické škálování můžete provádět i na základě běžných metrik webového serveru, jako je délka fronty http. Jeho název metriky je **HttpQueueLength**.  V následující části jsou uvedeny dostupné metriky serverové farmy (App Service).

### <a name="web-apps-metrics"></a>Web Apps metriky
Seznam metrik Web Apps můžete vygenerovat pomocí následujícího příkazu v PowerShellu.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pomocí těchto metrik můžete výstrahy zapnout nebo škálovat.

| Název metriky | Jednotka |
| --- | --- |
| CpuPercentage |Procento |
| MemoryPercentage |Procento |
| DiskQueueLength |Počet |
| HttpQueueLength |Počet |
| BytesReceived |Bajty |
| BytesSent |Bajty |

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
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Pro Service Bus koncept skupiny prostředků neexistuje, ale Azure Resource Manager vytvoří výchozí skupinu prostředků pro oblast. Skupina prostředků je obvykle ve formátu výchozí-ServiceBus-[region]. Například "default-ServiceBus-EastUS", "default-ServiceBus-WestUS", "default-ServiceBus-AustraliaEast" atd.
>
>

