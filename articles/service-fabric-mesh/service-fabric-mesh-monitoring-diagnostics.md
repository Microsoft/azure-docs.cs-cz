---
title: Monitorování a diagnostika v aplikacích Azure Service Fabric Mesh
description: Přečtěte si o monitorování a diagnostice aplikací v síti Service Fabric Mesh v Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498145"
---
# <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Monitorování a diagnostika sítě Service Fabric je rozdělena do tří hlavních typů diagnostických dat:

- Protokoly aplikací - tyto jsou definovány jako protokoly z kontejnerizovaných aplikací, na základě toho, jak jste instrumentovali aplikaci (např. protokoly dockeru)
- Události platformy – události z platformy Mesh relevantní pro provoz kontejneru, v současné době včetně aktivace kontejneru, deaktivace a ukončení.
- Metriky kontejnerů – metriky využití prostředků a výkonu pro vaše kontejnery (statistiky dockeru)

Tento článek popisuje možnosti monitorování a diagnostiky pro nejnovější verzi preview k dispozici.

## <a name="application-logs"></a>Protokoly aplikací

Můžete zobrazit protokoly dockeru z nasazených kontejnerů na základě kontejneru. V modelu aplikace Service Fabric Mesh je každý kontejner balíček kódu ve vaší aplikaci. Chcete-li zobrazit přidružené protokoly s balíčkem kódu, použijte následující příkaz:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> K získání názvu repliky můžete použít příkaz "az mesh service-replica". Názvy replik se získají celá čísla z 0.

Zde je to, co to vypadá pro zobrazení protokoly z kontejneru VotingWeb.Code z hlasovací aplikace:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriky kontejneru 

Prostředí Mesh zveřejňuje několik metrik označujících, jak si vaše kontejnery vedou. Následující metriky jsou dostupné prostřednictvím portálu Azure a rozhraní příkazového příkazu k monitorování Azure:

| Metrika | Popis | Jednotky|
|----|----|----|
| Využití procesoru | ActualCpu/AllocatedCpu v procentech | % |
| Využití paměti | ActualMem/AlokovánoMem jako procento | % |
| Přidělení procesoru | Procesor přidělený podle šablony Azure Resource Manager | Milijádra |
| Přidělená paměť | Paměť přidělená podle šablony Správce prostředků Azure | MB |
| ActualCpu | Využití procesoru | Milijádra |
| Skutečná paměť | Využití paměti | MB |
| ContainerStatus | 0 - Neplatný: Stav kontejneru není znám. <br> 1 - Čeká na vyřízení: Kontejner má být naplánován na začátek <br> 2 - Spuštění: Kontejner je v procesu spouštění <br> 3 - Spuštěno: Kontejner byl úspěšně spuštěn <br> 4 - Zastavení: Kontejner je zastaven <br> 5 - Zastaveno: Kontejner byl úspěšně zastaven | Není dostupné. |
| Stav aplikace | 0 - Neznámý: Stav nelze načíst <br> 1 - Ready: Aplikace je úspěšně spuštěna <br> 2 - Upgrade: Probíhá upgrade <br> 3 - Vytváření: Aplikace je vytvářena <br> 4 - Odstranění: Aplikace je smazána <br> 5 - Se nezdařilo: Aplikace se nepodařilo nasadit | Není dostupné. |
| Stav služby | 0 - Neplatný: Služba aktuálně nemá stav. <br> 1 - Ok: Služba je zdravá  <br> 2 - Upozornění: Může být něco špatně vyžadující vyšetřování <br> 3 - Chyba: Tam je něco špatně, že potřebuje šetření <br> 4 - Neznámý: Stav nelze získat | Není dostupné. |
| ServiceReplicaStatus | 0 - Neplatný: Replika aktuálně nemá stav. <br> 1 - Ok: Služba je zdravá  <br> 2 - Upozornění: Může být něco špatně vyžadující vyšetřování <br> 3 - Chyba: Tam je něco špatně, že potřebuje šetření <br> 4 - Neznámý: Stav nelze získat | Není dostupné. | 
| Restartovatcount | Počet restartování kontejneru | Není dostupné. |

> [!NOTE]
> Hodnoty ServiceStatus a ServiceReplicaStatus jsou stejné jako [healthstate](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) v service fabric. 

Každá metrika je k dispozici v různých dimenzích, takže můžete vidět agregace na různých úrovních. Aktuální seznam dimenzí je následující:

* ApplicationName
* ServiceName
* Název_repliky služby
* Název balíčku kódu

> [!NOTE]
> Dimenze CodePackageName není k dispozici pro aplikace linuxu. 

Každá dimenze odpovídá různým součástem [modelu aplikace Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Cli monitoru Azure

Úplný seznam příkazů je k dispozici v [dokumentech rozhraní příkazu Azure Monitor,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) ale níže jsme zahrnuli několik užitečných příkladů. 

V každém příkladu se podle tohoto vzoru řídí ID prostředku.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Využití procesoru kontejnerů v aplikaci

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Využití paměti pro každou repliku služby
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Restartuje pro každý kontejner v 1 hodinovém okně 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Průměrné využití procesoru napříč službami s názvem "VotingWeb" v okně 1 hodina
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Průzkumník metrik

Průzkumník metrik je okno na portálu, ve kterém můžete vizualizovat všechny metriky pro vaši aplikaci Mesh. Toto okno je přístupné na stránce aplikace na portálu a v okně monitorování Azure, které můžete použít k zobrazení metrik pro všechny prostředky Azure, které podporují Azure Monitor. 

![Průzkumník metrik](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Další kroky
* Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
* Další informace o příkazech metrik Azure Monitoru najdete v [dokumentech příkazu Příkazu k obsluze příkazu Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
