---
title: Běžné metriky automatického škálování
description: Další informace, které metriky se obvykle používají pro automatické škálování služby Cloud Services, Virtual Machines a Web Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 736ff5565bb279d26e686421cc13f54a73b1c7e9
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461089"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor běžné metriky automatického škálování
Automatické škálování Azure Monitor umožňuje škálování více spuštěných instancích směrem nahoru nebo dolů, na základě telemetrických dat (metriky). Tento dokument popisuje běžné metriky, které chcete použít. Na webu Azure Portal můžete zvolit metrika prostředku, který chcete škálovat. Ale můžete také jakékoliv metriky z různých zdrojů škálovat podle.

Automatické škálování služby Azure Monitor se týká pouze [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), a [služby API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Jiným službám Azure použít různé metody škálování.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Vypočítat metriky pro virtuální počítače založené na Resource Manageru
Ve výchozím nastavení virtuální počítače využívající Resource Manager a Virtual Machine Scale Sets generování základní metriky (na úrovni hostitele). Kromě toho při konfiguraci shromažďování dat diagnostiky pro virtuální počítač Azure a VMSS se diagnostické rozšíření Azure také vysílá čítače výkonu hostovaný operační systém (často označované jako "Hostovaného operačního systému metrik").  Tyto metriky se používá pravidla automatického škálování.

Můžete použít `Get MetricDefinitions` rozhraní API/PoSH/CLI, pokud chcete zobrazit metriky, které jsou k dispozici pro váš prostředek VMSS.

Pokud používáte škálovací sady virtuálních počítačů a nevidíte konkrétní metriky uvedeny, pak bude pravděpodobně *zakázané* v diagnostické rozšíření.

Pokud není konkrétní metriky vzorkovány nebo přenesená frekvencí chcete, můžete aktualizovat konfiguraci diagnostiky.

Pokud obou předchozích případech má hodnotu true, pak si projděte [použití Powershellu k povolení diagnostiky Azure v rámci virtuálního počítače se systémem Windows](../../virtual-machines/extensions/diagnostics-windows.md) o použití Powershellu ke konfiguraci a aktualizovat vaše rozšíření diagnostiku virtuálních počítačů Azure pro umožnění metriku. Tento článek také obsahuje ukázkový soubor konfigurace diagnostiky.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metriky hostitele pro Windows využívající Resource Manager a virtuální počítače s Linuxem
Následující metriky na úrovni hostitele jsou emitovány ve výchozím nastavení pro virtuální počítač Azure a VMSS ve Windows i Linuxem instancí. Tyto metriky popisují váš virtuální počítač Azure, ale se shromažďují z hostitele virtuálního počítače Azure, nikoli přes agenta nainstalovaného na virtuálním počítači hosta. Tyto metriky můžete použít v pravidlech automatického škálování.

- [Metriky hostitele pro Windows využívající Resource Manager a virtuální počítače s Linuxem](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Metriky hostitele založeného na správci prostředků Windows a Linux VM Scale Sets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Metriky hostovaného operačního systému virtuálních počítačů Windows využívající Resource Manager
Při vytváření virtuálního počítače v Azure diagnostics je povoleno pomocí diagnostického rozšíření. Rozšíření diagnostiky generuje sadu metriky přijatá z uvnitř virtuálního počítače. To znamená, že je možné automaticky škálovat z metriky, které nejsou ve výchozím nastavení zaznamenávány.

Seznam metrik lze vytvořit pomocí následujícího příkazu v Powershellu.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procento |
| \Processor(_Total)\% privilegovaného času |Procento |
| \Processor(_Total)\% čas uživatele |Procento |
| Frekvence \Processor \Processor informace o (využití _celkem) |Počet |
| \System\Processes |Počet |
| Počet \Thread \Process (využití _celkem) |Počet |
| Počet \Handle \Process (využití _celkem) |Počet |
| \Memory\% využívání svěřených bajtů |Procento |
| \Memory\Available Bytes |B |
| \Memory\Committed bajtů |B |
| \Memory\Commit limit |B |
| \Memory\Pool stránkovaného fondu |B |
| \Memory\Pool nestránkovaného |B |
| \PhysicalDisk(_Total)\% čas na disku |Procento |
| \PhysicalDisk(_Total)\% čas čtení disku |Procento |
| \PhysicalDisk(_Total)\% čas zápisu na disku |Procento |
| Přenosy \Disk \PhysicalDisk (využití _celkem) za sekundu |CountPerSecond |
| \PhysicalDisk (využití _celkem) \Disk přečtené strany/s |CountPerSecond |
| \PhysicalDisk (využití _celkem) \Disk zapsané strany/s |CountPerSecond |
| \PhysicalDisk (využití _celkem) \Disk bajty/s |BytesPerSecond |
| \Disk \PhysicalDisk (využití _celkem) přečtené bajty/s |BytesPerSecond |
| \PhysicalDisk (využití _celkem) \Disk zapsané bajty/s |BytesPerSecond |
| \Avg \PhysicalDisk (využití _celkem). Délka fronty disku |Počet |
| \Avg \PhysicalDisk (využití _celkem). Délka fronty disku pro čtení |Počet |
| \Avg \PhysicalDisk (využití _celkem). Délka fronty disku zápisu |Počet |
| \LogicalDisk(_Total)\% volného místa |Procento |
| \Free megabajty \LogicalDisk (využití _celkem) |Počet |

### <a name="guest-os-metrics-linux-vms"></a>Metriky hostovaného operačního systému virtuálních počítačů s Linuxem
Při vytváření virtuálního počítače v Azure diagnostics je ve výchozím nastavení povoleno pomocí diagnostického rozšíření.

Seznam metrik lze vytvořit pomocí následujícího příkazu v Powershellu.

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

## <a name="commonly-used-web-server-farm-metrics"></a>Běžně používané metriky webového (serverová farma)
Můžete také provést automatické škálování podle běžné metriky webového serveru, jako je délka fronty Http. Je to název metriky **HttpQueueLength**.  V následující části jsou uvedeny metrik dostupné serveru farmy (webové aplikace).

### <a name="web-apps-metrics"></a>Metriky webové aplikace
Pomocí následujícího příkazu v Powershellu můžete vygenerovat seznam metrik webových aplikací.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete výstrahy na nebo škálovat podle těchto metrikách.

| Název metriky | Jednotka |
| --- | --- |
| CpuPercentage |Procento |
| MemoryPercentage |Procento |
| DiskQueueLength |Počet |
| HttpQueueLength |Počet |
| BytesReceived |B |
| BytesSent |B |

## <a name="commonly-used-storage-metrics"></a>Běžně používané metriky úložiště
Délka fronty úložiště, což je počet zpráv ve frontě úložiště, které můžete škálovat. Délka fronty úložiště je speciální metriky a prahová hodnota je počet zpráv na instanci. Například pokud existují dvě instance a prahová hodnota je nastavena na hodnotu 100, škálování nastane, pokud celkový počet zpráv ve frontě je 200. Který může být 100 zprávy na jednu instanci, 120 a 80 nebo libovolné kombinaci, který přidá až 200 nebo víc.

Konfigurací tohoto nastavení na webu Azure Portal v **nastavení** okno. Pro škálovací sady virtuálních počítačů můžete aktualizovat nastavení automatického škálování v šabloně Resource Manageru používat *metricName* jako *ApproximateMessageCount* a předejte mu ID fronty úložiště jako  *metricResourceUri*.

Například by metricTrigger nastavení automatického škálování s klasický účet úložiště patří:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Pro účet úložiště (bez modelu classic) metricTrigger zahrnuje:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Běžně používané metriky služby Service Bus
Délka fronty služby Service Bus, což je počet zpráv ve frontě služby Service Bus, které můžete škálovat. Délka fronty služby Service Bus je speciální metriky a prahová hodnota je počet zpráv na instanci. Například pokud existují dvě instance a prahová hodnota je nastavena na hodnotu 100, škálování nastane, pokud celkový počet zpráv ve frontě je 200. Který může být 100 zprávy na jednu instanci, 120 a 80 nebo libovolné kombinaci, který přidá až 200 nebo víc.

Pro škálovací sady virtuálních počítačů můžete aktualizovat nastavení automatického škálování v šabloně Resource Manageru používat *metricName* jako *ApproximateMessageCount* a předejte mu ID fronty úložiště jako  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Služba Service Bus neexistuje koncept skupiny prostředků, ale vytvoří výchozí skupiny prostředků v jedné oblasti Azure Resource Manageru. Skupina prostředků je obvykle ve formátu "Default - ServiceBus-[Oblast]". Například: "Výchozí-ServiceBus-EastUS", 'Výchozí-ServiceBus-WestUS', 'výchozí-ServiceBus-AustraliaEast"atd.
>
>

