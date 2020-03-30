---
title: Agregace událostí s diagnostikou LinuxAzure
description: Další informace o agregaci a shromažďování událostí pomocí LAD pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609957"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregace a shromažďování událostí pomocí diagnostiky Linuxazure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Když používáte cluster Azure Service Fabric, je vhodné shromažďovat protokoly ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění vám pomůže analyzovat a řešit problémy v clusteru nebo problémy v aplikacích a službách spuštěných v tomto clusteru.

Jedním ze způsobů, jak nahrávat a shromažďovat protokoly, je použití rozšíření Diagnostika Linuxazure (LAD), které nahrává protokoly do Služby Azure Storage a má také možnost odesílat protokoly do Azure Application Insights nebo Event Hubs. Můžete také použít externí proces ke čtení událostí z úložiště a umístit je do produktu [platformy analýzy,](../log-analytics/log-analytics-service-fabric.md) jako jsou protokoly Azure Monitor nebo jiné řešení analýzy protokolů.

## <a name="log-and-event-sources"></a>Zdroje protokolů a událostí

### <a name="service-fabric-platform-events"></a>Události platformy Service Fabric
Service Fabric vydává několik out-of-the-box protokoly přes [LTTng](https://lttng.org), včetně provozních událostí nebo runtime události. Tyto protokoly jsou uloženy v umístění, které určuje šablona Správce prostředků clusteru. Chcete-li získat nebo nastavit podrobnosti o účtu úložiště, vyhledejte značku **AzureTableWinFabETWQueryable** a vyhledejte **StoreConnectionString**.

### <a name="application-events"></a>Události aplikace
 Události vyzařované z kódu vašich aplikací a služeb, jak jste určili při instrumentaci softwaru. Můžete použít libovolné řešení protokolování, které zapisuje textové soubory protokolu – například LTTng. Další informace naleznete v dokumentaci LTTng o trasování aplikace.

[Monitorujte a diagnostikujte služby v nastavení vývoje místního počítače](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Nasazení rozšíření Diagnostika
Prvním krokem při shromažďování protokolů je nasazení rozšíření diagnostiky na každém z virtuálních počítačů v clusteru Service Fabric. Rozšíření Diagnostika shromažďuje protokoly na každém virtuálním počítači a odešle je do účtu úložiště, který zadáte. 

Chcete-li nasadit rozšíření Diagnostika do virtuálních počítačů v clusteru jako součást vytváření clusteru, nastavte **diagnostiku** **na zapnuto**. Po vytvoření clusteru nelze toto nastavení změnit pomocí portálu, takže budete muset provést příslušné změny v šabloně Správce prostředků.

Tím nakonfigurujete agenta LAD tak, aby monitoroval zadané soubory protokolu. Při každém připojení nového řádku k souboru vytvoří položku syslogu, která je odeslána do zadaného úložiště (tabulky).


## <a name="next-steps"></a>Další kroky

1. Chcete-li podrobněji porozumět událostem, které byste měli prozkoumat při řešení problémů, naleznete v [dokumentaci LTTNG](https://lttng.org/docs) a [použití lad](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Nastavte agenta Log Analytics,](service-fabric-diagnostics-event-analysis-oms.md) který vám pomůže shromažďovat metriky, monitorovat kontejnery nasazené ve vašem clusteru a vizualizovat protokoly. 
