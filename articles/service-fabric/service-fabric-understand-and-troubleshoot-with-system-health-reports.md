---
title: "Poradce při potížích s sestav o stavu systému | Microsoft Docs"
description: "Popisuje sestav stavu odesílají součásti Azure Service Fabric a jejich využití pro řešení potíží clusteru nebo problémy s aplikací"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: def4f1cdcd173e26964f9be11266d0e1a20fcafa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Řešení problémů pomocí sestav o stavu systému
Azure Service Fabric součásti poskytují sestavy stavu systému na všechny entity v clusteru okamžitě po nasazení. [Úložiště stavu](service-fabric-health-introduction.md#health-store) vytvoří nebo odstraní entit na základě sestav systému. Také slouží k uspořádání je v hierarchii, která zaznamená interakce entity.

> [!NOTE]
> Abyste pochopili související se stavem koncepty, další informace v [modelu stavu Service Fabric](service-fabric-health-introduction.md).
> 
> 

Sestav o stavu systému poskytují přehled o clusteru a příznak problémy a funkce aplikací. Pro aplikace a služby ověřte sestav o stavu systému entity jsou implementované a chovají správně z hlediska Service Fabric. Sestavy neposkytují žádné sledování stavu obchodní logiky služby nebo zjišťování "zamrzlých" procesů. Uživatel služby lze rozšířit údaje o stavu informace specifické pro jejich logiku.

> [!NOTE]
> Sestavy o stavu odeslaných watchdogs uživatele jsou viditelné pouze *po* součástech systému vytvořte entitu. Při odstranění entity úložiště zdravotní automaticky odstraní všechny sestavy stavu s ním spojená. Totéž platí, když je vytvořena nová instance entity. Příkladem je, když je vytvořena nová instance repliky stavové služby trvalý. Všechny sestavy přidružené k původní instanci jsou odstraněna a vyčistit z úložiště.
> 
> 

Součást systému sestavy, jsou identifikovány zdroj, který začíná "**systému.**" Předpona. Watchdogs nelze používat stejnou předponu pro jejich zdroje, jako jsou odmítnuta sestavy se neplatné parametry.

Podívejme se na některé sestavy systému, abyste pochopili, co je aktivuje a zjistěte, jak opravte potenciální problémy, které reprezentují.

> [!NOTE]
> Service Fabric i nadále přidat sestavy týkající se podmínek, které vylepšují získat přehled o dění v clusteru a aplikace. Existující sestavy lze rozšířit o další informace, které pomohou rychlejší vyřešení tohoto problému.
> 
> 

## <a name="cluster-system-health-reports"></a>Cluster sestav o stavu systému
Stav entity clusteru se vytvoří automaticky v health store. Pokud všechno funguje správně, nemá sestavu system.

### <a name="neighborhood-loss"></a>Ztráta Okolní počítače
**System.Federation** nahlásí chybu, když zjistí ztrátu okolí. Sestava je z jednotlivých uzlů a ID uzlu je součástí názvu vlastnosti. Pokud je jeden okolí ztratili v celé prstenec Service Fabric, můžete očekávat obvykle dvě události, které představují obou stranách sestavy mezera. Pokud další sousedství jsou ztraceny, existují další události.

Sestava Určuje časový limit globální zapůjčení jako time to live (TTL). Sestava je nutno každých poloviny doby trvání TTL pro podmínku zůstává aktivní. Událost je automaticky odstraněna po jeho vypršení. Vypršela platnost odebrat pokud zaručuje, že sestava je vyčištěna z health store správně, i v případě, že generování sestav uzlu je vypnutý.

* **SourceId**: System.Federation
* **Vlastnost**: začíná **okolí** a obsahuje informace o uzlu.
* **Další kroky**: Zjistěte, proč dojde ke ztrátě okolí. Například zkontrolujte komunikaci mezi uzly clusteru.

### <a name="rebuild"></a>Znovu sestavit

Služba Správce převzetí služeb při selhání (FM) spravuje informace o uzly clusteru. Když FM ztratí svoje data a přejde do ztrátě dat, nezaručuje, že má nejaktuálnější informace o uzly clusteru. V takovém případě systém prochází opětovném sestavení a System.FM shromažďuje data ze všech uzlů v clusteru, aby bylo možné znovu sestavit její stav. V některých případech kvůli sítě nebo problémů uzlu, opětovné sestavení můžete získat zablokované nebo bylo zastaveno. Stejná situace může nastat službou převzetí služeb při selhání Manager Master (FMM). FMM je bezstavové systémová služba, která udržuje zaznamenávat, kde jsou všechny FMs v clusteru. Primární FMM je vždy uzel s ID nejbližší na hodnotu 0. Pokud tento uzel získá vyřazen, aktivuje se opětovném sestavení.
Pokud k jedné z předchozích podmínek dojde, **System.FM** nebo **System.FMM** flags prostřednictvím zprávu o chybách. Opětovné sestavení zablokovaná v jednom ze dvou fází:

* **Čekání na všesměrové vysílání**: FM/FMM čeká na odpověď všesměrového vysílání zprávy z jiných uzlů.

  * **Další kroky**: Zjistěte, zda je problém se síťovým připojením mezi uzly.
* **Čekání na uzly**: FM/FMM již přijaty všesměrového vysílání odpověď z jiných uzlů a čeká na odpověď od konkrétním uzlům. Sestava stavu zobrazuje seznam uzlů, pro které je FM/FMM čekání na odpověď.
   * **Další kroky**: prozkoumat síťové připojení mezi FM/FMM a uvedené uzly. Zkoumání jednotlivých uvedených uzel pro další možné problémy.

* **SourceID**: System.FM nebo System.FMM
* **Vlastnost**: znovu sestavit.
* **Další kroky**: prozkoumat síťové připojení mezi uzly, jakož i stav žádné konkrétní uzlů, které jsou uvedeny na popis sestavy stavu.

## <a name="node-system-health-reports"></a>Uzel sestav o stavu systému
System.FM, který představuje službu Správce převzetí služeb při selhání, je, že úřad, který spravuje informace o uzly clusteru. Každý uzel musí mít jednu sestavu z System.FM zobrazuje stav. Uzel entity, které se odeberou, když se odebere stav uzlu. Další informace najdete v tématu [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Uzel nahoru/dolů
System.FM nahlásí jako OK, když se uzel připojí prstenec (je spuštěná). Nahlásí chybu, pokud uzel vyplouvající řetězci (je vypnutý, buď pro upgrade nebo jednoduše protože se nezdařilo). Stav hierarchie sestavena úložiště zdravotní má vliv na nasazenou entity v korelace s System.FM sestavy uzlu. Nadřazený virtuální všechny nasazené entit považuje uzlu. Nasazené entit na tomto uzlu se zveřejňují přes dotazy, pokud uzel je hlášen jako až podle System.FM s stejnou instanci jako instanci spojenou s entity. Když System.FM hlásí, že uzel je vypnutý nebo restartovaný jako novou instanci, úložišti health automaticky vyčistí nasazené entitami, které může existovat jenom v uzlu dolů nebo na předchozí instanci uzlu.

* **SourceId**: System.FM
* **Vlastnost**: stavu.
* **Další kroky**: Pokud je uzel dolů k upgradu by měl mít zpět poté, co byla upgradována. V takovém případě by měl stav přepněte zpět na OK. Pokud uzel nemá vraťte nebo se nezdaří, problém potřebuje další šetření.

Následující příklad ukazuje System.FM události se stavem stavu OK pro uzel:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Konec platnosti certifikátu
**System.FabricNode** sestavy upozornění, když se certifikáty používané uzlu blíží vypršení platnosti. Existují tři certifikáty na uzel: **Certificate_cluster**, **Certificate_server**, a **Certificate_default_client**. Při vypršení je alespoň dva týdny, je sestava stavu v pořádku. Pokud doba vypršení platnosti je během dvou týdnů, typ sestavy je upozornění. Hodnota TTL z těchto událostí je nekonečno, a budou odstraněny Jestliže uzel opustí clusteru.

* **SourceId**: System.FabricNode
* **Vlastnost**: začíná **certifikát** a obsahuje další informace o typ certifikátu.
* **Další kroky**: aktualizovat certifikáty, pokud se blíží vypršení platnosti.

### <a name="load-capacity-violation"></a>Načíst porušení kapacity
Vyrovnávání zatížení Service Fabric hlásí upozornění, když zjistí porušení kapacity uzlu.

* **SourceId**: System.PLB
* **Vlastnost**: začíná **kapacity**.
* **Další kroky**: Zkontrolujte zadané metriky a zobrazit aktuální kapacitu na uzlu.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Neshoda uzlu kapacity pro prostředek zásad správného řízení metriky
System.Hosting sestavy, upozornění, pokud uzel kapacity definovaná v manifestu clusteru jsou větší než skutečná uzlu kapacity pro prostředek zásad správného řízení metriky (paměť a počet jader procesoru). Sestava stavu se zobrazí, když první balíčku služby, který používá [zásad správného řízení prostředků](service-fabric-resource-governance.md) zaregistruje na určeného uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: Tento problém může být problém, protože řízení balíčky služeb nejsou vynucená podle očekávání a [zásad správného řízení prostředků](service-fabric-resource-governance.md) nefunguje správně. Aktualizace v manifestu clusteru pomocí kapacity správný uzel pro tyto metriky nebo nepoužíváte zadejte je a nechat Service Fabric automaticky zjišťovat prostředky k dispozici.

## <a name="application-system-health-reports"></a>Aplikace sestav o stavu systému
System.CM, který představuje službu Správce clusteru, je, že úřad, který spravuje informace o aplikaci.

### <a name="state"></a>Stav
System.CM nahlásí jako OK když aplikace byly vytvořeny nebo aktualizovány. Úložiště stavu informuje při odstranění aplikace tak, aby bylo možné odebrat z úložiště.

* **SourceId**: System.CM
* **Vlastnost**: stavu.
* **Další kroky**: Pokud aplikace má byly vytvořeny nebo aktualizovány, měl by obsahovat sestava stavu Správce clusteru. Jinak zkontrolujte stav aplikace vydáním dotazu. Například použijte rutinu prostředí PowerShell **Get ServiceFabricApplication - ApplicationName** *applicationName*.

Následující příklad ukazuje události stavu na **fabric: / WordCount** aplikace:

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Služba sestav o stavu systému
System.FM, který představuje službu Failover Manager, je, že úřad, který spravuje informace o službách.

### <a name="state"></a>Stav
System.FM sestavy jako OK po vytvoření služby. Odstraní entitu z health store při odstranění služby.

* **SourceId**: System.FM
* **Vlastnost**: stavu.

Následující příklad ukazuje události stavu služby **fabric: / WordCount/WordCountWebService**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Chyba korelace služby
**System.PLB** nahlásí chybu, když zjistí, zda aktualizace služby je korelační s jinou službu, která vytvoří řetězec vztahů. Sestava je vymazán poté, co se stane úspěšná aktualizace.

* **SourceId**: System.PLB
* **Vlastnost**: **ServiceDescription**.
* **Další kroky**: Zkontrolujte popis korelační služby.

## <a name="partition-system-health-reports"></a>Oddíl sestav o stavu systému
System.FM, který představuje službu Failover Manager, je autority, který spravuje informace o oddílech služby.

### <a name="state"></a>Stav
System.FM nahlásí jako OK když oddíl existuje a je v pořádku. Odstraní entitu z health store při odstranění oddílu.

Pokud oddílu je menší než počet minimální repliky, nahlásí chybu. Pokud oddíl není nižší než počet minimální repliky, ale je nižší než počtu cílových replik, sestavy upozornění. Pokud oddíl je ve ztrátě kvora, System.FM nahlásí chybu.

Jiné významné události zahrnují upozornění při změně konfigurace trvá déle, než se očekávalo, a při sestavení trvá déle, než se očekávalo. Očekávané časy pro sestavení a změny konfigurace se dají konfigurovat podle scénáře služby. Například pokud má služba terabajt stavu, například Azure SQL Database, sestavení trvá déle než pro službu s malou stavu.

* **SourceId**: System.FM
* **Vlastnost**: stavu.
* **Další kroky**: Pokud stav není v pořádku, je možné, že nebyly některé repliky vytvoření, otevření nebo povýšen na primární nebo sekundární správně. 

Pokud popis popisuje ztráty kvora, pak zkoumání sestava podrobné stavu pro repliky, které jsou vypnuté a jejich uvedení zálohování pomáhá, aby oddíl zpět do režimu online.

Pokud popis popisuje oddíl zasekla v automatickém [Rekonfigurace](service-fabric-concepts-reconfiguration.md), pak sestava stavu na primární replice poskytuje dodatečné informace.

Pro ostatní System.FM sestav stavu bude zprávy o repliky nebo oddílu nebo služby z dalších komponent systému. 

Následující příklady popisují některé z těchto sestav. 

Následující příklad ukazuje oddíl v pořádku:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Následující příklad ukazuje stav oddíl, který je pod počtu cílových replik. Dalším krokem je popis oddílu, který ukazuje, jak jsou nakonfigurované získání: **MinReplicaSetSize** je tři a **TargetReplicaSetSize** je 7. Potom získáte počet uzlů v clusteru, který v tomto případě je pět. Ano v takovém případě dvě repliky nelze umístit, protože cílový počet replik je vyšší než počet uzlů, které jsou k dispozici.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Následující příklad ukazuje stav oddílu, který se zasekla v Opakovaná konfigurace kvůli uživatele není ctít zásady zrušení tokenů v **RunAsync** metoda. Sestava stavu všech označen jako primární repliky (P) na odstranění příčin může pomoci přejdete dolů další do problém.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Tato sestava stavu zobrazuje stav repliky oddílu, který prochází změny konfigurace: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Pro každou repliku obsahuje sestavu o stavu replikace:
- Předchozí konfiguraci role
- Aktuální konfigurace role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- Uzel, na kterém běží repliky
- ID repliky

V případě jako v příkladu další šetření je nutná. Prozkoumat stav každé jednotlivé repliky počínaje repliky označené jako `Primary` a `Secondary` (131482789658160654 a 131482789688598467) v předchozím příkladu.

### <a name="replica-constraint-violation"></a>Porušení omezení repliky
**System.PLB** sestavy upozornění, pokud zjistí porušení omezení repliky a nelze umístit všechny repliky oddílu. Zobrazí podrobnosti sestavy, které omezení a vlastnosti zabránit umístění repliky.

* **SourceId**: System.PLB
* **Vlastnost**: začíná **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Repliky sestav o stavu systému
**System.RA**, která představuje součást reconfiguration agent, je autorita pro stav repliky.

### <a name="state"></a>Stav
System.RA hlásí OK po vytvoření repliky.

* **SourceId**: System.RA
* **Vlastnost**: stavu.

Následující příklad ukazuje repliku v pořádku:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Tato vlastnost slouží k označení chyby nebo upozornění při pokusu o otevření repliky, zavřete repliku nebo přechod na jiný repliku z jedné role. Další informace najdete v tématu [životního cyklu repliky](service-fabric-concepts-replica-lifecycle.md). Chyby může být výjimek vyvolaných z volání rozhraní API nebo dojde k chybě procesu hostitele služby během této doby. Selhání kvůli volání rozhraní API z kódu jazyka C# Service Fabric přidá výjimku a trasování zásobníku sestavy stavu.

Tato upozornění na stav jsou vyvolány po opakování akce místně některé počet opakování (v závislosti na zásadách). Service Fabric opakuje akci až do maximální prahovou hodnotu. Po dosažení maximální prahová hodnota mohou zkuste tak, aby fungoval k napravení situace. Tento pokus může způsobit, že tato upozornění k jejímu vymazání, protože umožňuje na akci na tomto uzlu. Například pokud se nedaří repliku otevřete na uzlu, Service Fabric vyvolá upozornění stavu. Pokud replika stále se neotevře, Service Fabric funguje pro automatickou opravu. Tato akce může zahrnovat pokusem o stejné v jiném uzlu. Tento pokus způsobí, že upozornění aktivována pro tuto repliku vymazat. 

* **SourceId**: System.RA
* **Vlastnost**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, a **ReplicaChangeRoleStatus**.
* **Další kroky**: prozkoumat služby kód nebo havárie výpisy Chcete-li určit, proč selhává operaci.

Následující příklad ukazuje stav repliky, která je aktivována `TargetInvocationException` z jeho open – metoda. Popis obsahuje bodem selhání, **IStatefulServiceReplica.Open**, typ výjimky **targetinvocationexception –**a trasování zásobníku.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Následující příklad ukazuje repliku, která selhává neustále při ukončení:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Rekonfigurace
Tato vlastnost slouží k označení při repliky provádění [Rekonfigurace](service-fabric-concepts-reconfiguration.md) zjistí, že je zastaven a proces nebo zablokuje novou konfiguraci. Tato sestava stavu může být v replice, jejíž aktuální role je primární, s výjimkou případů primární Rekonfigurace odkládacího souboru, kde může být na repliku, jehož úroveň chcete snížit z primární na aktivní sekundární.

Nové konfigurace může zablokované pro jednu z následujících důvodů:

- Akce na místní replika, replika stejné jako provádění změny konfigurace, není dokončení. V takovém případě příčin sestavy stavu v této repliky z jiné komponenty, System.RAP nebo System.RE, může poskytnout další informace.

- Akce není dokončení vzdálené repliky. Repliky, pro které jsou čekající akce jsou uvedeny v sestavě stavu. Další šetření by mělo být provedeno na sestavy stavu pro tyto vzdálené repliky. Také je možné problémy komunikace mezi tento uzel a vzdáleném uzlu.

Ve výjimečných případech můžete být novou konfiguraci zablokované kvůli komunikaci nebo jiné problémy mezi tento uzel a service Manager převzetí služeb při selhání.

* **SourceId**: System.RA
* **Vlastnost**: Změna konfigurace.
* **Další kroky**: prozkoumat místní nebo vzdálené repliky v závislosti na popis sestavy stavu.

Následující příklad ukazuje sestavy stavu, kde se zablokuje Rekonfigurace místní repliky. V této ukázce ji není z důvodu služby ctít zásady token zrušení.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Následující příklad ukazuje sestav stavu, kde změny konfigurace se zasekla čekání na odpověď od dvě vzdálené repliky. V tomto příkladu jsou tři repliky v oddílu, včetně aktuální primární. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Tato sestava stavu zobrazuje, jestli nové konfigurace zasekl čekání na odpověď od dvě repliky: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Pro každou repliku je uveden následující informace:
- Předchozí konfiguraci role
- Aktuální konfigurace role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- ID uzlu
- ID repliky

Chcete-li odblokovat nové konfigurace:
- **Dolů** by měl být zapínají repliky. 
- **Inbuild** repliky by měla dokončení sestavení a přechod do připravené.

### <a name="slow-service-api-call"></a>Pomalá volání rozhraní API služby
**System.RAP** a **System.Replicator** sestavy upozornění, pokud volání do kódu uživatele služby trvá déle, než je doba, nakonfigurované. Upozornění je vymazán po dokončení volání.

* **SourceId**: System.RAP nebo System.Replicator
* **Vlastnost**: název pomalé rozhraní API. Popis poskytuje další podrobnosti o době, kdy byl čeká na rozhraní API.
* **Další kroky**: Zjistěte, proč trvá déle, než bylo očekáváno volání.

Následující příklad ukazuje událost stavu z System.RAP pro spolehlivá služba, která není ctít zásady zrušení tokenů v **RunAsync**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Vlastnost a text označují, které rozhraní API získali zablokované. Další kroky pro zajištění pro různé zablokované rozhraní API se liší. Jakéhokoli rozhraní API na *IStatefulServiceReplica* nebo *IStatelessServiceInstance* je obvykle chybou v kódu služby. Následující část popisuje, jak tyto nepřeloží na [spolehlivé služby modelu](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Toto upozornění znamená, že volání `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, nebo pokud přepsána, `OnOpenAsync` je zablokované.

- **IStatefulServiceReplica.Close** a **IStatefulServiceReplica.Abort**: nejběžnější případ je služba není ctít zásady token zrušení předané do `RunAsync`. Může být také který `ICommunicationListener.CloseAsync`, nebo pokud přepsána, `OnCloseAsync` je zablokované.

- **IStatefulServiceReplica.ChangeRole (S)** a **IStatefulServiceReplica.ChangeRole(N)**: nejběžnější případ je služba není ctít zásady token zrušení předané do `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: nejběžnější případem je, že služby nevrátil úlohu z `RunAsync`.

Další volání rozhraní API, které může být zablokován jsou na **IReplicator** rozhraní. Příklad:

- **IReplicator.CatchupReplicaSet**: Toto upozornění označuje jednu ze dvou akcí. Existují nedostatečné repliky. Pokud chcete zobrazit, pokud se jedná o tento případ, podívejte se na stav repliky replik v oddílu nebo sestava stavu System.FM zablokované překonfigurovávat. Nebo repliky nejsou to v úvahu operace. Rutiny prostředí PowerShell `Get-ServiceFabricDeployedReplicaDetail` slouží k určení průběh všech replik. Potíže nezpůsobuje repliky, jehož `LastAppliedReplicationSequenceNumber` hodnota je za primární `CommittedSequenceNumber` hodnotu.

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: Toto upozornění indikuje problém v procesu sestavení. Další informace najdete v tématu [životního cyklu repliky](service-fabric-concepts-replica-lifecycle.md). Může to být z důvodu chybné konfigurace Replikátor adresy. Další informace najdete v tématu [konfigurovat stavová spolehlivé služby](service-fabric-reliable-services-configuration.md) a [zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md). Také může být problém ve vzdáleném uzlu.

### <a name="replicator-system-health-reports"></a>Replikátor sestav o stavu systému
**Fronty replikací úplné:**
**System.Replicator** nahlásí upozornění, když se fronta replikací je plná. Na primárním fronty replikací obvykle plný protože jeden nebo více sekundárních replikách jsou pomalé potvrdit operace. Na sekundárním to obvykle se stane, když služba pomalé použít operace. Upozornění je vymazán poté, co už fronta je plná.

* **SourceId**: System.Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na roli repliky.
* **Další kroky**: Pokud sestava je na primárním, zkontrolujte připojení mezi uzly v clusteru. Pokud všechna připojení jsou v pořádku, může být alespoň jeden pomalé sekundární s latencí vysoké disku pro použití operace. Pokud sestava je na sekundárním, proveďte nejprve kontrolu využití disku a výkonu na uzlu. Zkontrolujte odchozí připojení z uzlu pomalé na primární.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** na primární replice nahlásí upozornění, když připojení k sekundární (vzdálený) Replikátor není v pořádku. Adresa vzdáleného Replikátor se zobrazí v zpráva sestavy, což umožňuje zjistit, pokud byla předána chybná konfigurace nebo pokud jsou problémy s síti mezi replikátory pohodlnější.

* **SourceId**: System.Replicator
* **Vlastnost**: **RemoteReplicatorConnectionStatus**.
* **Další kroky**: Zkontrolujte chybové zprávy a zajistěte, aby adresu vzdáleného Replikátor je správně nakonfigurovaná. Například pokud vzdáleného Replikátor je otevřené s adresou naslouchání "localhost", není dostupný z vnějšku. Pokud adresa správná, zkontrolujte připojení mezi primárním uzlu a Vzdálená adresa najít všechny potenciální problémy sítě.

### <a name="replication-queue-full"></a>Replikační fronta je plná
**System.Replicator** nahlásí upozornění, když se fronta replikací je plná. Na primárním fronty replikací obvykle plný protože jeden nebo více sekundárních replikách jsou pomalé potvrdit operace. Na sekundárním to obvykle se stane, když služba pomalé použít operace. Upozornění je vymazán poté, co už fronta je plná.

* **SourceId**: System.Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na roli repliky.

### <a name="slow-naming-operations"></a>Pomalé operations pojmenování
**System.NamingService** hlásí stav její primární replika při operaci pojmenování trvá déle než přípustné. Příklady operací pojmenování [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) nebo [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Další metody naleznete v části FabricClient. Například můžete najít v části [služby metody správy](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) nebo [metody správy vlastnost](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Službu Naming překládá názvy služby do umístění v clusteru. Uživatelé můžou použít ho ke správě služby názvy a vlastnosti. Je to služba Service Fabric jako trvalý rozdělena na oddíly. Představuje jeden z oddílů *Authority Owner*, který obsahuje metadata o všechny názvy Service Fabric a služeb. Service Fabric názvy jsou namapované na různé oddíly, nazývá *Name Owner* oddíly, tak služba je rozšiřitelný. Další informace o [Naming service](service-fabric-architecture.md).
> 
> 

Při operaci pojmenování trvá déle, než se očekávalo, operace označené sestavu upozornění na primární replice oddílu pojmenování služby, který slouží operaci. Pokud po úspěšném dokončení operace je zrušeno upozornění. Dokončení operace s chybou, sestava stavu zahrnuje podrobnosti o této chybě.

* **SourceId**: System.NamingService
* **Vlastnost**: začíná předponu "**Duration_**" a identifikuje pomalé operaci a název Service Fabric, na kterém se používá operaci. Například pokud vytvořit službu na název **fabric: / MyApp/Moje_služba** trvá příliš dlouho, je vlastnost **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" odkazuje na roli pojmenování oddílu pro tento název a operaci.
* **Další kroky**: Zkontrolujte, proč pojmenování operace selže. Každé operace může mít různé kořenové příčiny. Například zablokovaná službu odstranit. Služba zablokovaná, protože udržuje na uzlu z důvodu chyby uživatele v kódu služby chybám hostitele aplikací.

Následující příklad ukazuje operaci vytvoření služby. Operace trvalo déle, než nakonfigurovaná doba trvání. "AO" opakování a odešle pracovní "Ne" "Žádný" dokončena poslední operace s vypršením časového LIMITU. V takovém případě je stejné repliky primární "AO" a "Žádné" role.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication sestav o stavu systému
**System.Hosting** autoritou na nasazené entity.

### <a name="activation"></a>Aktivace
System.Hosting nahlásí jako OK když aplikace úspěšně aktivuje na uzlu. V opačném případě nahlásí chybu.

* **SourceId**: System.Hosting
* **Vlastnost**: **aktivace**, včetně verze zavedení.
* **Další kroky**: Pokud aplikace není v pořádku, zjistěte, proč aktivace se nezdařila.

Následující příklad ukazuje úspěšné aktivaci:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ke stažení
System.Hosting nahlásí chybu, pokud stahování balíčku aplikace selže.

* **SourceId**: System.Hosting
* **Vlastnost**: **Stáhnout**, včetně verze zavedení.
* **Další kroky**: Zjistěte, proč se stahování v tomto uzlu selhal.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage sestav o stavu systému
**System.Hosting** autoritou na nasazené entity.

### <a name="service-package-activation"></a>Služba aktivace balíčku
System.Hosting jako OK sestavy, pokud je aktivace balíček služby v uzlu úspěšná. V opačném případě nahlásí chybu.

* **SourceId**: System.Hosting
* **Vlastnost**: aktivace.
* **Další kroky**: Zjistěte, proč aktivace se nezdařila.

### <a name="code-package-activation"></a>Aktivace balíčku kódu.
System.Hosting hlásí jako OK pro každý balíček kódu pokud je aktivace úspěšná. Pokud se aktivace nezdaří, sestavy upozornění podle konfigurace. Pokud **CodePackage** nepodaří aktivovat nebo ukončí s chybou větší než nakonfigurované **CodePackageHealthErrorThreshold**, nahlásí chybu, který je hostitelem. Pokud balíček služby obsahuje více balíčků kódu, zprávu o aktivaci se generuje pro každé z nich.

* **SourceId**: System.Hosting
* **Vlastnost**: používá předponu **CodePackageActivation** a obsahuje název balíček kódu a vstupního bodu jako *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*. Například **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Typ registrace služby
System.Hosting sestavy jako OK, pokud byl úspěšně zaregistrován typ služby. Ohlásí chybu pokud registrace nebyla provedena v čase, je nakonfigurován pomocí **ServiceTypeRegistrationTimeout**. Pokud je zavřená modul runtime, typ služby je odregistrovat z uzlu a hostování sestavy upozornění.

* **SourceId**: System.Hosting
* **Vlastnost**: používá předponu **ServiceTypeRegistration** a obsahuje název typu služby. Například **ServiceTypeRegistration:FileStoreServiceType**.

Následující příklad ukazuje v pořádku nasazený balíček služby:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ke stažení
System.Hosting nahlásí chybu, pokud služba stahování balíčku selže.

* **SourceId**: System.Hosting
* **Vlastnost**: **Stáhnout**, včetně verze zavedení.
* **Další kroky**: Zjistěte, proč se stahování v tomto uzlu selhal.

### <a name="upgrade-validation"></a>Ověření upgradu
System.Hosting nahlásí chybu, pokud selže ověření během upgradu nebo pokud upgrade selže na uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: používá předponu **FabricUpgradeValidation** a obsahuje upgradované verze.
* **Popis**: odkazuje na došlo k chybě.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nedefinovaná uzlu kapacity pro prostředek zásad správného řízení metriky
System.Hosting hlásí upozornění, pokud uzel kapacity nejsou definovány v manifestu clusteru a konfigurace pro automatické zjišťování je vypnutý. Service Fabric vyvolá stavu upozornění pokaždé, když balíček služby, který použije [zásad správného řízení prostředků](service-fabric-resource-governance.md) zaregistruje na určeného uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: upřednostňovaný způsob, jak tento problém vyřešit je změna v manifestu clusteru povolit automatické zjišťování dostupných zdrojů. Další možností je aktualizovat v manifestu clusteru správně zadaný uzel kapacity pro tyto metriky.

## <a name="next-steps"></a>Další postup
* [Zobrazit sestavy stavu Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Postup vytvoření sestavy a zkontrolujte stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorování a Diagnostika služby místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

