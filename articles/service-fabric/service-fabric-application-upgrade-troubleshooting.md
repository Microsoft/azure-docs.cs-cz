---
title: Řešení potíží s upgrady aplikací | Dokumentace Microsoftu
description: Tento článek popisuje některé běžné problémy kolem upgrade aplikace Service Fabric a jejich řešení.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e393eb92e11dc8dc296f1dc5f1c0036566c285c5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797855"
---
# <a name="troubleshoot-application-upgrades"></a>Řešení potíží s upgrady aplikací

Tento článek popisuje některé běžné problémy kolem upgrade aplikace Azure Service Fabric a jejich řešení.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Řešení potíží s upgrade aplikace, která selhala

Pokud upgrade selže, výstup **Get-ServiceFabricApplicationUpgrade** příkaz obsahuje další informace o ladění chyby.  Následující seznam určuje, jak je možné použít další informace:

1. Určete typ chyby.
2. Určete důvod selhání.
3. Izolování jedné nebo více součástí selhání pro další zkoumání.

Tyto informace jsou k dispozici, když Service Fabric zjistí selhání bez ohledu na to, zda **FailureAction** je vrátit zpět nebo pozastavit upgradu.

### <a name="identify-the-failure-type"></a>Identifikujte typ selhání

Ve výstupu příkazu **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifikuje časové razítko (ve standardu UTC), ve kterém bylo zjištěno selhání upgradu pomocí Service Fabric a  **FailureAction** byla aktivována. **FailureReason** identifikuje tři možné základní příčiny selhání:

1. UpgradeDomainTimeout – označuje, že konkrétní upgradovací doména trvalo příliš dlouho pro dokončení a **UpgradeDomainTimeout** vypršela platnost.
2. OverallUpgradeTimeout – označuje, že celkový upgrade trvalo příliš dlouho pro dokončení a **UpgradeTimeout** vypršela platnost.
3. HealthCheck – označuje, že po dokončení upgradu aktualizační doména aplikace zůstala podle zásady zadaného stavu není v pořádku a **HealthCheckRetryTimeout** vypršela platnost.

Tyto položky zobrazit pouze ve výstupu při upgradu se nezdaří a spustí vrácení zpět. Další informace se zobrazí v závislosti na typu selhání.

### <a name="investigate-upgrade-timeouts"></a>Prozkoumat vypršení časového limitu pro upgrade

Upgrade vypršení časového limitu, selhání jsou nejčastěji způsobeno problémy s dostupností služby. Za tímto odstavcem výstup je typický pro upgrady kde služby replik nebo instancí nemůže spustit v nové verzi kódu. **UpgradeDomainProgressAtFailure** pole zaznamená snímek všechny čekající upgrade práce v době selhání.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
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

V tomto příkladu byl upgrade neúspěšný v upgradovací doméně *MYUD1* a dva oddíly (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* a *4b43f4d8-b26b-424e-9307-7a7a62e79750*) se zablokuje. Oddíly se zablokovala, protože modul runtime nelze umístit primární repliky (*WaitForPrimaryPlacement*) na cílové uzly *Node1* a *Uzel4*.

**Get-ServiceFabricNode** příkaz lze použít k ověření, že jsou tyto dva uzly v upgradovací doméně *MYUD1*. *UpgradePhase* říká *PostUpgradeSafetyCheck*, což znamená, že tyto bezpečnostní kontroly dochází po dokončení upgradu všech uzlů v upgradovací domény. Tyto informace se odkazuje na potenciální problém s novou verzí kódu aplikace. Nejběžnějších problémů jsou chyby služby otevřít nebo povýšení na cestách primární kódu.

*UpgradePhase* z *PreUpgradeSafetyCheck* znamená, že došlo k problémům, Příprava upgradu domény předtím, než byla provedena. Nejběžnějších problémů v tomto případě jsou chyby služby v zavřít nebo snížení úrovně z cesty primární kódu.

Aktuální **UpgradeState** je *RollingBackCompleted*, takže původní upgrade musí prováděly s vrácení zpět **FailureAction**, který automaticky souhrn Proveďte upgrade nebude úspěšná. Pokud původní upgradu byla provedena s ruční **FailureAction**, pak upgradu by místo toho se v pozastaveném stavu, aby živé ladění aplikace.

Ve výjimečných případech **UpgradeDomainProgressAtFailure** pole může být prázdný, pokud celkový upgrade vyprší časový limit, stejně jako systém dokončení všech prací pro aktuální upgradovací doméně. Pokud k tomu dojde, zkuste zvětšit **UpgradeTimeout** a **UpgradeDomainTimeout** upgradovat hodnoty parametrů a zkuste upgrade zopakovat.

### <a name="investigate-health-check-failures"></a>Prověřit chyby kontroly stavu

Stav kontroly chyb může aktivovat různé problémy, které může dojít po dokončení všechny uzly v upgradovací doméně upgradu a předání všechny bezpečnostní kontroly. Za tímto odstavcem výstup je typický pro upgrade nezdaří z důvodu kontroly stavu se nezdařilo. **UnhealthyEvaluations** pole zaznamená snímek kontroly stavu, které selhaly během upgradu podle zadaného [zásady stavu](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
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

Zkoumání selhání kontroly stavu jsou nejprve nutné znalosti toho modelu stavu Service Fabric. Ale i bez těchto porozumět tomu, vidíme, že jsou dvě služby není v pořádku: *fabric: / DemoApp/Svc3* a *fabric: / DemoApp/Svc2*, společně se zprávami stav Chyba ("InjectedFault" v tomto případě). V tomto příkladu dvě z čtyři služby nejsou v pořádku, což je míň než výchozí cíl 0 % není v pořádku (*MaxPercentUnhealthyServices*).

Upgrade byl pozastaven po selhání tak, že zadáte **FailureAction** ručně prováděné při zahájení upgradu. Tento režim umožňuje nám prozkoumat systému za provozu v chybovém stavu před provedením jakékoli další akce.

### <a name="recover-from-a-suspended-upgrade"></a>Obnovit z pozastavené upgradu

S vrácení zpět **FailureAction**, neexistuje žádný obnovení potřeba, protože upgrade automaticky vrátí zpět při selhání. Ručního **FailureAction**, máte několik možností obnovení:

1.  aktivační událost vrácení zpět.
2. Projděte zbývající část upgrade ručně
3. Pokračovat v monitorovaných upgradu

**Start ServiceFabricApplicationRollback** příkaz je možné kdykoli spustit vrácení zpět aplikace. Až příkaz skončí úspěšně, požadavek vrácení zpět byl zaregistrován v systému a spustí po krátké době.

**Resume-ServiceFabricApplicationUpgrade** příkaz je možné ručně, pokračujte až do upgradu jednu upgradovací doménu najednou. V tomto režimu se provádí pouze bezpečnostní kontroly systému. Jsou prováděny žádné další kontroly stavu. Tento příkaz lze použít, pokud *UpgradeState* ukazuje *RollingForwardPending*, to znamená, že doména upgradu současné dokončil upgrade, ale další příkaz nebyl spuštěn (čeká na zpracování).

**Update-ServiceFabricApplicationUpgrade** příkaz je možné pokračovat v upgradu monitorovaných pomocí obou bezpečnosti a provádění kontroly stavu.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
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
```

Upgrade bude pokračovat v upgradovací doméně, ve kterém posledního pozastavení a použijte stejný upgrade parametry a zásad stavu jako před. V případě potřeby všechny parametry upgradu a zásady stavu, které jsou uvedené v předchozím výstupu lze změnit v jednom příkazu při pokračuje v upgradu. V tomto příkladu upgradu obnovena v režimu monitorované pomocí monitorování stavu zásadami beze změny a parametry.

## <a name="further-troubleshooting"></a>Další informace o řešení

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric není podle zadaného stavu zásad

Možné příčiny 1:

Service Fabric převádí všechny procenta na aktuální počet entity (například repliky, oddíly a služby) pro vyhodnocování stavu a vždy zaokrouhluje nahoru na celé entity. Například pokud maximální *MaxPercentUnhealthyReplicasPerPartition* je 21 % a jsou pěti replikami, a Service Fabric umožňuje až dvě repliky není v pořádku (to znamená`Math.Ceiling (5*0.21)`). Zásady stavu proto musí být nastaven odpovídajícím způsobem.

Možné příčiny 2:

Zásady stavu jsou specifikované jako procenta celkového služby a instance není konkrétní služby. Například před provedením upgradu, pokud aplikace obsahuje čtyři služby instance A, B, C a D, kde je služba D není v pořádku, ale s malým dopadem na aplikaci. Chceme ignorovat známé služby není v pořádku, D během upgradu a nastavte parametr *MaxPercentUnhealthyServices* 25 %, za předpokladu, že pouze A, B a C musí být v pořádku.

Nicméně během upgradu, D může se obnoví dobrý stav během C nebude v pořádku. Upgrade bude přesto úspěšné, protože jsou pouze 25 % služby není v pořádku. Však může způsobit neočekávané chyby z důvodu C se neočekávaně není v pořádku, ne D. V takovém případě by měl D nemodelují jako určitého typu různé služby A, B a C. Protože zásady stavu zadávají se na typ služby, prahové hodnoty různých procentních poměrech není v pořádku, lze použít pro různé služby. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Můžu neurčil zásady stavu pro upgrade aplikace, ale stále selhání upgradu pro některé nikdy zadané vypršení časových limitů

Když zásady stavu nejsou zadané pro požadavek na upgrade, jsou převzaty z *ApplicationManifest.xml* aktuální verze aplikace. Například se používají aplikace X při upgradu z verze 1.0 na verzi 2.0, zásady stavu aplikace podle verze 1.0. Pokud jiný stav zásad se použije pro upgrade, pak zásady musí být zadaný jako součást upgradu volání rozhraní API aplikace. Zásady, zadaný jako součást volání rozhraní API se vztahují jenom během upgradu. Po dokončení upgradu zásady podle *ApplicationManifest.xml* se používají.

### <a name="incorrect-time-outs-are-specified"></a>Jsou zadané nesprávné časové limity

Může vás zajímá o co se stane, když jsou nekonzistentně nastavené vypršení časových limitů. Například můžete mít *UpgradeTimeout* to menší než *UpgradeDomainTimeout*. Odpověď se vrátí chybu. Chyby jsou vráceny, pokud *UpgradeDomainTimeout* je menší než součtem *HealthCheckWaitDuration* a *HealthCheckRetryTimeout*, nebo pokud  *UpgradeDomainTimeout* je menší než součtem *HealthCheckWaitDuration* a *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje upgrady trvá moc dlouho

Doba pro upgrade k dokončení závisí na kontroly stavu a zadat časové limity. Kontroly stavu a časových limitů závisí na jak dlouho trvá kopírování, nasazení a stabilizaci aplikace. Probíhá příliš agresivní s časové limity může znamenat více neúspěšných upgrady, proto doporučujeme konzervativní počínaje delší časové limity.

Tady je rychlý aktualizační interakci časových limitů s upgradem časy:

Inovace pro jednu upgradovací doménu nelze dokončit rychlejší než *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Nelze provést upgrade nezdaří, je rychlejší než *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Doba upgradu pro jednu upgradovací doménu je omezená rozhraním *UpgradeDomainTimeout*.  Pokud *HealthCheckRetryTimeout* a *HealthCheckStableDuration* jsou nenulové a stav aplikace udržuje přepínání vpřed a zpět, pak upgrade časem i vypršení časového na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* spustí odpočítávání jednou inovace pro začíná aktuální upgradovací doméně.

## <a name="next-steps"></a>Další postup

[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

Řídí, jak vaše aplikace upgradovala pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).