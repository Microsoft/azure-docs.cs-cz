---
title: Seznam událostí pro Azure Service Fabric
description: Úplný seznam událostí poskytovaných službou Azure Service Fabric, které vám pomůžou monitorovat clustery.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258535"
---
# <a name="list-of-service-fabric-events"></a>Seznam událostí Service Fabric 

Service Fabric zveřejňuje primární sadu událostí clusteru, aby informovala o stavu clusteru jako [Service Fabricch událostí](service-fabric-diagnostics-events.md). Jsou založené na akcích, které provádí Service Fabric na vašich uzlech a na rozhodnutích o clusteru nebo správě provedených vlastníkem nebo operátorem clusteru. K těmto událostem může být umožněno konfigurace několika způsobů, včetně konfigurace [Azure monitor protokolů s clusterem](service-fabric-diagnostics-oms-setup.md)nebo dotazování na [eventstoru](service-fabric-diagnostics-eventstore.md). V počítačích s Windows se tyto události dodají do protokolu událostí, takže v Prohlížeč událostí uvidíte události Service Fabric. 

Tady jsou některé charakteristiky těchto událostí.
* Každá událost je svázána se specifickou entitou v clusteru, např. aplikace, služba, uzel, replika.
* Každá událost obsahuje sadu společných polí: EventInstanceId, EventName a Category.
* Každá událost obsahuje pole, která propojují událost zpátky k entitě, ke které je přidružena. Například událost ApplicationCreated by měla pole, která identifikují název vytvořené aplikace.
* Události jsou strukturované takovým způsobem, že je lze spotřebovat v různých nástrojích, aby bylo možné provádět další analýzu. Kromě toho jsou relevantní podrobnosti události definovány jako samostatné vlastnosti na rozdíl od dlouhého řetězce. 
* Události jsou zapisovány různými subsystémy v Service Fabric jsou označeny zdrojem (úlohou) níže. Další informace jsou k dispozici v těchto podsystémech [Service Fabric architektury](service-fabric-architecture.md) a [Service Fabric technický přehled](service-fabric-technical-overview.md).

Tady je seznam těchto událostí Service Fabric uspořádaných podle entity.

## <a name="cluster-events"></a>Události clusteru

**Události upgradu clusteru**

Další podrobnosti o upgradech clusteru najdete [tady](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Upgradovat | Upgrade clusteru se spustil. | CM | Informativní |
| 29628 | ClusterUpgradeCompleted | Upgradovat | Upgrade clusteru byl dokončen. | CM | Informativní | 
| 29629 | ClusterUpgradeRollbackStarted | Upgradovat | Upgrade clusteru se začal vrátit zpátky.  | CM | Upozornění | 
| 29630 | ClusterUpgradeRollbackCompleted | Upgradovat | Upgrade clusteru se dokončil zpátky. | CM | Upozornění | 
| 29631 | ClusterUpgradeDomainCompleted | Upgradovat | Upgrade domény byl dokončen během upgradu clusteru. | CM | Informativní | 

## <a name="node-events"></a>Události uzlu

**Události životního cyklu uzlu** 

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Deaktivace uzlu se dokončila. | FM | Informativní | 
| 18603 | NodeUp | StateTransition | Cluster zjistil, že se spustil uzel. | FM | Informativní | 
| 18604 | NodeDown | StateTransition | Cluster zjistil vypnutí uzlu. Během restartování uzlu se zobrazí událost NodeDown následovaná událostí NodeUp. |  FM | Chyba | 
| 18605 | NodeAddedToCluster | StateTransition |  Do clusteru se přidal nový uzel a Service Fabric můžou do tohoto uzlu nasadit aplikace. | FM | Informativní | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Uzel byl odebrán z clusteru. Service Fabric už nebude nasazovat aplikace do tohoto uzlu. | FM | Informativní | 
| 18607 | NodeDeactivateStarted | StateTransition |  Bylo zahájeno deaktivace uzlu. | FM | Informativní | 
| 25621 | NodeOpenSucceeded | StateTransition |  Uzel byl úspěšně spuštěn. | FabricNode | Informativní | 
| 25622 | NodeOpenFailed | StateTransition |  Uzel se nepovedlo spustit a připojit k okruhu. | FabricNode | Chyba | 
| 25624 | NodeClosed | StateTransition |  Uzel se úspěšně vypnul. | FabricNode | Informativní | 
| 25626 | NodeAborted | StateTransition |  Uzel se nekorektně vypnul. | FabricNode | Chyba | 

## <a name="application-events"></a>Události aplikace

**Události životního cyklu aplikace**

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Nejčastější | Byla vytvořena nová aplikace. | CM | Informativní | 
| 29625 | ApplicationDeleted | Nejčastější | Existující aplikace byla odstraněna. | CM | Informativní | 
| 23083 | ApplicationProcessExited | Nejčastější | Proces v rámci aplikace se ukončil. | Hosting | Informativní | 

**Události upgradu aplikace**

Další podrobnosti o upgradech aplikací najdete [tady](service-fabric-application-upgrade.md).

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Upgradovat | Byl spuštěn upgrade aplikace. | CM | Informativní | 
| 29622 | ApplicationUpgradeCompleted | Upgradovat | Upgrade aplikace byl dokončen. | CM | Informativní | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgradovat | Upgrade aplikace se začal odvolat. |CM | Upozornění | 
| 29624 | ApplicationUpgradeRollbackCompleted | Upgradovat | Byl dokončen návrat do upgradu aplikace | CM | Upozornění | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgradovat | Upgrade domény byl dokončen během upgradu aplikace. | CM | Informativní | 

## <a name="service-events"></a>Události služby

**Události životního cyklu služby**

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Nejčastější | Vytvořila se nová služba. | FM | Informativní | 
| 18658 | ServiceDeleted | Nejčastější | Existující služba se odstranila. | FM | Informativní | 

## <a name="partition-events"></a>Rozdělit události na oddíly

**Přesunout události oddílu**

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Nejčastější | Konfigurace oddílu se dokončila. | R | Informativní | 

## <a name="replica-events"></a>Události repliky

**Události životního cyklu repliky**

| EventId | Název | Kategorie | Popis |Zdroj (úloha) | Úroveň |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Nejčastější | Byl otevřen spolehlivý slovník. | DistributedDictionary | Informativní |
| 61702 | ReliableDictionaryClosed | Nejčastější | Spolehlivý slovník se zavřel. | DistributedDictionary | Informativní |
| 61703 | ReliableDictionaryCheckpointRecovered | Nejčastější | Spolehlivý slovník obnovil svůj kontrolní bod. | DistributedDictionary | Informativní |
| 61704 | ReliableDictionaryCheckpointFilesSent | Nejčastější | Replika odeslala soubory kontrolních bodů spolehlivého slovníku. | DistributedDictionary | Informativní |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Nejčastější | Replika přijala soubory kontrolních bodů spolehlivého slovníku. | DistributedDictionary | Informativní |
| 61963 | ReliableQueueOpened | Nejčastější | Spolehlivá fronta byla otevřena. | DistributedQueue | Informativní |
| 61964 | ReliableQueueClosed | Nejčastější | Spolehlivá fronta byla zavřena. | DistributedQueue | Informativní |
| 61965 | ReliableQueueCheckpointRecovered | Nejčastější | Spolehlivá fronta obnovila svůj kontrolní bod. | DistributedQueue | Informativní |
| 61966 | ReliableQueueCheckpointFilesSent | Nejčastější | Replika odeslala soubory kontrolních bodů spolehlivé fronty. | DistributedQueue | Informativní |
| 63647 | ReliableQueueCheckpointFilesReceived | Nejčastější | Replika přijala soubory kontrolních bodů spolehlivé fronty. | DistributedQueue | Informativní |
| 63648 | ReliableConcurrentQueueOpened | Nejčastější | Spolehlivá souběžná fronta byla otevřena. | ReliableConcurrentQueue | Informativní |
| 63649 | ReliableConcurrentQueueClosed | Nejčastější | Spolehlivá souběžná fronta byla zavřena. | ReliableConcurrentQueue | Informativní |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Nejčastější | Spolehlivá souběžná fronta obnovila svůj kontrolní bod. | ReliableConcurrentQueue | Informativní |
| 61687 | TStoreError | Nezdařilo se | Spolehlivá kolekce přijala neočekávanou chybu. | TStore | Chyba |
| 63831 | PrimaryFullCopyInitiated | Nejčastější | Primární replika iniciovala úplnou kopii. | TReplicator | Informativní |
| 63832 | PrimaryPartialCopyInitiated | Nejčastější | Primární replika iniciovala částečnou kopii. | TReplicator | Informativní |
| 16831 | BuildIdleReplicaStarted | Nejčastější | Primární replika zahájila sestavování nečinné repliky. | Replikace | Informativní |
| 16832 | BuildIdleReplicaCompleted | Nejčastější | U primární repliky se dokončilo vytváření nečinné repliky. | Replikace | Informativní |
| 16833 | BuildIdleReplicaFailed | Nejčastější | Primární replika selhala při vytváření nečinné repliky. | Replikace | Upozornění |
| 16834 | PrimaryReplicationQueueFull | Stav | Fronta replikace primární repliky je plná. | Replikace | Upozornění |
| 16835 | PrimaryReplicationQueueWarning | Stav | Fronta replikace primární repliky je blízko úplného. | Replikace | Upozornění |
| 16836 | PrimaryReplicationQueueWarningMitigated | Stav | Fronta replikace primární repliky je v pořádku. | Replikace | Informativní |
| 16837 | SecondaryReplicationQueueFull | Stav | Fronta replikace sekundární repliky je plná. | Replikace | Upozornění |
| 16838 | SecondaryReplicationQueueWarning | Stav | Fronta replikace sekundární repliky je blízko úplného. | Replikace | Upozornění |
| 16839 | SecondaryReplicationQueueWarningMitigated | Stav | Fronta replikace sekundární repliky je v pořádku. | Replikace | Informativní |
| 16840 | PrimaryFaultedSlowSecondary | Stav | U primární repliky došlo k chybě pomalé sekundární repliky. | Replikace | Upozornění |
| 16841 | ReplicatorFaulted | Stav | V replice došlo k chybě. | Replikace | Upozornění |

## <a name="container-events"></a>Události kontejneru

**Události životního cyklu kontejneru** 

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Kontejner se spustil. | Hosting | Informativní | 1 |
| 23075 | ContainerDeactivated | Kontejner se zastavil. | Hosting | Informativní | 1 |
| 23082 | ContainerExited | Kontejner se ukončil – Podívejte se na příznak UnexpectedTermination. | Hosting | Informativní | 1 |

## <a name="health-reports"></a>Sestavy stavu

[Model Service Fabric Health](service-fabric-health-introduction.md) poskytuje bohatě, flexibilní a rozšiřitelné vyhodnocení stavu a vytváření sestav. Počínaje Service Fabric verze 6,2 se data o stavu zapisují jako události platformy, aby poskytovaly historické záznamy o stavu. Chcete-li udržet objem událostí stavu nízká, zapište pouze následující události Service Fabric:

* Všechny sestavy o stavu `Error` nebo `Warning`
* sestavy o stavu `Ok` během přechodů
* Když vyprší platnost `Error` nebo `Warning` události stavu. Dá se použít k určení, jak dlouho není entita v pořádku.

**Události sestav stavu clusteru**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | K dispozici je nová Sestava stavu clusteru. | HM | Informativní | 1 |
| 54437 | ClusterHealthReportExpired | Platnost existující sestavy stavu clusteru vypršela. | HM | Informativní | 1 |

**Události sestav stavu uzlu**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Je k dispozici nová Sestava stavu uzlu. | HM | Informativní | 1 |
| 54432 | NodeHealthReportExpired | Platnost existující sestavy stavu uzlu vypršela. | HM | Informativní | 1 |

**Události sestavy stavu aplikace**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Byla vytvořena nová sestava o stavu aplikace. Toto je pro nenasazené aplikace. | HM | Informativní | 1 |
| 54426 | DeployedApplicationNewHealthReport | Vytvořila se nová Sestava stavu nasazené aplikace. | HM | Informativní | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Byla vytvořena nová nasazená Sestava služby Service Health. | HM | Informativní | 1 |
| 54434 | ApplicationHealthReportExpired | Platnost existující sestavy stavu aplikace vypršela. | HM | Informativní | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Platnost existující nasazené sestavy o stavu aplikace vypršela. | HM | Informativní | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Platnost existující nasazené sestavy služby Service Health vypršela. | HM | Informativní | 1 |

**Události sestav stavu služby**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Byla vytvořena nová Sestava služby Service Health. | HM | Informativní | 1 |
| 54433 | ServiceHealthReportExpired | Platnost existující sestavy služby Service Health vypršela. | HM | Informativní | 1 |

**Události sestav stavu oddílu**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Vytvořila se nová Sestava stavu oddílů. | HM | Informativní | 1 |
| 54431 | PartitionHealthReportExpired | Platnost existující sestavy stavu oddílu vypršela. | HM | Informativní | 1 |

**Události sestav stavu repliky**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Byla vytvořena stavová zpráva o stavu repliky. | HM | Informativní | 1 |
| 54430 | StatelessInstanceNewHealthReport | Byla vytvořena nová stavová zpráva o stavu instance. | HM | Informativní | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Platnost stávající stavové repliky stavu vypršela. | HM | Informativní | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Platnost stávající stavové sestavy stavu bez stavů vypršela. | HM | Informativní | 1 |

## <a name="chaos-testing-events"></a>Události testování chaos 

**Události relace chaos**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Spustila se relace testování chaos. | Testovatelnosti | Informativní | 1 |
| 50023 | ChaosStopped | Relace testování chaos se zastavila. | Testovatelnosti | Informativní | 1 |

**Události uzlu chaos**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Uzel se naplánoval restart jako součást relace testování chaos. | Testovatelnosti | Informativní | 1 |
| 50087 | ChaosNodeRestartCompleted | Restart uzlu byl dokončen v rámci relace testování chaos | Testovatelnosti | Informativní | 1 |

**Události aplikace chaos**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Během relace testování chaos se naplánoval restart balíčku kódu. | Testovatelnosti | Informativní | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Během relace testování chaos se dokončil restart balíčku kódu. | Testovatelnosti | Informativní | 1 |

**Události oddílů chaos**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Primární oddíl se naplánoval na přesun v rámci relace chaos testování. | Testovatelnosti | Informativní | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Sekundární oddíl má naplánovaný přesun v rámci relace chaos testování. | Testovatelnosti | Informativní | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | K dispozici je hlubší analýza primárního oddílu přesunutí. | Testovatelnosti | Informativní | 1 |

**Události repliky chaos**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Restart repliky byl naplánován jako součást relace testování chaos. | Testovatelnosti | Informativní | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Odebrání repliky bylo naplánováno jako součást relace testování chaos. | Testovatelnosti | Informativní | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Odebrání repliky bylo dokončeno jako součást relace testování chaos. | Testovatelnosti | Informativní | 1 |

## <a name="other-events"></a>Další události

**Události korelace**

| EventId | Název | Popis |Zdroj (úloha) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Zjistila se korelace. | Testovatelnosti | Informativní | 1 |

## <a name="events-prior-to-version-62"></a>Události před verzí 6,2

Zde je vyčerpávající seznam událostí poskytovaných Service Fabric před verzí 6,2.

| EventId | Název | Zdroj (úloha) | Úroveň |
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

## <a name="next-steps"></a>Další kroky

* Získejte přehled [diagnostiky v Service Fabric](service-fabric-diagnostics-overview.md)
* Další informace o Eventstoru najdete v článku [Přehled nástroje Service Fabric eventstoru](service-fabric-diagnostics-eventstore.md) .
* Úprava konfigurace [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pro shromažďování dalších protokolů
* [Nastavení Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) pro zobrazení protokolů provozních kanálů
