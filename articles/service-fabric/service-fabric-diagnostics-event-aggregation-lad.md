---
title: Agregace událostí pomocí Azure Diagnostics pro Linux
description: Naučte se agregovat a shromažďovat události pomocí LAD pro monitorování a diagnostiku clusterů Azure Service Fabric.
ms.topic: conceptual
ms.date: 2/25/2019
ms.openlocfilehash: bd5efc6b661bf25ce7946cda0bcfa01be4c351a7
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628930"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregace a shromažďování událostí pomocí Azure Diagnostics pro Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Pokud používáte cluster Azure Service Fabric, je vhodné shromáždit protokoly ze všech uzlů v centrálním umístění. Protokoly v centrálním umístění vám pomůžou analyzovat a řešit problémy v clusteru nebo problémy s aplikacemi a službami běžícími v tomto clusteru.

Jedním ze způsobů, jak nahrávat a shromažďovat protokoly, je použít rozšíření Linux Azure Diagnostics (LAD), které nahrává protokoly k Azure Storage a má také možnost odesílat protokoly do Azure Application Insights nebo Event Hubs. Můžete také použít externí proces ke čtení událostí z úložiště a jejich umístění do produktu Analysis Platform, jako jsou [protokoly Azure monitor](./service-fabric-diagnostics-oms-setup.md) nebo jiné řešení pro analýzu protokolů.

## <a name="log-and-event-sources"></a>Zdroje protokolů a událostí

### <a name="service-fabric-platform-events"></a>Service Fabric události platformy
Service Fabric emituje několik bezplatných protokolů prostřednictvím [LTTng](https://lttng.org), včetně provozních událostí nebo běhových událostí. Tyto protokoly jsou uloženy v umístění, které určuje šablona Správce prostředků clusteru. Pokud chcete získat nebo nastavit podrobnosti účtu úložiště, vyhledejte tag **AzureTableWinFabETWQueryable** a vyhledejte **StoreConnectionString**.

### <a name="application-events"></a>Události aplikace
 Události emitované z kódu vašich aplikací a služeb, které jsou určeny při instrumentaci softwaru. Můžete použít jakékoli řešení protokolování, které zapisuje textové soubory protokolu – například LTTng. Další informace najdete v dokumentaci k LTTng o trasování vaší aplikace.

[Monitorování a diagnostika služeb v nastavení pro vývoj v místním počítači](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>Nasazení rozšíření diagnostiky
Prvním krokem při shromažďování protokolů je nasazení diagnostického rozšíření na každém virtuálním počítači v clusteru Service Fabric. Diagnostické rozšíření shromažďuje protokoly na každém virtuálním počítači a nahrává je do účtu úložiště, který zadáte. 

Pokud chcete v rámci vytváření clusteru nasadit diagnostické rozšíření na virtuální počítače v clusteru, nastavte **diagnostiku** na **zapnuto**. Po vytvoření clusteru nemůžete toto nastavení změnit pomocí portálu, takže budete muset provést příslušné změny v šabloně Správce prostředků.

Tím se nakonfiguruje agent LAD pro sledování zadaných souborů protokolu. Pokaždé, když se do souboru připojí nový řádek, vytvoří se položka syslog, která se pošle do úložiště (tabulky), které jste zadali.


## <a name="next-steps"></a>Další kroky

1. Další informace o tom, jaké události byste měli prošetřit při řešení problémů, najdete v [dokumentaci k LTTng](https://lttng.org/docs) a [pomocí lad](../virtual-machines/extensions/diagnostics-linux.md).
2. [Nastavte agenta Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) , který vám umožní shromáždit metriky, monitorovat kontejnery nasazené v clusteru a vizualizovat protokoly. 
