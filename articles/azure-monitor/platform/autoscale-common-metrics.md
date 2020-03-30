---
title: Automatické škálování běžných metrik
description: Zjistěte, které metriky se běžně používají pro automatické škálování cloudových služeb, virtuálních počítačů a webových aplikací.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 2c335168683212337876c963a7cfdb441d0ac69a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845563"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Automatické škálování běžných metrik Azure Monitoru

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Automatické škálování Azure Monitorumožňuje škálovat počet spuštěných instancí nahoru nebo dolů na základě telemetrických dat (metrik). Tento dokument popisuje běžné metriky, které můžete chtít použít. Na webu Azure Portal můžete zvolit metriku prostředků, podle které chcete škálovat. Můžete však také zvolit libovolnou metriku z jiného prostředku, podle které chcete škálovat.

Automatické škálování Azure Monitor uplatní jenom [pro škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), [aplikační služby – webové aplikace](https://azure.microsoft.com/services/app-service/web/)a [služby správy rozhraní API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Jiné služby Azure používají různé metody škálování.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Výpočetní metriky pro virtuální počítače založené na Správci prostředků
Ve výchozím nastavení virtuální počítače založené na Správci prostředků a škálovací sady virtuálních počítačů vyzařují základní metriky (na úrovni hostitele). Kromě toho při konfiguraci shromažďování diagnostických dat pro virtuální počítač Azure a VMSS, rozšíření diagnostiky Azure také vydává čítače výkonu hosta operačního systému (běžně označované jako "metriky hosta operačního systému").  Všechny tyto metriky použijete v pravidlech automatického škálování.

Rozhraní `Get MetricDefinitions` API/PoSH/CLI můžete použít k zobrazení metriky, které jsou k dispozici pro prostředek VMSS.

Pokud používáte škálovací sady virtuálních počítačových společností a nevidíte konkrétní metriku uvedenou, pak je pravděpodobně *zakázána* v rozšíření diagnostiky.

Pokud konkrétní metrika není vzorkována nebo přenášena na požadovanou frekvenci, můžete aktualizovat konfiguraci diagnostiky.

Pokud je některý z předchozích případů pravdivý, projděte [si použití PowerShellu a povolte Azure Diagnostics ve virtuálním počítači se systémem Windows](../../virtual-machines/extensions/diagnostics-windows.md) o Prostředí PowerShell ke konfiguraci a aktualizaci rozšíření Diagnostika virtuálních zařízení Azure, abyste tuto metriku povolili. Tento článek také obsahuje ukázkový konfigurační soubor diagnostiky.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metriky hostitele pro virtuální počítače se Správcem prostředků pro Windows a Linux
Následující metriky na úrovni hostitele jsou ve výchozím nastavení vyzařovány pro virtuální počítače Azure a VMSS v instancích Windows i Linuxu. Tyto metriky popisují váš virtuální počítač Azure, ale shromažďují se z hostitele virtuálního počítače Azure, nikoli prostřednictvím agenta nainstalovaného na virtuálním počítači hosta. Tyto metriky můžete použít v pravidlech automatického škálování.

- [Metriky hostitele pro virtuální počítače se Správcem prostředků pro Windows a Linux](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Metriky hostitele pro škálovací sady Virtuálnípočítače pro Windows a Linux založené na Správci prostředků](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Metriky hostovaného operačního systému pro virtuální servery Windows založené na Správci prostředků
Když vytvoříte virtuální počítač v Azure, diagnostika je povolená pomocí rozšíření Diagnostika. Rozšíření diagnostiky vyzařuje sadu metrik y převzaté z vnitřku virtuálního aplikace. To znamená, že můžete automaticky škálovat mimo metriky, které nejsou vydávány ve výchozím nastavení.

Seznam metrik můžete vygenerovat pomocí následujícího příkazu v prostředí PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procento |
| \Procesor (_Total)\% Privilegovaný čas |Procento |
| \Čas uživatele\% procesoru (_Total) |Procento |
| \Informace o procesoru (_Total)\Frekvence procesoru |Počet |
| \Systém\Procesy |Počet |
| \Process(_Total)\Počet vláken |Počet |
| \Process(_Total)\Počet popisovačů |Počet |
| \Počet\% ucommitted potvrzených bajtů paměti je používán |Procento |
| \Memory\Available Bytes |Bajty |
| \Paměť\Potvrzené bajty |Bajty |
| \Memory\Commit Limit |Bajty |
| \Paměť\Bajty stránkovaných v bazénu |Bajty |
| \Paměť\Nestránkované bajty fondu |Bajty |
| \Fyzický disk (_Total)\% Čas disku |Procento |
| \Fyzický disk (_Total)\% Čas čtení disku |Procento |
| \Fyzický disk (_Total)\% Doba zápisu disku |Procento |
| \PhysicalDisk(_Total)\Přenosy disků/s |CountPerSecond |
| \PhysicalDisk(_Total)\Čtení disku/s |CountPerSecond |
| \PhysicalDisk(_Total)\Zápisy na disku/s |CountPerSecond |
| \PhysicalDisk(_Total)\Bajty disku/s |BajtyPerSekunda |
| \PhysicalDisk(_Total)\Bajty čtení disku/s |BajtyPerSekunda |
| \PhysicalDisk(_Total)\Bajty zápisu na disku/s |BajtyPerSekunda |
| \PhysicalDisk(_Total)\Avg. Délka fronty disku |Počet |
| \PhysicalDisk(_Total)\Avg. Délka fronty čtení disku |Počet |
| \PhysicalDisk(_Total)\Avg. Délka fronty zápisu disku |Počet |
| \LogicalDisk(_Total)\% Volné místo |Procento |
| \LogicalDisk(_Total)\Volné megabajty |Počet |

### <a name="guest-os-metrics-linux-vms"></a>Metriky hostovaného operačního systému virtuální počítače Linux
Když vytvoříte virtuální počítač v Azure, diagnostika je ve výchozím nastavení povolena pomocí rozšíření Diagnostika.

Seznam metrik můžete vygenerovat pomocí následujícího příkazu v prostředí PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Můžete vytvořit výstrahu pro následující metriky:

| Název metriky | Jednotka |
| --- | --- |
| \Paměť\Dostupná paměť |Bajty |
| \Paměť\Procento dostupnépaměti |Procento |
| \Paměť\Použité Paměti |Bajty |
| \Paměť\PercentUsedMemory |Procento |
| \Paměť\PercentUsedByCache |Procento |
| \Paměť\PagesPersec |CountPerSecond |
| \Paměť\PagesReadPersec |CountPerSecond |
| \Paměť\PagesWrittenPersec |CountPerSecond |
| \Memory\AvailableSwap |Bajty |
| \Memory\PercentAvailableSwap |Procento |
| \Memory\UsedSwap |Bajty |
| \Memory\PercentUsedSwap |Procento |
| \Procesor\PercentIdleTime |Procento |
| \Procesor\Procento času uživatele |Procento |
| \Procesor\PercentNiceTime |Procento |
| \Procesor\PercentPrivilegedTime |Procento |
| \Procesor\Procento času přerušení |Procento |
| \Procesor\PercentDPCTime |Procento |
| \Procesor\Procento času procesoru |Procento |
| \procesor\PercentioWaitTime |Procento |
| \Fyzický disk\Bajtpersekunda |BajtyPerSekunda |
| \PhysicalDisk\ReadBytesPerSecond |BajtyPerSekunda |
| \PhysicalDisk\WriteBytesPerSecond |BajtyPerSekunda |
| \Fyzický disk\Přenosymezi druhé |CountPerSecond |
| \Fyzický disk\Přečítá vteřinu |CountPerSecond |
| \Fyzický disk\Zapisovánízasekundu |CountPerSecond |
| \Fyzický disk\AverageReadTime |Sekundy |
| \Fyzický disk\AverageWriteTime |Sekundy |
| \Fyzický disk\PrůměrČasu přenosu |Sekundy |
| \Fyzický disk\Průměrná délka fronty disků |Počet |
| \NetworkInterface\Přenesené bajty |Bajty |
| \NetworkInterface\Počet bajtů |Bajty |
| \NetworkInterface\Pakety přenášené |Počet |
| \NetworkInterface\PacketsReceived |Počet |
| \NetworkInterface\BytesTotal |Bajty |
| \NetworkInterface\TotalRxErrors |Počet |
| \NetworkInterface\TotalTxErrors |Počet |
| \NetworkInterface\TotalCollisions |Počet |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Běžně používané metriky služby App Service (Server Farm)
Automatické škálování můžete také provádět na základě běžných metrik webového serveru, jako je například délka fronty Http. Jeho název metriky je **HttpQueueLength**.  V následující části jsou uvedeny metriky dostupné serverové farmy (App Service).

### <a name="web-apps-metrics"></a>Metriky webových aplikací
Seznam metrik webových aplikací můžete vygenerovat pomocí následujícího příkazu v prostředí PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Podle těchto metrik můžete upozorňovat na tyto metriky nebo je škálovat.

| Název metriky | Jednotka |
| --- | --- |
| Procento procesoru |Procento |
| Procento paměti |Procento |
| Délka fronty disků |Počet |
| HttpQueueLength |Počet |
| Přijaté bajty |Bajty |
| BajtůOdeslání |Bajty |

## <a name="commonly-used-storage-metrics"></a>Běžně používané metriky úložiště
Můžete škálovat podle délky fronty úložiště, což je počet zpráv ve frontě úložiště. Délka fronty úložiště je speciální metrika a prahová hodnota je počet zpráv na instanci. Například pokud existují dvě instance a pokud je prahová hodnota nastavena na 100, škálování dochází při celkový počet zpráv ve frontě je 200. To může být 100 zpráv na instanci, 120 a 80 nebo jakoukoli jinou kombinaci, která přidá až 200 nebo více.

Toto nastavení nakonfigurujte na webu Azure Portal v okně **Nastavení.** U škálovacích sad virtuálních zařízení můžete aktualizovat nastavení automatického škálování v šabloně Správce prostředků tak, aby *používalo metricName* jako *ApproximateMessageCount* a předalo ID fronty úložiště jako *metricResourceUri*.

Například s klasickým účtem úložiště by metrika nastavení automatického škálování trigger zahrnovala:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Pro účet (neklasické) úložiště metricTrigger by zahrnoval:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Běžně používané metriky service bus
Můžete škálovat podle délky fronty service bus, což je počet zpráv ve frontě service bus. Délka fronty service bus je speciální metrika a prahová hodnota je počet zpráv na instanci. Například pokud existují dvě instance a pokud je prahová hodnota nastavena na 100, škálování dochází při celkový počet zpráv ve frontě je 200. To může být 100 zpráv na instanci, 120 a 80 nebo jakoukoli jinou kombinaci, která přidá až 200 nebo více.

U škálovacích sad virtuálních zařízení můžete aktualizovat nastavení automatického škálování v šabloně Správce prostředků tak, aby *používalo metricName* jako *ApproximateMessageCount* a předalo ID fronty úložiště jako *metricResourceUri*.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Pro Service Bus koncept skupiny prostředků neexistuje, ale Azure Resource Manager vytvoří výchozí skupinu prostředků pro oblast. Skupina prostředků je obvykle ve formátu Default-ServiceBus-[region].. Například "Default-ServiceBus-EastUS", "Default-ServiceBus-WestUS", "Default-ServiceBus-AustraliaEast" atd.
>
>

