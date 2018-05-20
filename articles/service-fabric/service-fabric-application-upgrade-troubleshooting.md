---
title: Řešení potíží s aplikací upgrady | Microsoft Docs
description: Tento článek popisuje některé běžné problémy kolem upgrade aplikace Service Fabric a způsob jejich řešení.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: c6ba61354bf7466819e34a0d619a5a1820dd7b90
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-application-upgrades"></a>Řešení potíží s upgrady aplikací
Tento článek popisuje některé běžné problémy, týkající se upgradu aplikace Azure Service Fabric a způsob jejich řešení.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Řešení potíží při upgradu aplikaci, která selhala
Pokud upgrade selže, výstup **Get-ServiceFabricApplicationUpgrade** příkaz obsahuje další informace o ladění selhání.  V následujícím seznamu se určuje, jak je možné použít další informace:

1. Určete typ chyby.
2. Určete důvod selhání.
3. Izolujte jednu nebo více součástí selhání dále prozkoumat.

Tyto informace jsou k dispozici, když Service Fabric zjistí selhání bez ohledu na to, jestli **FailureAction** se používá k vrácení nebo pozastavit upgradu.

### <a name="identify-the-failure-type"></a>Identifikujte typ chyby
Ve výstupu **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifikuje časové razítko (ve formátu UTC), kdy bylo zjištěno selhání upgradu pomocí Service Fabric a  **FailureAction** byla aktivována. **Důvod chyby** označuje jeden tři základní příčin selhání:

1. UpgradeDomainTimeout – označuje, že konkrétní upgradu domény trvalo příliš dlouho dokončení a **UpgradeDomainTimeout** vypršela platnost.
2. OverallUpgradeTimeout – označuje, že celkový upgradu trvalo příliš dlouho dokončení a **UpgradeTimeout** vypršela platnost.
3. HealthCheck – označuje, že po dokončení upgradu domény služby aktualizace zůstal není v pořádku podle zásady zadaného stavu aplikace a **HealthCheckRetryTimeout** vypršela platnost.

Tyto položky pouze zobrazí ve výstupu se upgrade nezdaří a spustí vrácení zpět. Další informace se zobrazí v závislosti na typu selhání.

### <a name="investigate-upgrade-timeouts"></a>Prozkoumat upgradu časové limity
Upgrade vypršení časového limitu, selhání jsou nejčastěji způsobeny problémy s dostupností služby. Výstup níže je typický pro upgrady, kde služby replik nebo instancí nepodaří spustit v nové verzi kódu. **UpgradeDomainProgressAtFailure** pole zaznamená snímek žádné čekající upgradu práce v době selhání.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

V tomto příkladu upgrade se nezdařil v doméně pro upgrade *MYUD1* a dva oddíly (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* a *4b43f4d8-b26b-424e-9307-7a7a62e79750*) se zablokuje. Oddíly, které byly zablokované, protože modul runtime nemohl umístit primární repliky (*WaitForPrimaryPlacement*) na cílové uzly *Uzel1* a *Uzel4*.

**Get-ServiceFabricNode** příkaz lze použít k ověření, že jsou tyto dva uzly v doméně pro upgrade *MYUD1*. *UpgradePhase* uvádí *PostUpgradeSafetyCheck*, což znamená, že tyto kontroly zabezpečení dochází po dokončení upgradu všech uzlech v upgradovací doméně. Tyto informace se odkazuje na potenciální problém s novou verzí kódu aplikace. Nejběžnějším problémům, jsou chyby služby v otevřené nebo povýšení na primární kód cesty.

*UpgradePhase* z *PreUpgradeSafetyCheck* znamená byly nějaké problémy Příprava upgradu domény předtím, než byla provedena. Nejběžnějších problémů v tomto případě jsou chyby služby v zavřít nebo snížení úrovně z cesty primární kódu.

Aktuální **UpgradeState** je *RollingBackCompleted*, takže původní upgrade musí prováděly s vrácení zpět **FailureAction**, které automaticky nezobrazený Proveďte upgrade při selhání. Pokud původní upgradu byla provedena ručního **FailureAction**, pak upgradu by místo toho se v pozastaveném stavu, aby živých ladění aplikace.

Ve výjimečných případech **UpgradeDomainProgressAtFailure** pole může být prázdný, pokud celkový upgradu vyprší časový limit, stejně jako systém dokončení veškeré práce pro aktuální upgradovací doméně. Pokud k tomu dojde, pokuste se zvýšit **UpgradeTimeout** a **UpgradeDomainTimeout** upgrade hodnoty parametru a opakujte pokus o upgrade.

### <a name="investigate-health-check-failures"></a>Prozkoumat selhání kontroly stavu
Selhání kontroly stavu mohou být spouštěny různé problémy, které může dojít po všechny uzly v doméně upgradu dokončete upgrade a předání všechny kontroly zabezpečení. Výstup níže je typické pro upgrade nezdaří z důvodu kontroly stavu se nezdařilo. **UnhealthyEvaluations** pole zaznamená snímek kontroly stavu, které selhaly během upgradu podle zadaného [zásady stavu](service-fabric-health-introduction.md).

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Příčin selhání kontroly stavu nejprve vyžaduje znalosti o stavu modelu Service Fabric. Ale i bez takové detailní znalost, uvidíte, že jsou dvě služby není v pořádku: *fabric: / DemoApp/Svc3* a *fabric: / DemoApp/Svc2*, společně s sestav stavu chyby ("InjectedFault" v tomto případě). V tomto příkladu dva ze čtyř jsou služby není v pořádku, což je pod cílovou výchozí hodnotou 0 % není v pořádku (*MaxPercentUnhealthyServices*).

Upgrade zadáním pozastavením při selhání **FailureAction** ruční při spouštění upgradu. Tento režim umožňuje nám prozkoumat systému za provozu v chybovém stavu před provedením žádné další akce.

### <a name="recover-from-a-suspended-upgrade"></a>Obnovit pozastavenou upgradem
S vrácení zpět **FailureAction**, obnovit potřeby vzhledem k tomu, že upgrade automaticky vrátí zpět při selhání. S ruční **FailureAction**, máte několik možností obnovení:

1.  aktivační událost vrácení zpět.
2. Pokračovat ve zbývající části upgrade ručně
3. Obnovit monitorovaných upgradu

**Start-ServiceFabricApplicationRollback** příkaz lze použít kdykoli spustit vrácení zpět aplikace. Jakmile příkaz vrátí úspěšně, žádost o vrácení zpět je zaregistrován v systému a spustí krátce po tomto datu.

**Resume-ServiceFabricApplicationUpgrade** příkaz lze použít, chcete-li pokračovat ve zbývající části upgrade ručně, jednu upgradovací doménu najednou. V tomto režimu budou provedeny kontroly zabezpečení pouze v systému. Budou provedeny žádné další kontroly stavu. Tento příkaz lze použít, když *UpgradeState* ukazuje *RollingForwardPending*, což znamená, že doména upgradu současné dokončil upgrade ale dalšímu nebyla spuštěna (čeká na zpracování).

**Aktualizace ServiceFabricApplicationUpgrade** příkaz lze použít obnovit monitorovaných upgrade s obou zabezpečení a kontroluje stav provádí.

```
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
```

Z upgradovací doméně, kde byl poslední pozastavený a použijete stejný upgrade parametry a zásad stavu jako před bude pokračovat v upgradu. V případě potřeby jakýkoli z upgradu parametry a zásady stavu zobrazené ve výstupu předchozí lze změnit v jednom příkazu při upgradu obnoví. V tomto příkladu bylo obnoveno upgradu v režimu monitorované s parametry a zásad stavu beze změny.

## <a name="further-troubleshooting"></a>Další informace o řešení
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric není následující zásady zadaného stavu
Možné příčiny 1:

Service Fabric překládá všechny procenta do skutečný počet entit (například repliky, oddíly a služby) pro vyhodnocení stavu a vždy se zaokrouhlí na celý entity. Například pokud maximální *MaxPercentUnhealthyReplicasPerPartition* je 21 % a existují pět repliky, pak Service Fabric umožňuje až dvě repliky není v pořádku (tedy`Math.Ceiling (5*0.21)`). Zásady stavu proto musí být nastaven odpovídajícím způsobem.

Možná příčina 2:

Zásady stavu jsou specifikované jako procenta celkového služby a instance nejsou specifické služby. Například před provedením upgradu, pokud aplikace má čtyři služby instancí A, B, C a D, kde je služba D není v pořádku, ale s malý vliv k aplikaci. Chceme ignorovat známé služby není v pořádku D během upgradu a nastavte parametr *MaxPercentUnhealthyServices* být 25 %, za předpokladu, že pouze A a B a C musí být v pořádku.

Však během upgradu D může se nezotavila během C se změní na není v pořádku. Upgrade by být přesto úspěšné, protože jsou pouze 25 % služby není v pořádku. Však může vést k neočekávané chybám kvůli C se neočekávaně není v pořádku místo D. V takovém případě by měl být D modelovaná jako typu různé služby z A a B a c Vzhledem k tomu, že zásady stavu jsou nastaveny na typ služby, jiný není v pořádku procento prahové hodnoty je použít pro různé služby. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>I nezadali zásady stavu pro upgradu aplikace, ale pro některé vypršení časových limitů, nikdy zadaných stále selhání upgradu
Pokud zásady stavu nejsou zadaný pro žádost o upgrade, jsou převzaty z *ApplicationManifest.xml* aktuální verze aplikace. Například pokud upgradujete z verze 1.0 na verzi 2.0, zásady stavu aplikace uvedených v verze 1.0 pro aplikace X používají. Pokud chcete zásadu jiný stav upgradu, zásady musí být zadaný jako součást upgradu volání rozhraní API aplikace. Zásady, zadaný jako součást volání rozhraní API se uplatní jenom během upgradu. Po dokončení upgradu zásady zadaná v *ApplicationManifest.xml* se používají.

### <a name="incorrect-time-outs-are-specified"></a>Jsou zadány nesprávné vypršení časových limitů
Vám může mít zajímalo o co se stane, když jsou nekonzistentně nastavit vypršení časových limitů. Například můžete mít *UpgradeTimeout* to je méně než *UpgradeDomainTimeout*. Odpověď se vrátí chybu. Chyby se vrátí v případě *UpgradeDomainTimeout* je menší než součet *HealthCheckWaitDuration* a *HealthCheckRetryTimeout*, nebo pokud  *UpgradeDomainTimeout* je menší než součet *HealthCheckWaitDuration* a *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje upgrady trvá příliš dlouho
Doba pro upgrade k dokončení závisí na kontroly stavu a vypršení časových limitů zadán. Kontroly stavu a vypršení časových limitů závisí na tom, jak dlouho trvá kopírování, nasazení a stabilizaci aplikace. Probíhá příliš agresivní s vypršení časových limitů může znamenat více se selháním upgradu, takže doporučujeme můžete začít s delší časové limity.

Tady je rychlý aktualizačnímu programu na způsob, jakým vypršení časových limitů komunikovat s upgradem časy:

Upgrady pro upgradovací doméně nemůže dokončit rychlejší než *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Selhání při upgradu nemůže proběhnout, rychlejší než *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Doba upgradu upgradu domény je omezena *UpgradeDomainTimeout*.  Pokud *HealthCheckRetryTimeout* a *HealthCheckStableDuration* jsou oba nenulové a stavu aplikace udržuje přepínání a zpět, a nakonec vyprší upgrade na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* spustí počítání dolů jednou upgradu pro začne aktuální upgradovací doméně.

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade vaší aplikace pomocí prostředí Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.

Řídí, jak vaše aplikace upgraduje pomocí [Upgrade parametry](service-fabric-application-upgrade-parameters.md).

Zkontrolujte upgradů aplikace kompatibilní podle naučit se používat [serializace dat](service-fabric-application-upgrade-data-serialization.md).

Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).
