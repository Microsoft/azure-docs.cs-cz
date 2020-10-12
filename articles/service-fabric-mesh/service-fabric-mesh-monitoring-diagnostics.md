---
title: Monitorování a diagnostika v aplikacích Service Fabric sítě v Azure
description: Seznamte se s monitorováním a diagnostikou aplikace v Service Fabric sítě v Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: e940f0cf0d1547b317cd9e7bd15ac5486d5e70b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248403"
---
# <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Monitorování a diagnostika Service Fabric sítě jsou rozdělené do tří hlavních typů diagnostických dat:

- Protokoly aplikací – ty se definují jako protokoly z vašich kontejnerových aplikací na základě toho, jak jste aplikaci nastavili (např. protokoly Docker).
- Události platformy – události z platformy sítě, které jsou relevantní pro vaši operaci kontejneru, aktuálně včetně aktivace kontejnerů, deaktivace a ukončení.
- Metriky kontejnerů – využití prostředků a metriky výkonu pro vaše kontejnery (statistika Docker)

Tento článek popisuje možnosti monitorování a diagnostiky pro nejnovější dostupnou verzi Preview.

## <a name="application-logs"></a>Protokoly aplikací

Protokoly Docker můžete zobrazit z nasazených kontejnerů na základě jednotlivých kontejnerů. V modelu aplikace Service Fabric sítě je každý kontejner balíček kódu ve vaší aplikaci. Chcete-li zobrazit přidružené protokoly s balíčkem kódu, použijte následující příkaz:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> K získání názvu repliky můžete použít příkaz AZ oky Service-Replica. Názvy replik jsou přičítáním celých čísel od 0.

Tady je postup, jak to vypadá při zobrazení protokolů z kontejneru VotingWeb. Code z hlasovací aplikace:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriky kontejneru 

Prostředí sítě zpřístupňuje několik metrik, které označují, jak vaše kontejnery provádějí. Pomocí Azure Portal a rozhraní příkazového řádku Azure monitor jsou k dispozici následující metriky:

| Metrika | Popis | Jednotky|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu jako procento | % |
| MemoryUtilization | ActualMem/AllocatedMem jako procento | % |
| AllocatedCpu | Procesor přidělený jako šablona pro Azure Resource Manager | Millicores |
| AllocatedMemory | Paměť přidělená jako šablona pro Azure Resource Manager | MB |
| ActualCpu | Využití procesoru | Millicores |
| ActualMemory | Využití paměti | MB |
| ContainerStatus | 0 – neplatné: stav kontejneru je neznámý. <br> 1 – čeká se na spuštění kontejneru. <br> 2 – spouštění: kontejner se právě spouští. <br> 3-zahájeno: kontejner byl úspěšně spuštěn. <br> 4 – zastavování: Probíhá zastavení kontejneru. <br> 5 – zastaveno: kontejner se úspěšně zastavil. | Není k dispozici |
| ApplicationStatus | 0 – neznámý: stav nelze načíst. <br> 1 – připraveno: aplikace je úspěšně spuštěná. <br> 2. upgrade: probíhá upgrade. <br> 3 – vytváření: aplikace se vytváří. <br> 4 – odstraňování: aplikace se odstraňuje. <br> 5 – neúspěch: aplikaci se nepovedlo nasadit. | Není k dispozici |
| ServiceStatus | 0 – neplatné: služba aktuálně nemá stav <br> 1 – OK: služba je v pořádku.  <br> 2 – Upozornění: může se jednat o něco špatného, vyžadovat šetření <br> 3 – Chyba: došlo k nějakému problému, který vyžaduje šetření. <br> 4 – neznámé: stav nelze načíst. | Není k dispozici |
| ServiceReplicaStatus | 0 – neplatné: replika aktuálně nemá stav <br> 1 – OK: služba je v pořádku.  <br> 2 – Upozornění: může se jednat o něco špatného, vyžadovat šetření <br> 3 – Chyba: došlo k nějakému problému, který vyžaduje šetření. <br> 4 – neznámé: stav nelze načíst. | Není k dispozici | 
| RestartCount | Počet restartování kontejneru | Není k dispozici |

> [!NOTE]
> Hodnoty ServiceStatus a ServiceReplicaStatus jsou stejné jako hodnota [elementu](/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) ve Service Fabric. 

Každá metrika je k dispozici v různých dimenzích, takže můžete vidět agregace na různých úrovních. Aktuální seznam dimenzí je následující:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Dimenze CodePackageName není pro aplikace Linux k dispozici. 

Každá dimenze odpovídá různým komponentám [modelu Service Fabric aplikace](service-fabric-mesh-service-fabric-resources.md#applications-and-services) .

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Úplný seznam příkazů je k dispozici v [dokumentaci Azure monitor CLI](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , ale obsahujeme několik užitečných příkladů. 

V každém příkladu ID prostředku následuje tento model.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Využití procesoru kontejnery v aplikaci

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Využití paměti pro každou repliku služby
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Restartuje se pro každý kontejner v intervalu 1 hodiny. 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Průměrné využití procesoru napříč službami s názvem "VotingWeb" v okně 1 hodiny
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Průzkumník metrik

Průzkumník metrik je okno na portálu, ve kterém můžete vizualizovat všechny metriky pro aplikaci vaší sítě. Toto okno je dostupné na stránce aplikace na portálu a v okně Azure monitor, které můžete použít k zobrazení metrik pro všechny prostředky Azure, které podporují Azure Monitor. 

![Průzkumník metrik](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Další kroky
* Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
* Další informace o příkazech Azure Monitor metrik naleznete v dokumentaci k rozhraní příkazového [řádku Azure monitor CLI](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
