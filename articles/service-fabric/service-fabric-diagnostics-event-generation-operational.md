---
title: Seznam událostí infrastruktury služeb Azure
description: Úplný seznam událostí poskytovaných azure service fabric pomoci sledovat clustery.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258535"
---
# <a name="list-of-service-fabric-events"></a>Seznam událostí Service Fabric 

Service Fabric zpřístupňuje primární sadu událostí clusteru, která vás informuje o stavu clusteru jako [události service fabric](service-fabric-diagnostics-events.md). Ty jsou založeny na akcích prováděných Service Fabric na uzlech a vaše clusteru nebo rozhodnutí o správě provedené vlastníkem clusteru nebo operátor. K těmto událostem lze přistupovat pomocí konfigurace v mnoha způsoby, včetně konfigurace [protokolů Azure Monitor s clusteru](service-fabric-diagnostics-oms-setup.md)nebo dotazování [EventStore](service-fabric-diagnostics-eventstore.md). V počítačích se systémem Windows jsou tyto události vloženy do aplikace EventLog – takže můžete zobrazit události service fabric v Prohlížeči událostí. 

Zde jsou některé charakteristiky těchto událostí
* Každá událost je vázána na určitou entitu v clusteru, například aplikace, služba, uzel, replika.
* Každá událost obsahuje sadu společných polí: EventInstanceId, EventName a Category.
* Každá událost obsahuje pole, která spojují událost zpět s entitou, ke které je přidružena. Například ApplicationCreated událost by měla pole, která identifikují název aplikace vytvořena.
* Události jsou strukturovány tak, že mohou být konzumovány v různých nástrojích pro další analýzu. Kromě toho relevantní podrobnosti pro událost jsou definovány jako samostatné vlastnosti na rozdíl od dlouhé String. 
* Události jsou zapsány různými subsystémy v Service Fabric jsou identifikovány Source(Task) níže. Další informace jsou k dispozici na těchto subsystémech v [technickém přehledu architektury service fabric](service-fabric-architecture.md) a [service fabric](service-fabric-technical-overview.md).

Zde je seznam těchto událostí Service Fabric uspořádaných podle entity.

## <a name="cluster-events"></a>Události clusteru

**Události upgradu clusteru**

Další podrobnosti o upgradech clusteru naleznete [zde](service-fabric-cluster-upgrade-windows-server.md).

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | Clusterupgradebyl. | Upgrade | Byla zahájena inovace clusteru. | CM | Informační |
| 29628 | ClusterUpgrade dokončen | Upgrade | Upgrade clusteru byl dokončen. | CM | Informační | 
| 29629 | ClusterUpgradeRollbackStarted | Upgrade | Upgrade clusteru se začal vrátit zpět.  | CM | Upozornění | 
| 29630 | ClusterUpgradeRollbackDokončeno | Upgrade | Upgrade clusteru dokončil vrácení zpět. | CM | Upozornění | 
| 29631 | ClusterUpgradeDomainDokončen | Upgrade | Upgradovaná doména byla dokončena při upgradu clusteru. | CM | Informační | 

## <a name="node-events"></a>Události uzlu

**Události životního cyklu uzlu** 

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Deaktivace uzlu byla dokončena. | FM | Informační | 
| 18603 | NodeUp | StateTransition | Cluster zjistil, že byl spuštěn uzel. | FM | Informační | 
| 18604 | NodeDown | StateTransition | Cluster zjistil, že uzel byl ukončen. Během restartování uzlu se zobrazí událost NodeDown následovaná událostí NodeUp |  FM | Chyba | 
| 18605 | NodeAddedToCluster | StateTransition |  Do clusteru byl přidán nový uzel a service fabric může nasadit aplikace do tohoto uzlu. | FM | Informační | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Uzel byl odebrán z clusteru. Service Fabric již nebude nasazovat aplikace do tohoto uzlu | FM | Informační | 
| 18607 | NodeDeactivateStarted | StateTransition |  Deaktivace uzlu byla zahájena. | FM | Informační | 
| 25621 | NodeOpenSucceeded | StateTransition |  Uzel byl úspěšně spuštěn. | Látková uzel | Informační | 
| 25622 | NodeOpenFailed | StateTransition |  Spuštění uzlu a připojení ke kroužku se nezdařilo. | Látková uzel | Chyba | 
| 25624 | Uzel uzavřeno | StateTransition |  Uzel byl úspěšně ukončen. | Látková uzel | Informační | 
| 25626 | NodeAborted | StateTransition |  Uzel má ungracefully vypnout | Látková uzel | Chyba | 

## <a name="application-events"></a>Události aplikace

**Události životního cyklu aplikace**

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | Aplikace Vytvořená | Životního cyklu | Byla vytvořena nová aplikace. | CM | Informační | 
| 29625 | ApplicationDeleted | Životního cyklu | Existující aplikace byla odstraněna. | CM | Informační | 
| 23083 | ApplicationProcessExited | Životního cyklu | Proces v rámci aplikace byl ukončen. | Hostování | Informační | 

**Události upgradu aplikace**

Více informací o upgradu aplikací naleznete [zde](service-fabric-application-upgrade.md).

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | Upgrade aplikace byl zahájen. | Upgrade | Byl zahájen upgrade aplikace. | CM | Informační | 
| 29622 | Upgrade aplikacebyl dokončen. | Upgrade | Byl dokončen upgrade aplikace. | CM | Informační | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgrade | Upgrade aplikace se začal vrátit zpět. |CM | Upozornění | 
| 29624 | ApplicationUpgradeRollbackCompleted | Upgrade | Upgrade aplikace dokončil vrácení zpět. | CM | Upozornění | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgrade | Upgradovaná doména byla dokončena při upgradu aplikace. | CM | Informační | 

## <a name="service-events"></a>Události služby

**Události životního cyklu služby**

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň | 
| --- | --- | ---| --- | --- | --- |
| 18657 | Vytvoření služby | Životního cyklu | Byla vytvořena nová služba. | FM | Informační | 
| 18658 | Služba byla odstraněna. | Životního cyklu | Existující služba byla odstraněna. | FM | Informační | 

## <a name="partition-events"></a>Události oddílu

**Události přesunutí oddílu**

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň | 
| --- | --- | ---| --- | --- | --- |
| 18940 | OddílPřekonfigurováno | Životního cyklu | Byla dokončena změna konfigurace oddílu. | RA | Informační | 

## <a name="replica-events"></a>Události repliky

**Události životního cyklu repliky**

| Eventid | Name (Název) | Kategorie | Popis |Zdroj (úkol) | Úroveň |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOtevřeno | Životního cyklu | Otevřel se spolehlivý slovník | DistributedDictionary | Informační |
| 61702 | ReliableDictionaryClosed | Životního cyklu | Spolehlivý slovník byl uzavřen | DistributedDictionary | Informační |
| 61703 | ReliableDictionaryCheckpointRecovered | Životního cyklu | Spolehlivý slovník obnovil svůj kontrolní bod | DistributedDictionary | Informační |
| 61704 | ReliableDictionaryCheckpointFilesSent | Životního cyklu | Replika odeslala soubory kontrolních bodů spolehlivého slovníku. | DistributedDictionary | Informační |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Životního cyklu | Replika obdržela spolehlivé soubory kontrolních bodů slovníku | DistributedDictionary | Informační |
| 61963 | ReliableQueueOpened | Životního cyklu | Byla otevřena spolehlivá fronta | Distribuovaná fronta | Informační |
| 61964 | ReliableQueueClosed | Životního cyklu | Spolehlivá fronta byla uzavřena. | Distribuovaná fronta | Informační |
| 61965 | ReliableQueueCheckpointRecovered | Životního cyklu | Spolehlivá fronta obnovila svůj kontrolní bod | Distribuovaná fronta | Informační |
| 61966 | ReliableQueueCheckpointFilesSent | Životního cyklu | Replika odeslala spolehlivé soubory kontrolních bodů fronty. | Distribuovaná fronta | Informační |
| 63647 | Přijaté soubory ReliableQueueCheckpointFilesReceived | Životního cyklu | Replika obdržela spolehlivé soubory kontrolních bodů fronty | Distribuovaná fronta | Informační |
| 63648 | SpolehlivéConcurrentQueueOtevřeno | Životního cyklu | Byla otevřena spolehlivá souběžná fronta. | Spolehlivásouběžná fronta | Informační |
| 63649 | ReliableConcurrentQueueClosed | Životního cyklu | Spolehlivá souběžná fronta byla uzavřena. | Spolehlivásouběžná fronta | Informační |
| 63650 | SpolehlivéConcurrentQueueCheckpointRecovered | Životního cyklu | Spolehlivá souběžná fronta obnovila svůj kontrolní bod. | Spolehlivásouběžná fronta | Informační |
| 61687 | Chyba tstore | Selhání | Spolehlivá kolekce byla přijata neočekávaná chyba | Tstore | Chyba |
| 63831 | Primární FullCopyInitiated | Životního cyklu | Primární replika iniciovala úplnou kopii. | TReplicator | Informační |
| 63832 | Primárnípartialcopyinitiated | Životního cyklu | Primární replika iniciovala částečnou kopii. | TReplicator | Informační |
| 16831 | Buildidlereplikabyla | Životního cyklu | Primární replika začala vytvářet nečinnou repliku. | Replikace | Informační |
| 16832 | BuildIdleReplicaCompleted | Životního cyklu | Primární replika dokončila nečinnost repliky sestavení | Replikace | Informační |
| 16833 | Sestavenínefunkční repliky se nezdařilo | Životního cyklu | Primární replika selhala v nečinnosti repliky. | Replikace | Upozornění |
| 16834 | Primární replikaceFrontfull | Stav | Fronta replik primární repliky je plná. | Replikace | Upozornění |
| 16835 | Upozornění na primární replikaci fronty | Stav | Fronta replik primární repliky je téměř úplná. | Replikace | Upozornění |
| 16836 | Upozornění na primaryreplicationqueueupozornění | Stav | Fronta replik primární repliky je v pořádku. | Replikace | Informační |
| 16837 | SecondaryReplicationQueueFull | Stav | Fronta replikace sekundární repliky je plná. | Replikace | Upozornění |
| 16838 | Upozornění na sekundární replikaci fronty | Stav | Fronta replikace sekundární repliky je téměř úplná. | Replikace | Upozornění |
| 16839 | Upozornění na secondaryReplicationQueue | Stav | Fronta replikace sekundární repliky je v pořádku. | Replikace | Informační |
| 16840 | PrimaryFaultedSlowSekundární | Stav | Primární replika chybovala v pomalé sekundární replice | Replikace | Upozornění |
| 16841 | ReplicatorFaulted | Stav | Replika byla chybná | Replikace | Upozornění |

## <a name="container-events"></a>Události kontejneru

**Události životního cyklu kontejneru** 

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | Kontejneraktivován | Kontejner byl spuštěn | Hostování | Informační | 1 |
| 23075 | KontejnerDeaktivovat | Kontejner byl zastaven | Hostování | Informační | 1 |
| 23082 | ContainerExited | Kontejner byl ukončen – kontrola příznaku Neočekávané ukončení | Hostování | Informační | 1 |

## <a name="health-reports"></a>Zdravotní zprávy

[Model stavu service fabric](service-fabric-health-introduction.md) poskytuje bohaté, flexibilní a rozšiřitelné hodnocení stavu a vykazování. Spuštění service fabric verze 6.2, data o stavu je zapsána jako události platformy poskytnout historické záznamy o stavu. Chcete-li zachovat nízký objem událostí stavu, zapíšeme pouze následující události service fabric:

* Všechny `Error` `Warning` nebo zdravotní zprávy
* `Ok`zdravotní zprávy během přechodů
* Když `Error` vyprší `Warning` platnost události nebo stavu. To lze použít k určení, jak dlouho byla entita nev pořádku.

**Události sestavy stavu clusteru**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | Sestava clusteru NewHealthReport | K dispozici je nová sestava stavu clusteru. | Hm | Informační | 1 |
| 54437 | Vypršela platnost sestavy clusteru HealthReport | Platnost existující sestavy stavu clusteru vypršela. | Hm | Informační | 1 |

**Události sestavy stavu uzlu**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | K dispozici je nová sestava stavu uzlu. | Hm | Informační | 1 |
| 54432 | Platnost zprávy NodeHealthReport vypršela. | Platnost existující sestavy stavu uzlu vypršela. | Hm | Informační | 1 |

**Události sestavy stavu aplikace**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | AplikaceNewHealthReport | Byla vytvořena nová sestava stavu aplikace. Toto je pro nenasazené aplikace. | Hm | Informační | 1 |
| 54426 | DeployedApplicationNewHealthReport | Byla vytvořena nová sestava stavu nasazené aplikace. | Hm | Informační | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Byla vytvořena nová sestava stavu nasazené služby. | Hm | Informační | 1 |
| 54434 | ApplicationHealthReportVypršela | Platnost existující zprávy o stavu aplikace vypršela. | Hm | Informační | 1 |
| 54435 | Platnost deployedApplicationHealthReportvypršela platnost | Platnost existující sestavy stavu nasazené aplikace vypršela. | Hm | Informační | 1 |
| 54436 | Platnost deployedServicePackageHealthReportvypršela | Platnost existující sestavy stavu nasazené služby vypršela. | Hm | Informační | 1 |

**Události sestavy stavu služby**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Byla vytvořena nová sestava stavu služby. | Hm | Informační | 1 |
| 54433 | ServiceHealthReportVypršela | Platnost existující zprávy o stavu služby vypršela. | Hm | Informační | 1 |

**Události sestavy stavu oddílu**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Byla vytvořena nová sestava stavu oddílu. | Hm | Informační | 1 |
| 54431 | Platnost sestavy PartitionHealthReportvypršela | Platnost existující sestavy stavu oddílu vypršela. | Hm | Informační | 1 |

**Události sestavy stavu repliky**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StavováreplikaNewHealthReport | Byla vytvořena stavová sestava stavu repliky. | Hm | Informační | 1 |
| 54430 | Sestava bezstavové instanceNewHealthReport | Byla vytvořena nová sestava stavu instance bez stavů. | Hm | Informační | 1 |
| 54438 | Vypršela platnost sestavyReplikHealthReport | Platnost existující sestavy stavu repliky vypršela. | Hm | Informační | 1 |
| 54439 | Vypršela platnost zprávy Bezstavová instance HealthReport | Platnost existující sestavy stavu instance bez státní příslušnosti vypršela. | Hm | Informační | 1 |

## <a name="chaos-testing-events"></a>Události testování chaosu 

**Události relace chaosu**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosZačal | Byla zahájena relace testování chaosu | Testovatelnosti | Informační | 1 |
| 50023 | Chaoszastavený | Relace testování chaosu byla zastavena. | Testovatelnosti | Informační | 1 |

**Události uzlu Chaos**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Uzel má naplánováno restartování jako součást relace testování Chaos | Testovatelnosti | Informační | 1 |
| 50087 | ChaosNodeRestartDokončeno | Uzel dokončil restartování jako součást relace testování Chaos | Testovatelnosti | Informační | 1 |

**Události aplikace Chaos**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartOdplánováno | Během relace testování chaosu bylo naplánováno restartování balíčku kódu. | Testovatelnosti | Informační | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Restartování balíčku kódu bylo dokončeno během relace testování chaosu | Testovatelnosti | Informační | 1 |

**Události oddílu Chaos**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMovePlánovánd | Primární oddíl byl naplánován pro přesun jako součást relace testování Chaos | Testovatelnosti | Informační | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Sekundární oddíl je naplánován o přesunutí v rámci relace testování Chaos | Testovatelnosti | Informační | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Hlubší analýza primárního přesunu oddílu je k dispozici | Testovatelnosti | Informační | 1 |

**Události repliky chaosu**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Restartování repliky bylo naplánováno jako součást relace testování chaosu. | Testovatelnosti | Informační | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Odebrání repliky bylo naplánováno jako součást relace testování chaosu. | Testovatelnosti | Informační | 1 |
| 50093 | ChaosReplicaRemovalDokončeno | Odebrání repliky bylo dokončeno jako součást relace testování chaosu. | Testovatelnosti | Informační | 1 |

## <a name="other-events"></a>Další akce

**Korelační události**

| Eventid | Name (Název) | Popis |Zdroj (úkol) | Úroveň | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | KorelaceProvozní | Byla zjištěna korelace. | Testovatelnosti | Informační | 1 |

## <a name="events-prior-to-version-62"></a>Události před verzí 6.2

Zde je úplný seznam událostí poskytovaných Service Fabric před verzí 6.2.

| Eventid | Name (Název) | Zdroj (úkol) | Úroveň |
| --- | --- | --- | --- |
| 25620 | Otevření uzlu | Látková uzel | Informační |
| 25621 | NodeOpenedÚspěch | Látková uzel | Informační |
| 25622 | Uzel Otevřeno se nezdařilo. | Látková uzel | Informační |
| 25623 | Uzavření uzlu | Látková uzel | Informační |
| 25624 | Uzel uzavřeno | Látková uzel | Informační |
| 25625 | NodeAborting | Látková uzel | Informační |
| 25626 | NodeAborted | Látková uzel | Informační |
| 29627 | ClusterUpgradeStart | CM | Informační |
| 29628 | ClusterUpgradeComplete | CM | Informační |
| 29629 | ClusterUpgradeRollback | CM | Informační |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informační |
| 29631 | ClusterUpgradeDomainComplete | CM | Informační |
| 23074 | Kontejneraktivován | Hostování | Informační |
| 23075 | KontejnerDeaktivovat | Hostování | Informační |
| 29620 | Aplikace Vytvořená | CM | Informační |
| 29621 | ApplicationUpgradeStart | CM | Informační |
| 29622 | ApplicationUpgradeComplete | CM | Informační |
| 29623 | AplikaceUpgradeRollback | CM | Informační |
| 29624 | AplikaceUpgradeRollbackComplete | CM | Informační |
| 29625 | ApplicationDeleted | CM | Informační |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informační |
| 18566 | Vytvoření služby | FM | Informační |
| 18567 | Služba byla odstraněna. | FM | Informační |

## <a name="next-steps"></a>Další kroky

* Získejte přehled [diagnostiky v service fabric](service-fabric-diagnostics-overview.md)
* Další informace o přehledu eventstore v [service fabric](service-fabric-diagnostics-eventstore.md)
* Úprava konfigurace [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pro shromažďování dalších protokolů
* [Nastavení Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) pro zobrazení protokolů provozních kanálů
