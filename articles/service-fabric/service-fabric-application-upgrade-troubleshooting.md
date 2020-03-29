---
title: Poradce při potížích s upgrady aplikací
description: Tento článek popisuje některé běžné problémy týkající se upgradu aplikace Service Fabric a jak je vyřešit.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377918"
---
# <a name="troubleshoot-application-upgrades"></a>Řešení potíží s upgrady aplikací

Tento článek popisuje některé běžné problémy týkající se upgradu aplikace Azure Service Fabric a jak je vyřešit.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Poradce při potížích s neúspěšným upgradem aplikace

Pokud se upgrade nezdaří, výstup příkazu **Get-ServiceFabricApplicationUpgrade** obsahuje další informace pro ladění selhání.  Následující seznam určuje, jak lze použít další informace:

1. Identifikujte typ selhání.
2. Identifikujte důvod selhání.
3. Izolujte jednu nebo více selhávajících složek pro další šetření.

Tyto informace jsou k dispozici, když Service Fabric zjistí selhání bez ohledu na to, zda **failureaction** je vrátit zpět nebo pozastavit upgrade.

### <a name="identify-the-failure-type"></a>Identifikace typu selhání

Ve výstupu **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifikuje časové razítko (v UTC), na kterém byla zjištěna chyba upgradu service fabric a **FailureAction** byla spuštěna. **FailureReason** identifikuje jednu ze tří potenciálních příčin selhání na vysoké úrovni:

1. UpgradeDomainTimeout - Označuje, že konkrétní upgrade domény trvalo příliš dlouho na dokončení a **UpgradeDomainTimeout** vypršela.
2. OverallUpgradeTimeout - Označuje, že dokončení celkové inovace trvalo příliš dlouho a **upgradeTimeout** vypršela.
3. HealthCheck - Označuje, že po upgradu aktualizační domény aplikace zůstala v pořádku podle zadaných zásad stavu a **HealthCheckRetryTimeout** vypršela.

Tyto položky se zobrazí ve výstupu pouze v případě, že upgrade selže a spustí vrácení zpět. Další informace se zobrazí v závislosti na typu poruchy.

### <a name="investigate-upgrade-timeouts"></a>Prozkoumat časové opozí upgradu

Selhání časového období upgradu jsou nejčastěji způsobena problémy s dostupností služby. Výstup následující po tomto odstavci je typický pro inovace, kde repliky služby nebo instance se nepodaří spustit v nové verzi kódu. Pole **UpgradeDomainProgressAtFailure** zachycuje snímek všech čekajících prací na upgradu v době selhání.

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

V tomto příkladu se upgrade nezdařil v upgradu domény *MYUD1* a dva oddíly (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* a *4b43f4d8-b26b-424e-9307-7a7a62e79750*) byly přilepené. Oddíly byly zablokované, protože za běhu nebylo možné umístit primární repliky (*WaitForPrimaryPlacement*) na cílové uzly *Node1* a *Node4*.

**Příkaz Get-ServiceFabricNode** lze použít k ověření, že tyto dva uzly jsou v upgradu domény *MYUD1*. *UpgradePhase* říká *PostUpgradeSafetyCheck*, což znamená, že tyto bezpečnostní kontroly dochází po všechny uzly v doméně upgradu dokončili upgrade. Všechny tyto informace ukazují na potenciální problém s novou verzí kódu aplikace. Nejběžnější problémy jsou chyby služby v otevřené nebo povýšení na cesty primárního kódu.

*UpgradePhase* *preupgradeSafetyCheck* znamená, že byly problémy při přípravě domény upgradu před tím, než byla provedena. Nejběžnější problémy v tomto případě jsou chyby služby v uzavření nebo snížení úrovně z cesty primárníkód.

Aktuální **UpgradeState** je *RollingBackCompleted*, takže původní upgrade musí být provedena s vrácení **mne akce ,** která automaticky vrátit zpět upgrade při selhání. Pokud původní upgrade byl proveden s ruční **Akce selhání**, pak upgrade by místo toho být v pozastaveném stavu povolit živé ladění aplikace.

Ve výjimečných případech **upgradeDomainProgressAtFailure** pole může být prázdný, pokud celkový časový čas upgradu časový mzda stejně jako systém dokončí všechny práce pro aktuální doménu upgradu. If this happens, try increasing the **UpgradeTimeout** and **UpgradeDomainTimeout** upgrade parameter values and retry the upgrade.

### <a name="investigate-health-check-failures"></a>Prozkoumat selhání kontroly stavu

Selhání kontroly stavu může být vyvolána různými problémy, které se mohou stát po dokončení upgradu všech uzlů v upgradu domény a předání všech bezpečnostních kontrol. Výstup následující po tomto odstavci je typický pro selhání upgradu z důvodu neúspěšných kontrol stavu. Pole **NezdravéHodnocení** zachycuje snímek kontrolstavu, které se nezdařilo v době upgradu podle zadané [zásady stavu](service-fabric-health-introduction.md).

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

Zkoumání selhání kontroly stavu nejprve vyžaduje pochopení modelu stavu Service Fabric. Ale i bez takového hlubokého porozumění, můžeme vidět, že dvě služby jsou nezdravé: *fabric:/DemoApp/Svc3* a *fabric:/DemoApp/Svc2*, spolu se zprávami o stavu chyb ("InjectedFault" v tomto případě). V tomto příkladu dvě ze čtyř služeb nejsou v pořádku, což je pod výchozí cíl 0 % není v pořádku (*MaxPercentUnhealthyServices*).

Upgrade byl pozastaven po selhání zadáním **FailureAction** ruční při spuštění upgradu. Tento režim nám umožňuje prozkoumat živý systém ve stavu selhání před přijetím jakékoli další akce.

### <a name="recover-from-a-suspended-upgrade"></a>Obnovení z pozastaveného upgradu

S vrácení **mne není**potřeba žádné obnovení, protože upgrade se automaticky vrátí zpět při selhání. S ruční **failureaction**, existuje několik možností obnovení:

1.  aktivace vrácení zpět
2. Postup ujte ručně po zbývající část upgradu.
3. Obnovení sledovaného upgradu

**Příkaz Start-ServiceFabricFabricRollback** lze kdykoli použít ke spuštění vrácení aplikace zpět. Jakmile se příkaz úspěšně vrátí, požadavek na vrácení zpět byl zaregistrován v systému a spustí se krátce poté.

**Příkaz Resume-ServiceFabricApplicationUpgrade** lze použít k ručnímu provedení zbývající části upgradu po jedné doméně upgradu. V tomto režimu provádí systém pouze bezpečnostní kontroly. Žádné další kontroly stavu jsou prováděny. Tento příkaz lze použít pouze v *případě, že upgradeState* zobrazí *RollingForwardPending*, což znamená, že aktuální upgrade domény byla dokončena inovace, ale další nebylspuštěn (čekající).

**Příkaz Update-ServiceFabricApplicationUpgrade** lze obnovit monitorovaný upgrade s prováděním bezpečnostních i zdravotních kontrol.

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

Upgrade pokračuje z domény upgradu, kde byla naposledy pozastavena a používat stejné parametry upgradu a zásady stavu jako dříve. V případě potřeby lze při obnovení inovace změnit všechny parametry upgradu a zásady stavu uvedené v předchozím výstupu ve stejném příkazu. V tomto příkladu byla inovace obnovena v režimu Monitored, s parametry a zásady stavu beze změny.

## <a name="further-troubleshooting"></a>Další řešení potíží

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric nedodržuje zadané zásady stavu.

Možná příčina 1:

Service Fabric převede všechna procenta do skutečného počtu entit (například repliky, oddíly a služby) pro vyhodnocení stavu a vždy zaokrouhluje nahoru na celé entity. Například pokud maximální *MaxPercentUnhealthyReplicasPerPartition* je 21 % a existuje pět replik, pak Service Fabric umožňuje`Math.Ceiling (5*0.21)`až dvě repliky není v pořádku (to znamená). Proto by měly být odpovídajícím způsobem stanoveny zdravotní politiky.

Možná příčina 2:

Zásady stavu jsou určeny z hlediska procent celkových služeb a nikoli konkrétních instancí služeb. Například před upgradem, pokud aplikace má čtyři instance služby A, B, C a D, kde služba D není v pořádku, ale s malým dopadem na aplikaci. Chceme ignorovat známé služby není v pořádku D během upgradu a nastavit parametr *MaxPercentUnhealthyServices* být 25 %, za předpokladu, že pouze A, B a C musí být v pořádku.

Však během upgradu D může stát v pořádku, zatímco C se stane není v pořádku. Upgrade by stále úspěšné, protože pouze 25 % služeb nejsou v pořádku. Však může mít za následek neočekávané chyby z důvodu C je neočekávaně není v pořádku namísto D. V takovém případě D by měl být modelován jako jiný typ služby z A, B a C. Vzhledem k tomu, že zásady stavu jsou určeny pro každý typ služby, různé nefunkční procentní prahové hodnoty lze použít pro různé služby. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nezadal jsem zásady stavu pro upgrade aplikace, ale upgrade stále selže pro některé časové výtky, které jsem nikdy nezadal

Pokud nejsou k dispozici zásady stavu pro požadavek na upgrade, jsou převzaty z *ApplicationManifest.xml* aktuální verze aplikace. Pokud například upgradujete aplikaci X z verze 1.0 na verzi 2.0, použijí se zásady stavu aplikace určené pro verzi 1.0. Pokud by měl y pro upgrade použít jinou zásadu stavu, musí být tato zásada zadána jako součást volání rozhraní API pro upgrade aplikace. Zásady zadané jako součást volání rozhraní API platí pouze během upgradu. Po dokončení upgradu se používají zásady zadané v *souboru ApplicationManifest.xml.*

### <a name="incorrect-time-outs-are-specified"></a>Jsou zadány nesprávné časové odnože.

Možná jste přemýšleli o tom, co se stane, když jsou časové očasy nastaveny nekonzistentně. Například můžete mít *UpgradeTimeout,* který je menší než *UpgradeDomainTimeout*. Odpověď je, že je vrácena chyba. Chyby jsou vráceny, pokud *UpgradeDomainTimeout* je menší než součet *HealthCheckWaitDuration* a *HealthCheckRetryTimeout*, nebo pokud *UpgradeDomainTimeout* je menší než součet *HealthCheckWaitDuration* a *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje upgrady trvá příliš dlouho

Čas dokončení upgradu závisí na zadaných kontrolách stavu a časových časech. Kontroly stavu a časové nedonosné časové rozsahy závisí na tom, jak dlouho trvá kopírování, nasazování a stabilizace aplikace. Příliš agresivní s časovými výpadky může znamenat více neúspěšných upgradů, proto doporučujeme začít konzervativně s delšími časovými výčasovými časy.

Zde je stručný návod na to, jak časové intervaly interagují s dobou upgradu:

Inovace pro upgradovací doménu nelze dokončit rychleji než *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

K selhání upgradu nemůže dojít rychleji než *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Doba upgradu pro doménu upgradu je omezena *upgradeDomainTimeout*.  Pokud *HealthCheckRetryTimeout* a *HealthCheckStableDuration* jsou nenulové a stav aplikace udržuje přepínání tam a zpět, pak upgrade nakonec časový mzda na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* začne odpočítávat po zahájení upgradu pro aktuální doménu upgradu.

## <a name="next-steps"></a>Další kroky

[Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí funkce [Parametry upgradu](service-fabric-application-upgrade-parameters.md)můžete řídit způsob upgradu aplikace .

Pomocí aplikace můžete upgrady aplikací provést tak, že se naučíte používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [upřesnit témata](service-fabric-application-upgrade-advanced.md).