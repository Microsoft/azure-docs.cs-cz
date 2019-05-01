---
title: Monitorování a Diagnostika aplikace Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Další informace o monitorování a Diagnostika aplikace v Service Fabric sítě v Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939825"
---
# <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Monitorování a Diagnostika sítě pro Service Fabric je rozdělená na tři hlavní typy diagnostických dat:

- Protokoly aplikací – jsou definované jako protokoly z kontejnerizované aplikace, podle toho, jak máte instrumentované aplikace (například protokoly dockeru)
- Události platformy – události z relevantní pro vaši operaci kontejner sítě platformy aktuálně včetně kontejneru aktivaci, deaktivaci a ukončení.
- Metriky kontejnerů - metriky využití a výkonu prostředků pro kontejnery (docker statistiky)

Tento článek popisuje možnosti monitorování a diagnostiky pro nejnovější dostupná verze preview.

## <a name="application-logs"></a>Protokoly aplikací

Zobrazit protokoly dockeru na nasazenými kontejnery, na základě za kontejner. Každý kontejner v aplikační model sítě pro Service Fabric, je balíček kódu v aplikaci. Přidružených protokolů se balíček kódu zobrazíte následujícím příkazem:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Příkaz "az mřížky repliku služby" můžete získat název repliky. Replika je zvyšování hodnoty celých čísel od 0.

Zde je, jak to vypadá k zobrazení protokolů z kontejnerů VotingWeb.Code z hlasovací aplikace:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriky kontejnerů 

Prostředí sítě poskytuje několik metrik určující, jaký výkon vaše kontejnery. Tyto metriky jsou k dispozici přes Azure portal a Azure monitorovat rozhraní příkazového řádku:

| Metrika | Popis | Jednotky|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu v procentech | % |
| MemoryUtilization | ActualMem/AllocatedMem v procentech | % |
| AllocatedCpu | Využití procesoru, které jsou přiděleny podle šablony Azure Resource Manageru | Jednotkách Millicore |
| AllocatedMemory | Paměť přidělená podle šablony Azure Resource Manageru | MB |
| ActualCpu | Využití procesoru | Jednotkách Millicore |
| ActualMemory | Využití paměti | MB |
| ContainerStatus | 0 – neplatný: Stav kontejneru neznámý <br> 1 - čekající na vyřízení: Kontejner je naplánováno spuštění <br> 2 - od: Probíhá spuštění kontejneru <br> 3 – spuštění: Kontejner se úspěšně spustila. <br> 4 – zastavení: Kontejner se zastavuje. <br> 5 - stopped: Kontejner se úspěšně zastavila. | neuvedeno |
| ApplicationStatus | 0 – Neznámý: Stav není retrievable <br> 1 - připravené: Aplikace je úspěšně spuštěná. <br> 2 - upgradu: Je v průběhu upgradu <br> 3 – vytvoření: Vytvoření aplikace <br> 4 – odstranění: Odstraňuje se aplikace <br> 5 – se nezdařilo: Nasazení aplikace se nezdařilo. | neuvedeno |
| ServiceStatus | 0 – neplatný: Služba momentálně nemá stav <br> 1 - ok: Služba je v pořádku  <br> 2 - upozornění: Může být problém vyžadující šetření <br> 3 – Chyba: Vyskytl se problém, který je potřeba vyšetřit <br> 4 = neznámý: Stav není retrievable | neuvedeno |
| ServiceReplicaStatus | 0 – neplatný: Replika nemá aktuálně stav <br> 1 - ok: Služba je v pořádku  <br> 2 - upozornění: Může být problém vyžadující šetření <br> 3 – Chyba: Vyskytl se problém, který je potřeba vyšetřit <br> 4 = neznámý: Stav není retrievable | neuvedeno | 
| RestartCount | Počet kontejnerů restartování | neuvedeno |

> [!NOTE]
> Hodnoty ServiceStatus a ServiceReplicaStatus jsou stejné jako [stavu HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) v Service Fabric. 

Jednotlivé metriky je k dispozici na různých dimenzí, abyste si mohli zobrazit na různých úrovních agregace. Aktuální seznam dimenze jsou následující:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Dimenze CodePackageName není k dispozici pro Linuxové aplikace. 

Každé dimenze odpovídá různými komponentami nástroje [modelu aplikace Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Úplný seznam příkazů je k dispozici v [dokumentace rozhraní příkazového řádku Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) ale uvádíme několik užitečné následující příklady 

V obou příkladech ID prostředku, které používá tento vzor

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Využití procesoru v aplikaci kontejnery

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Využití paměti pro každou repliku služby
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Restartování pro každý kontejner v okně 1 hodina 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Průměrné využití procesoru napříč službami s názvem "VotingWeb" v okně 1 hodina
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Průzkumník metrik

Průzkumník metrik je okno na portálu, ve kterém můžete vizualizovat všechny metriky pro vaši aplikaci sítě. V tomto okně je dostupná na stránce aplikace v portálu a okno Azure monitor, odkazující z nich můžete použít, pokud chcete zobrazit metriky pro všechny vaše prostředky Azure, které podporují Azure Monitor. 

![Průzkumník metrik](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Další postup
* Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
* Další informace o příkazech metrik Azure monitoru, podívejte se [dokumentace rozhraní příkazového řádku Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
