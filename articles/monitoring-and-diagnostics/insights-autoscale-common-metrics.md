---
title: Běžné metriky automatického škálování
description: Další informace, které metriky běžně se používají pro automatické škálování cloudové služby, virtuální počítače a webové aplikace.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 7b6f454a8d4c8794b8c56494fd9ed573f8b79852
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262235"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure monitorování běžné metriky automatického škálování
Automatické škálování Azure monitorování umožňuje škálovat počet spuštěných instancích směrem nahoru nebo dolů na základě telemetrických dat (metriky). Tento dokument popisuje běžné metriky, které chcete použít. Na portálu Azure pro cloudové služby a serverové farmy můžete prostředku škálovat podle metriky. Můžete však žádné metrika z různých prostředků škálovat podle.

Azure monitorování škálování se vztahují pouze na [sady škálování virtuálního počítače](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), a [služby App Service – webové aplikace](https://azure.microsoft.com/services/app-service/web/). Jinými službami Azure použít různé metody škálování.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Výpočetní metriky pro virtuální počítače na základě Resource Manager
Ve výchozím nastavení virtuální počítače využívající Resource Manager a sady škálování virtuálního počítače emitování základní metriky (na úrovni hostitele). Kromě toho při konfiguraci shromažďování dat diagnostiky pro virtuální počítač Azure a VMSS rozšíření diagnostiky Azure také vysílá čítače výkonu hostovaného operačního systému (často označované jako "Hostovaného operačního systému metrik").  Všechny tyto metriky můžete používat v pravidlech automatického škálování.

Můžete použít `Get MetricDefinitions` rozhraní API nebo PoSH nebo rozhraní příkazového řádku zobrazíte dostupné VMSS prostředku metriky.

Pokud používáte škálovatelné sady virtuálních počítačů a nevidíte konkrétní metrika uvedené, pak je pravděpodobné, *zakázáno* ve vašem rozšíření diagnostiky.

Pokud není konkrétní metrika vzorkovat nebo přenést s frekvencí chcete, můžete aktualizovat konfiguraci diagnostiky.

Pokud se buď předchozím případě je hodnota true, zkontrolujte [pomocí Powershellu povolit v virtuálního počítače se systémem Windows Azure Diagnostics](../virtual-machines/windows/ps-extensions-diagnostics.md) o prostředí PowerShell ke konfiguraci a aktualizovat vaše rozšíření diagnostiky virtuálního počítače Azure umožňující metriku. Tento článek obsahuje také ukázkový soubor konfigurace diagnostiky.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metriky hostitele založené na správci prostředků systému Windows a virtuální počítače s Linuxem
Následující metriky úrovni hostitele jsou vygenerované ve výchozím nastavení pro virtuální počítač Azure a VMSS v systému Windows a Linux instancí. Tyto metriky popisují svého virtuálního počítače Azure, ale jsou shromažďovány z hostitele virtuálního počítače Azure a nikoli prostřednictvím agent nainstalovaný na virtuálním počítači hosta. Tyto metriky můžete používat v pravidlech automatického škálování.

- [Metriky hostitele založené na správci prostředků systému Windows a virtuální počítače s Linuxem](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Metriky hostitele založené na správci prostředků systému Windows a škálovatelné sady virtuálních počítačů Linux](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Metriky hostovaného operačního systému založené na správci prostředků virtuálních počítačů Windows
Když vytvoříte virtuální počítač v Azure, je povolený diagnostiky pomocí rozšíření diagnostiky. Rozšíření diagnostiky vysílá sadu metriky, které jsou převzaty z uvnitř virtuálního počítače. To znamená, že můžete použít automatické škálování z metriky, které nejsou vygenerované ve výchozím nastavení.

Seznam metriky můžete vygenerovat pomocí následujícího příkazu v prostředí PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procento |
| \Processor(_Total)\% privilegovaného času |Procento |
| \Processor(_Total)\% uživatelského času |Procento |
| Frekvence \Processor \Processor informace (_celkem) |Počet |
| \System\Processes |Počet |
| Počet \Thread \Process (_celkem) |Počet |
| Počet \Handle \Process (_celkem) |Počet |
| \Memory\% využití potvrzených bajtů |Procento |
| \Memory\Available Bytes |B |
| \Memory\Committed bajtů |B |
| \Memory\Commit limit |B |
| \Memory\Pool stránkovaného fondu |B |
| \Memory\Pool nestránkovaného fondu |B |
| \PhysicalDisk(_Total)\% čas na disku |Procento |
| \PhysicalDisk(_Total)\% čas čtení disku |Procento |
| \PhysicalDisk(_Total)\% doba zápisu na disku |Procento |
| Přenosy \Disk \PhysicalDisk (_celkem) za sekundu |CountPerSecond |
| Čtení \Disk \PhysicalDisk (_celkem) za sekundu |CountPerSecond |
| \PhysicalDisk (_celkem) \Disk zápisů za sekundu |CountPerSecond |
| \PhysicalDisk (_celkem) \Disk bajty/s |BytesPerSecond |
| Číst bajty/s \Disk \PhysicalDisk (_celkem) |BytesPerSecond |
| \PhysicalDisk (_celkem) \Disk zápisu bajty/s |BytesPerSecond |
| \Avg \PhysicalDisk (_celkem). Délka fronty disku |Počet |
| \Avg \PhysicalDisk (_celkem). Délka fronty disku pro čtení |Počet |
| \Avg \PhysicalDisk (_celkem). Délka fronty disku zápisu |Počet |
| \LogicalDisk(_Total)\% volného místa |Procento |
| Megabajtech \Free \LogicalDisk (_celkem) |Počet |

### <a name="guest-os-metrics-linux-vms"></a>Metriky hostovaného operačního systému Linux virtuální počítače
Když vytvoříte virtuální počítač v Azure, povolí se Diagnostika pomocí rozšíření diagnostiky ve výchozím nastavení.

Seznam metriky můžete vygenerovat pomocí následujícího příkazu v prostředí PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Memory\AvailableMemory |B |
| \Memory\PercentAvailableMemory |Procento |
| \Memory\UsedMemory |B |
| \Memory\PercentUsedMemory |Procento |
| \Memory\PercentUsedByCache |Procento |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |B |
| \Memory\PercentAvailableSwap |Procento |
| \Memory\UsedSwap |B |
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
| \NetworkInterface\BytesTransmitted |B |
| \NetworkInterface\BytesReceived |B |
| \NetworkInterface\PacketsTransmitted |Počet |
| \NetworkInterface\PacketsReceived |Počet |
| \NetworkInterface\BytesTotal |B |
| \NetworkInterface\TotalRxErrors |Počet |
| \NetworkInterface\TotalTxErrors |Počet |
| \NetworkInterface\TotalCollisions |Počet |

## <a name="commonly-used-web-server-farm-metrics"></a>Běžně používané metriky webové (serverové farmě)
Můžete také provést automatické škálování podle běžné webové metriky serveru jako je délka fronty Http. Metriky název je **HttpQueueLength**.  V následující části jsou uvedeny metriky dostupný server farmy (webové aplikace).

### <a name="web-apps-metrics"></a>Webové aplikace metriky
Seznam webových aplikací metriky můžete vygenerovat pomocí následujícího příkazu v prostředí PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete výstrahy na nebo škálovat podle tyto metriky.

| Název metriky | Jednotka |
| --- | --- |
| CpuPercentage |Procento |
| MemoryPercentage |Procento |
| DiskQueueLength |Počet |
| HttpQueueLength |Počet |
| BytesReceived |B |
| BytesSent |B |

## <a name="commonly-used-storage-metrics"></a>Běžně používané metriky úložiště
Je možné škálovat podle délka fronty úložiště, což je počet zpráv ve frontě úložiště. Délka fronty úložiště je speciální metriky a prahová hodnota je počet zpráv na jednu instanci. Například pokud existují dvě instance, a pokud je prahová hodnota nastavená na 100, škálování nastane, když je celkový počet zpráv ve frontě 200. Který může být 100 zprávy na jednu instanci, 120 a 80 nebo jakoukoli jinou kombinaci, přidá až 200 nebo víc.

Konfigurací tohoto nastavení na portálu Azure v **nastavení** okno. Pro škálovatelné sady virtuálních počítačů, můžete aktualizovat nastavení škálování v šabloně Resource Manager, můžete používat *metricName* jako *ApproximateMessageCount* a předejte ID fronty úložiště jako *metricResourceUri*.

Například by metricTrigger nastavení automatického škálování s Classic účet úložiště patří:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Pro účet úložiště (ne klasický) by mělo zahrnovat metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Běžně používané metriky služby Service Bus
Je možné škálovat podle délka fronty Service Bus, která je počet zpráv ve frontě Service Bus. Délka fronty Service Bus je speciální metriky a prahová hodnota je počet zpráv na jednu instanci. Například pokud existují dvě instance, a pokud je prahová hodnota nastavená na 100, škálování nastane, když je celkový počet zpráv ve frontě 200. Který může být 100 zprávy na jednu instanci, 120 a 80 nebo jakoukoli jinou kombinaci, přidá až 200 nebo víc.

Pro škálovatelné sady virtuálních počítačů, můžete aktualizovat nastavení škálování v šabloně Resource Manager, můžete používat *metricName* jako *ApproximateMessageCount* a předejte ID fronty úložiště jako *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Služba Service Bus neexistuje koncept skupiny prostředků, ale Azure Resource Manager vytvoří výchozí skupinu prostředků podle oblastí. Skupina prostředků je obvykle ve formátu "Default - ServiceBus-[Oblast]". Například: 'Výchozí-ServiceBus-EastUS', 'Výchozí-ServiceBus-WestUS', 'výchozí-ServiceBus-AustraliaEast"atd.
>
>
