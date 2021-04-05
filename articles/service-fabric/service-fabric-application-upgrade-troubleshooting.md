---
title: Řešení potíží s upgrady aplikací
description: Tento článek se zabývá některými běžnými problémy při upgradu aplikace Service Fabric a jejich řešení.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009343"
---
# <a name="troubleshoot-application-upgrades"></a>Řešení potíží s upgrady aplikací

Tento článek se zabývá některými běžnými problémy při upgradu aplikace Service Fabric Azure a jejich řešení.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Řešení problémů s upgradem neúspěšných aplikací

V případě selhání upgradu obsahuje výstup příkazu **Get-ServiceFabricApplicationUpgrade** Další informace pro ladění selhání.  Následující seznam uvádí, jak lze použít další informace:

1. Identifikujte typ selhání.
2. Identifikujte důvod selhání.
3. Izolujte jednu nebo více neúspěšných součástí pro další šetření.

Tyto informace jsou k dispozici, pokud Service Fabric zjistí selhání bez ohledu na to, zda má **FailureAction** vrátit nebo pozastavit upgrade.

### <a name="identify-the-failure-type"></a>Identifikace typu selhání

Ve výstupu **Get-ServiceFabricApplicationUpgrade** **FailureTimestampUtc** identifikuje časové razítko (v UTC), u kterého se zjistilo selhání upgradu Service Fabric a **FailureAction** se aktivovalo. **FailureReason** identifikuje jednu ze tří potenciálních příčin selhání:

1. UpgradeDomainTimeout – označuje, že konkrétní upgradovací doména trvala příliš dlouho a **UpgradeDomainTimeout** vypršela její platnost.
2. OverallUpgradeTimeout – označuje, že dokončení celého upgradu trvalo příliš dlouho a **UpgradeTimeout** vypršela jeho platnost.
3. HealthCheck – označuje, že po upgradu aktualizační domény zůstala aplikace v nesprávném stavu podle zadaných zásad stavu a **HealthCheckRetryTimeout** vypršela její platnost.

Tyto položky se zobrazí ve výstupu pouze v případě, že se upgrade nepovede a spustí se vracení zpět. V závislosti na typu selhání se zobrazí další informace.

### <a name="investigate-upgrade-timeouts"></a>Prozkoumat časové limity upgradu

Chyby s vypršením časového limitu upgradu se nejčastěji způsobují v případě problémů s dostupností služeb. Výstupem, který následuje za tímto odstavcem, je typická inovace, kde se v nové verzi kódu nedaří spustit repliky služby nebo instance. Pole **UpgradeDomainProgressAtFailure** zachycuje snímek všech probíhajících nedokončených upgradů v době selhání.

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

V tomto příkladu se upgrade nezdařil v upgradovací doméně *MYUD1* a byly zablokované dva oddíly (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* a *4b43f4d8-b26b-424e-9307-7a7a62e79750*). Oddíly se zablokovaly, protože modul runtime nemohl umístit primární repliky (*WaitForPrimaryPlacement*) do cílových uzlů *Uzel1* a *Uzel4*.

Pomocí příkazu **Get-ServiceFabricNode** můžete ověřit, jestli jsou tyto dva uzly v upgradu domény *MYUD1*. *UpgradePhase* říká *PostUpgradeSafetyCheck*, což znamená, že k těmto bezpečnostním kontrolám dochází po dokončení upgradu všech uzlů v upgradovací doméně. Všechny tyto informace odkazují na potenciální problém s novou verzí kódu aplikace. Nejběžnějšími problémy jsou chyby služeb v otevřených nebo propagačních cestách k primárnímu kódu.

*UpgradePhase* *PreUpgradeSafetyCheck* znamená, že při přípravě upgradovací domény byly problémy, než se provedla. Nejběžnější problémy v tomto případě jsou chyby služby při zavření nebo degradování z primárních cest kódu.

Aktuální **UpgradeState** je *RollingBackCompleted*, takže původní upgrade musí být proveden s návratovou **FailureActionou**, která automaticky odvolala upgrade po selhání. Pokud byl původní upgrade proveden s ručním **FailureAction**, upgrade by byl spíše v pozastaveném stavu, aby bylo možné živé ladění aplikace.

Ve výjimečných případech může být pole **UpgradeDomainProgressAtFailure** prázdné, pokud je celkový časový limit upgradu shodný s tím, jak systém dokončuje veškerou práci pro aktuální upgradovací doménu. Pokud k tomu dojde, zkuste zvýšit hodnoty parametru upgradu **UpgradeTimeout** a **UpgradeDomainTimeout** a zkuste upgrade zopakovat.

### <a name="investigate-health-check-failures"></a>Prozkoumat selhání kontroly stavu

Selhání kontroly stavu se můžou aktivovat různými problémy, ke kterým může dojít po dokončení upgradu všech uzlů v doméně upgradu a předání všech bezpečnostních kontrol. Výstupem, který následuje za tímto odstavcem, je typická Chyba při upgradu z důvodu neúspěšných kontrol stavu. Pole **UnhealthyEvaluations** zachycuje snímek kontrol stavu, které selhaly v době upgradu v závislosti na zadaných [zásadách stavu](service-fabric-health-introduction.md).

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

Vyšetření selhání kontroly stavu nejdřív vyžaduje porozumění modelu stavu Service Fabric. Ale i bez tohoto podrobného porozumění vidíte, že dvě služby nejsou v pořádku: *topologie Fabric:/DemoApp/Svc3* a *Fabric:/DemoApp/Svc2*, spolu se zprávami o stavu chyby (v tomto případě "InjectedFault"). V tomto příkladu jsou dvě ze čtyř služeb ve špatném stavu, což je pod výchozí cílovou hodnotou 0% není v pořádku (*MaxPercentUnhealthyServices*).

Upgrade byl pozastaven při selhání zadáním **FailureAction** ručně při zahájení upgradu. Tento režim nám umožní prozkoumat živý systém v neúspěšném stavu před provedením jakékoli další akce.

### <a name="recover-from-a-suspended-upgrade"></a>Obnovit z pozastaveného upgradu

S vrácením zpět **FailureAction** není potřeba žádné obnovení, protože se upgrade automaticky vrátí po selhání. Ruční **FailureAction** má několik možností obnovení:

1.  Aktivace vrácení zpět
2. Projděte si zbývající část upgradu ručně.
3. Pokračovat v monitorovaném upgradu

Příkaz **Start-ServiceFabricApplicationRollback** lze kdykoli použít k zahájení vracení aplikace zpět. Po úspěšném dokončení příkazu se v systému zaregistruje žádost o vrácení zpět a spustí se krátce po ní.

Příkaz **Resume-ServiceFabricApplicationUpgrade** se dá použít k ručnímu provedení zbývajícího upgradu na jednu upgradovací doménu. V tomto režimu se systémem provádí pouze kontroly zabezpečení. Neprovádí se žádné další kontroly stavu. Tento příkaz lze použít pouze v případě, že *UpgradeState* zobrazuje *RollingForwardPending*, což znamená, že aktuální upgradovací doména dokončila upgrade, ale ta ještě nebyla spuštěna (čeká na vyřízení).

Příkaz **Update-ServiceFabricApplicationUpgrade** se dá použít k obnovení monitorovaného upgradu s prováděnými kontrolami bezpečnosti i stavu.

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

Upgrade bude pokračovat z upgradovací domény, ve které byl naposledy pozastaven, a použít stejné parametry upgradu a zásady stavu jako dříve. V případě potřeby lze kterýkoli z parametrů upgradu a zásad stavu zobrazených v předchozím výstupu změnit v rámci stejného příkazu, když se upgrade obnoví. V tomto příkladu byl upgrade obnovený v monitorovaném režimu, přičemž parametry a zásady stavu se nezměnily.

## <a name="further-troubleshooting"></a>Další řešení potíží

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric nenásleduje pro zadané zásady stavu.

Možná příčina 1:

Service Fabric překládá všechny procentní podíly na skutečný počet entit (například repliky, oddíly a služby) pro hodnocení stavu a vždy zaokrouhluje na celé entity. Pokud je například maximální *MaxPercentUnhealthyReplicasPerPartition* 21% a existuje pět replik, pak Service Fabric umožňuje až dvě repliky, které nejsou v pořádku (tj `Math.Ceiling (5*0.21)` .). Proto by se měly zásady stavu nastavit odpovídajícím způsobem.

Možná příčina 2:

Zásady stavu se zadává v procentech z celkových služeb a nikoli z konkrétních instancí služby. Třeba před upgradem, pokud má aplikace čtyři instance služby A, B, C a D, kde služba D není v pořádku, ale s malým dopadem na aplikaci. Chceme během upgradu ignorovat známou službu, která není v pořádku, a nastavte parametr *MaxPercentUnhealthyServices* na 25%. za předpokladu, že musí být v pořádku jenom a, B a C.

Během upgradu se ale v případě, že C přestane nastavovat, může stát v pořádku. Upgrade se pořád úspěšně zdařil, protože jenom 25% služeb není v pořádku. Může to ale vést k neočekávaným chybám z důvodu neočekávaného nesprávného stavu jazyka C místo D. V takovém případě by měl být D modelován jako jiný typ služby od A, B a C. Vzhledem k tomu, že jsou zásady stavu určené pro typ služby, můžete pro různé služby použít různé prahové hodnoty v procentech, které nejsou v pořádku. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nezadal (a) jsem zásady stavu pro upgrade aplikace, ale upgrade se u některých časových limitů, které jsem nezadal, pořád neprojde.

Pokud nejsou požadavky na upgrade poskytovány zásady stavu, jsou pořízeny z *ApplicationManifest.xml* aktuální verze aplikace. Pokud například provádíte upgrade aplikace X z verze 1,0 na verzi 2,0, jsou použity zásady stavu aplikace zadané pro verzi 1,0. Pokud se má pro upgrade použít jiné zásady stavu, pak je potřeba tuto zásadu zadat v rámci volání rozhraní API pro upgrade aplikace. Zásady zadané jako součást volání rozhraní API platí pouze během upgradu. Po dokončení upgradu se použijí zásady zadané v *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Jsou zadané nesprávné časové limity.

Možná jste se přemýšlelii, co se stane, když časové limity nastavíte nekonzistentně. Například můžete mít *UpgradeTimeout* , který je menší než *UpgradeDomainTimeout*. Odpověď je vrácena chyba. Pokud je *UpgradeDomainTimeout* menší než součet *HealthCheckWaitDuration* a *HealthCheckRetryTimeout*, nebo pokud *UpgradeDomainTimeout* je menší než součet *HealthCheckWaitDuration* a *HealthCheckStableDuration*, vrátí se chyby.

### <a name="my-upgrades-are-taking-too-long"></a>Mé upgrady trvají moc dlouho

Čas dokončení upgradu závisí na kontrolách stavu a zadaných časových limitech. Kontroly stavu a časové limity závisí na tom, jak dlouho trvá kopírování, nasazování a stabilizaci aplikace. Příliš agresivní s časovým limitem může znamenat více neúspěšných upgradů, takže doporučujeme začít s delší časovou prodlevou.

Tady je rychlý aktualizační program, jak časové limity pracují s časy upgradu:

Upgrady pro upgradovací doménu nelze dokončit rychleji než *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

Selhání upgradu nemůže být rychlejší než *HealthCheckWaitDuration*  +  *HealthCheckRetryTimeout*.

Doba upgradu pro upgradovací doménu je omezená nástrojem *UpgradeDomainTimeout*.  Pokud jsou *HealthCheckRetryTimeout* a *HealthCheckStableDuration* v nenulovém stavu a stav aplikace se přepíná, pak upgrade nakonec vyprší v *UpgradeDomainTimeout*. *UpgradeDomainTimeout* začne počítat po zahájení upgradu pro aktuální upgradovací doménu.

## <a name="next-steps"></a>Další kroky

[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).