---
title: Seznam událostí služby Azure Service Fabric | Dokumentace Microsoftu
description: Úplný seznam událostí, které poskytuje Azure Service Fabric, aby se usnadnilo monitorování clusterů.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde0464985f756132c60453c4e79ffefd4a1dd2c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788599"
---
# <a name="list-of-service-fabric-events"></a>Seznam událostí Service Fabric 

Service Fabric poskytuje sadu události clusteru pro informování o stavu vašeho clusteru jako primární [události Service Fabric](service-fabric-diagnostics-events.md). Jsou založeny na rozhodnutích týkajících se správy od vlastníka nebo operátor clusteru nebo akce provádět na svých uzlech i cluster Service Fabric. Tyto události je přístupná pomocí konfigurace v několika způsoby, včetně konfigurace [protokoly Azure monitoru s vaším clusterem](service-fabric-diagnostics-oms-setup.md), nebo dotazování [Eventstoru](service-fabric-diagnostics-eventstore.md). Na počítačích s Windows získávají tyto události do protokolu událostí –, abyste si mohli zobrazit události Service Fabric v prohlížeči událostí. 

Tady jsou některé vlastnosti tyto události
* Každá událost se váže na konkrétní entitu v clusteru např. aplikace, služby, uzel, repliky.
* Každá událost obsahuje sadu společných polí: EventInstanceId EventName a kategorie.
* Každá událost obsahuje pole, které jsou události zpět do entity, který je přidružený. Událost ApplicationCreated by obsahovat pole, které identifikují název aplikace vytvořená.
* Události jsou strukturované tak, že mohou být využívány v různých nástrojů provést hlubší analýzu. Kromě toho související podrobnosti o události jsou definovány jako samostatné vlastnosti na rozdíl od dlouhý řetězec. 
* Události se zapisují podle různé subsystémy v Service Fabric jsou označeny Source(Task) níže. Další informace jsou k dispozici na těchto subsystémů v [architektura Service Fabric](service-fabric-architecture.md) a [Service Fabric – technický přehled](service-fabric-technical-overview.md).

Tady je seznam těchto událostí Service Fabric uspořádané podle entity.

## <a name="cluster-events"></a>Události clusteru

**Události upgrade clusteru**

Najdete další podrobnosti o clusteru upgrady [tady](service-fabric-cluster-upgrade-windows-server.md).

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Upgrade | Bylo zahájeno upgradu clusteru | CM | Informativní |
| 29628 | ClusterUpgradeCompleted | Upgrade | Dokončení upgradu clusteru | CM | Informativní | 
| 29629 | ClusterUpgradeRollbackStarted | Upgrade | Upgrade clusteru začal vrácení zpět  | CM | Upozornění | 
| 29630 | ClusterUpgradeRollbackCompleted | Upgrade | Upgrade clusteru dokončení vrácení zpět | CM | Upozornění | 
| 29631 | ClusterUpgradeDomainCompleted | Upgrade | Upgradovací doména dokončil upgrade během upgradu clusteru | CM | Informativní | 

## <a name="node-events"></a>Uzel události

**Události životního cyklu uzlu** 

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Deaktivace uzlu byla dokončena. | FM | Informativní | 
| 18603 | NodeUp | StateTransition | Cluster zjistil, že uzel má spuštění | FM | Informativní | 
| 18604 | NodeDown | StateTransition | Cluster zjistil, že uzel byl vypnut. Při restartování uzlu zobrazí se NodeDown událostí, za nímž následuje NodeUp událostí |  FM | Chyba | 
| 18605 | NodeAddedToCluster | StateTransition |  Byl přidán nový uzel do clusteru a Service Fabric můžete nasazovat aplikace do tohoto uzlu | FM | Informativní | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Uzel byl odebrán z clusteru. Service Fabric se už moct nasazovat aplikace do tohoto uzlu | FM | Informativní | 
| 18607 | NodeDeactivateStarted | StateTransition |  Bylo zahájeno deaktivace uzlu | FM | Informativní | 
| 25621 | NodeOpenSucceeded | StateTransition |  Uzel byl úspěšně spuštěn | FabricNode | Informativní | 
| 25622 | NodeOpenFailed | StateTransition |  Uzel se nepovedlo spustit a připojit se k okruhu | FabricNode | Chyba | 
| 25624 | NodeClosed | StateTransition |  Uzel úspěšně vypnuta | FabricNode | Informativní | 
| 25626 | NodeAborted | StateTransition |  Uzel má ungracefully vypnout | FabricNode | Chyba | 

## <a name="application-events"></a>Události aplikace

**Události životního cyklu aplikace**

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Životní cyklus | Vytvoření nové aplikace | CM | Informativní | 
| 29625 | ApplicationDeleted | Životní cyklus | Existující aplikace byla odstraněna. | CM | Informativní | 
| 23083 | ApplicationProcessExited | Životní cyklus | Došlo k ukončení procesu v rámci aplikace | Hosting | Informativní | 

**Události upgradu aplikace**

Najdete další podrobnosti o upgradech aplikací [tady](service-fabric-application-upgrade.md).

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Upgrade | Upgrade aplikace byla spuštěna. | CM | Informativní | 
| 29622 | ApplicationUpgradeCompleted | Upgrade | Upgrade aplikace byla dokončena. | CM | Informativní | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgrade | Upgrade aplikace spustila na vrácení zpět |CM | Upozornění | 
| 29624 | ApplicationUpgradeRollbackCompleted | Upgrade | Upgrade aplikace dokončení vrácení zpět | CM | Upozornění | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgrade | Upgradovací doména dokončil upgrade během upgradu aplikace | CM | Informativní | 

## <a name="service-events"></a>Události služby

**Události životního cyklu služeb**

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Životní cyklus | Byla vytvořena nová služba | FM | Informativní | 
| 18658 | ServiceDeleted | Životní cyklus | Existující služba se odstranila. | FM | Informativní | 

## <a name="partition-events"></a>Oddíl události

**Události pohybu oddílu**

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Životní cyklus | Rekonfigurace oddílu byla dokončena. | RA | Informativní | 

## <a name="replica-events"></a>Události repliky

**Události životního cyklu repliky**

| ID události | Název | Category | Popis |Zdroj (úloha) | Úroveň |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Životní cyklus | Otevření spolehlivého slovníku | DistributedDictionary | Informativní |
| 61702 | ReliableDictionaryClosed | Životní cyklus | Spolehlivého slovníku byl uzavřen. | DistributedDictionary | Informativní |
| 61703 | ReliableDictionaryCheckpointRecovered | Životní cyklus | Spolehlivého slovníku se zotavil jeho kontrolní bod | DistributedDictionary | Informativní |
| 61704 | ReliableDictionaryCheckpointFilesSent | Životní cyklus | Replika byla odeslána souborů kontrolních bodů spolehlivého slovníku | DistributedDictionary | Informativní |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Životní cyklus | Repliky přijal souborů kontrolních bodů spolehlivého slovníku | DistributedDictionary | Informativní |
| 61963 | ReliableQueueOpened | Životní cyklus | Otevření spolehlivé fronty | DistributedQueue | Informativní |
| 61964 | ReliableQueueClosed | Životní cyklus | Spolehlivá fronta byl uzavřen. | DistributedQueue | Informativní |
| 61965 | ReliableQueueCheckpointRecovered | Životní cyklus | Spolehlivá fronta byla obnovena jeho kontrolní bod | DistributedQueue | Informativní |
| 61966 | ReliableQueueCheckpointFilesSent | Životní cyklus | Replika byla odeslána souborů kontrolních bodů spolehlivé fronty | DistributedQueue | Informativní |
| 63647 | ReliableQueueCheckpointFilesReceived | Životní cyklus | Repliky přijal souborů kontrolních bodů spolehlivé fronty | DistributedQueue | Informativní |
| 63648 | ReliableConcurrentQueueOpened | Životní cyklus | Spolehlivá souběžná fronta otevřel. | ReliableConcurrentQueue | Informativní |
| 63649 | ReliableConcurrentQueueClosed | Životní cyklus | Spolehlivá souběžná fronta byl uzavřen. | ReliableConcurrentQueue | Informativní |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Životní cyklus | Spolehlivá souběžná fronta byla obnovena jeho kontrolní bod | ReliableConcurrentQueue | Informativní |
| 61687 | TStoreError | Chyba | Spolehlivé kolekce přijal neočekávanou chybu | TStore | Chyba |
| 63831 | PrimaryFullCopyInitiated | Životní cyklus | Primární replika inicioval úplného kopírování | TReplicator | Informativní |
| 63832 | PrimaryPartialCopyInitiated | Životní cyklus | Primární replika inicioval částečnou kopii | TReplicator | Informativní |
| 16831 | BuildIdleReplicaStarted | Životní cyklus | Primární replika bylo zahájeno vytváření repliky nečinnosti | Replikace | Informativní |
| 16832 | BuildIdleReplicaCompleted | Životní cyklus | Primární replika dokončil vytváření repliky nečinnosti | Replikace | Informativní |
| 16833 | BuildIdleReplicaFailed | Životní cyklus | Primární repliky se nepovedlo se vytvořit nečinnosti repliky | Replikace | Upozornění |
| 16834 | PrimaryReplicationQueueFull | Zdraví | Primární repliky ve frontě replikací je plná | Replikace | Upozornění |
| 16835 | PrimaryReplicationQueueWarning | Zdraví | Fronta replikací primární repliky je téměř úplnou | Replikace | Upozornění |
| 16836 | PrimaryReplicationQueueWarningMitigated | Zdraví | Primární repliky ve frontě replikací je v pořádku | Replikace | Informativní |
| 16837 | SecondaryReplicationQueueFull | Zdraví | Sekundární repliky ve frontě replikací je plná | Replikace | Upozornění |
| 16838 | SecondaryReplicationQueueWarning | Zdraví | Sekundární repliky ve frontě replikací je téměř úplnou | Replikace | Upozornění |
| 16839 | SecondaryReplicationQueueWarningMitigated | Zdraví | Sekundární repliky ve frontě replikací je v pořádku | Replikace | Informativní |
| 16840 | PrimaryFaultedSlowSecondary | Zdraví | Primární repliky došlo k chybě pomalé sekundární repliky | Replikace | Upozornění |
| 16841 | ReplicatorFaulted | Zdraví | Repliky došlo k chybě. | Replikace | Upozornění |

## <a name="container-events"></a>Události kontejneru

**Události životního cyklu kontejneru** 

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Bylo zahájeno kontejneru | Hosting | Informativní | 1 |
| 23075 | ContainerDeactivated | Kontejner se zastavila. | Hosting | Informativní | 1 |
| 23082 | ContainerExited | Kontejner se ukončil - zkontrolujte soubor UnexpectedTermination příznak | Hosting | Informativní | 1 |

## <a name="health-reports"></a>Sestav stavu

[Modelu stavu Service Fabric](service-fabric-health-introduction.md) poskytuje bohaté možnosti, flexibilní a rozšiřitelné stav vyhodnocení a vytváření sestav. Spouští se Service Fabric verze 6.2, data o stavu je zapisují jako události platformy poskytnout historické záznamy o stavu. Pokud chcete zachovat objemu událostí stavu s nízkou, napíšeme pouze následující události Service Fabric:

* Všechny `Error` nebo `Warning` sestav stavu
* `Ok` sestavy stavu během přechodů
* Když `Error` nebo `Warning` událost stavu – platnost vyprší. To umožňuje zjistit, jak dlouho entity není v pořádku

**Události sestavy stavu clusteru**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Je k dispozici nová sestava stavu clusteru | HM | Informativní | 1 |
| 54437 | ClusterHealthReportExpired | Vypršela platnost existující sestavy stavu clusteru | HM | Informativní | 1 |

**Události sestavy stavu uzlu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Je k dispozici nová sestava stavu uzlu | HM | Informativní | 1 |
| 54432 | NodeHealthReportExpired | Vypršela platnost existující sestavy stavu uzlu | HM | Informativní | 1 |

**Události sestavy stavu aplikace**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Byla vytvořena nová sestava stavu aplikace. Jde o nenasazené aplikace. | HM | Informativní | 1 |
| 54426 | DeployedApplicationNewHealthReport | Vytvoření nové sestavy stavu nasazení aplikace | HM | Informativní | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Vytvoření nové sestavy nasazené služby stavu | HM | Informativní | 1 |
| 54434 | ApplicationHealthReportExpired | Vypršela platnost existující sestavy stavu aplikace | HM | Informativní | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Vypršela platnost existující sestavy stavu nasazení aplikace | HM | Informativní | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Vypršela platnost existující sestavy nasazené služby stavu | HM | Informativní | 1 |

**Sestava události služby Service health**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Nová sestava health service byla vytvořena. | HM | Informativní | 1 |
| 54433 | ServiceHealthReportExpired | Vypršela platnost existující sestavy stavu služby | HM | Informativní | 1 |

**Události sestavy stav oddílu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Vytvoření nové sestavy stav oddílu | HM | Informativní | 1 |
| 54431 | PartitionHealthReportExpired | Existující sestavy stav oddílu vypršela platnost. | HM | Informativní | 1 |

**Události sestavy stavu repliky**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Sestava stavu stavové replika byla vytvořena. | HM | Informativní | 1 |
| 54430 | StatelessInstanceNewHealthReport | Nová sestava stavu bezstavové instance byla vytvořena. | HM | Informativní | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Existující sestavy stavu stavové repliky vypršela platnost. | HM | Informativní | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Existující sestavy stavu bezstavové instance vypršela platnost. | HM | Informativní | 1 |

## <a name="chaos-testing-events"></a>Testování události chaosu 

**Chaos relace události**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Bylo zahájeno Chaos testovací relaci | Testovatelnosti | Informativní | 1 |
| 50023 | ChaosStopped | Chaos testování relace byla zastavena. | Testovatelnosti | Informativní | 1 |

**Chaos uzel události**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Uzel je naplánováno restartuje jako součást Chaos testovací relaci | Testovatelnosti | Informativní | 1 |
| 50087 | ChaosNodeRestartCompleted | Uzel dokončení se restartuje jako součást Chaos testovací relaci | Testovatelnosti | Informativní | 1 |

**Události aplikace chaosu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Restartování balíček kódu je naplánovaná během Chaos testovací relaci | Testovatelnosti | Informativní | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Restartování balíček kódu byla dokončena během Chaos testovací relaci | Testovatelnosti | Informativní | 1 |

**Chaos oddílu události**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Primární oddíl je naplánováno přesunout jako součást Chaos testovací relaci | Testovatelnosti | Informativní | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Sekundární oddílu je naplánováno přesunout jako součást Chaos testovací relaci | Testovatelnosti | Informativní | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Hlubší analýza přesunutí primární oddíl je k dispozici | Testovatelnosti | Informativní | 1 |

**Události repliky chaosu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Restartování replika byla plánována jako součást Chaos testovací relaci | Testovatelnosti | Informativní | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Jako součást Chaos testovací relaci bylo naplánováno odstranění repliky | Testovatelnosti | Informativní | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Odstranění repliky byla dokončena jako součást Chaos testovací relaci | Testovatelnosti | Informativní | 1 |

## <a name="other-events"></a>Další události

**Události modulu Correlation**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Byl zjištěn korelaci | Testovatelnosti | Informativní | 1 |

## <a name="events-prior-to-version-62"></a>Události starší než verze 6.2

Tady je úplný seznam událostí Service Fabric poskytuje starší než verze 6.2.

| ID události | Název | Zdroj (úloha) | Úroveň |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativní |
| 25621 | NodeOpenedSuccess | FabricNode | Informativní |
| 25622 | NodeOpenedFailed | FabricNode | Informativní |
| 25623 | NodeClosing | FabricNode | Informativní |
| 25624 | NodeClosed | FabricNode | Informativní |
| 25625 | NodeAborting | FabricNode | Informativní |
| 25626 | NodeAborted | FabricNode | Informativní |
| 29627 | ClusterUpgradeStart | CM | Informativní |
| 29628 | ClusterUpgradeComplete | CM | Informativní |
| 29629 | ClusterUpgradeRollback | CM | Informativní |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativní |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativní |
| 23074 | ContainerActivated | Hosting | Informativní |
| 23075 | ContainerDeactivated | Hosting | Informativní |
| 29620 | ApplicationCreated | CM | Informativní |
| 29621 | ApplicationUpgradeStart | CM | Informativní |
| 29622 | ApplicationUpgradeComplete | CM | Informativní |
| 29623 | ApplicationUpgradeRollback | CM | Informativní |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativní |
| 29625 | ApplicationDeleted | CM | Informativní |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativní |
| 18566 | ServiceCreated | FM | Informativní |
| 18567 | ServiceDeleted | FM | Informativní |

## <a name="next-steps"></a>Další postup

* Získejte přehled o [diagnostiky v Service Fabric](service-fabric-diagnostics-overview.md)
* Další informace o Eventstoru v [Service Fabric – přehled Eventstoru](service-fabric-diagnostics-eventstore.md)
* Úprava vaše [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurace shromažďování protokolů více
* [Nastavení Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zobrazíte vaší provozní kanál protokoly
