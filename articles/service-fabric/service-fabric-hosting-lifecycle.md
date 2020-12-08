---
title: Azure Service Fabric hostování aktivace a deaktivace životního cyklu
description: Vysvětluje životní cyklus aplikace a ServicePack na uzlu.
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779596"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Životní cyklus hostování Azure Service Fabric

Tento článek poskytuje přehled událostí, ke kterým dochází v Azure Service Fabric, když se aplikace aktivuje na uzlu a v různých konfiguracích clusteru používaných k řízení chování.

Než budete pokračovat, ujistěte se, že rozumíte různým konceptům a vztahům, které jsou vysvětleny v tématu [modelování aplikace v Service Fabric][a1]. 

> [!NOTE]
> V tomto článku, pokud výslovně nezmiňujete o něco jiného:
>
> - *Replika* odkazuje jak na repliku stavové služby, tak na instanci bezstavové služby.
> - *CodePackage* se považuje za ekvivalent procesu *ServiceHost* , který registruje *ServiceType* a hostuje repliky služeb tohoto *ServiceType*.
>

## <a name="activation-of-applicationservicepackage"></a>Aktivace aplikace/ServicePack

Kanál pro aktivaci je následující:

1. Stáhněte si ApplicationPackage. Například: ApplicationManifest.xml atd.
2. Nastavení prostředí pro aplikaci pro: vytvoření uživatelů atd.
3. Spustit sledování aplikace pro deaktivaci.
4. Stáhněte si ServicePack. Například: ServiceManifest.xml, Code, config, datové balíčky atd.
5. Nastavení prostředí pro balíček služby pro: nastavení brány firewall, přidělování portů pro koncové body atd.
6. Spustit sledování ServicePack pro deaktivaci.
7. Spusťte SetupEntryPoint z CodePackages a počkejte na dokončení.
8. Spusťte MainEntryPoint z CodePackages.

### <a name="servicetype-blocklisting"></a>ServiceType – Blocklisting
**ServiceTypeDisableFailureThreshold** určuje počet selhání (aktivace, stahování, CodePackage selhání), po jejichž uplynutí je pro Blocklisting naplánována hodnota serviceType. První Chyba aktivace, selhání stažení nebo CodePackage selhání způsobí naplánování blocklistingu ServiceType. Konfigurace **ServiceTypeDisableGraceInterval** určuje interval odkladu, po jehož uplynutí je ServiceType označen jako blocklisted v tomto uzlu. I když se to děje, aktivace, stažení a CodePackage restartování se opakují paralelně. Opakováním, znamená to, že se CodePackage bude naplánovat opětovné spuštění znovu po chybě nebo se Service Fabric pokusí znovu stáhnout balíčky.

Když je ServiceType blocklisted, zobrazí se chyba stavu System. hosting ohlásil chybu pro vlastnost ServiceTypeRegistration: ServiceType. ServiceType se zakázal na uzlu.

ServiceType se v uzlu povolí znovu, pokud dojde k některé z následujících akcí:
- Aktivace se zdaří nebo dosáhne **ActivationMaxFailureCount** opakování při selhání.
- Stažení se zdaří nebo dosáhne **DeploymentMaxFailureCountch** opakovaných pokusů po selhání.
- CodePackage, ve kterém došlo k chybě, spustí a úspěšně zaregistruje ServiceType.

**ActivationMaxFailureCount** a **DeploymentMaxFailureCount** jsou maximální počet pokusů, které Service Fabric aktivovat nebo stáhnout aplikaci na uzlu, a potom Service Fabric povolí znovu pro aktivaci serviceType. To znamená, že služba poskytuje další možnost aktivace, což může být úspěšné, což vede k potížím automaticky. Nová operace aktivace/stahování aktivovaná umístěním a aktivací repliky může znovu aktivovat modul ServiceType Blocklisting nebo může být úspěšný.

> [!NOTE]
> Pokud vaše CodePackage, který neregistruje ServiceType, dochází k chybě, nemá vliv na ServiceType. Pouze CodePackage hostující selhání repliky bude mít vliv na ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage chyby
Když dojde k selhání CodePackage, Service Fabric k opětovnému spuštění používá omezení rychlosti. Omezení rychlosti je nezávisle na tom, zda balíček kódu zaregistroval typ s Service Fabric modulem runtime nebo ne.

Hodnota omezení rychlosti je min (RetryTime, **ActivationMaxRetryInterval**) a tato hodnota omezení rychlosti se zvyšuje v konstantním, lineárním nebo exponenciálním množství na základě nastavení konfigurace **ActivationRetryBackoffExponentiationBase** .

- Konstanta: IF **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- Lineární: Pokud  **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** , kde ContinousFailureCount je počet chyb CodePackage nebo se aktivace nezdařila.
- Exponenciální: RetryTime = (**ActivationRetryBackoffInterval** v sekundách) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Chování můžete řídit změnou hodnot. Například pokud potřebujete několik rychlých pokusů o restartování, můžete použít lineární nastavením **ActivationRetryBackoffExponentiationBase** na 0 a **ActivationRetryBackoffInterval** na 10. Pokud se s těmito nastaveními CodePackage chyba, interval spouštění bude po 10 sekundách. Pokud balíček pokračuje v zhroucení, omezení rychlosti se změní na 20, 30, 40 sekund a tak dále, dokud neproběhne aktivace CodePackage nebo deaktivace balíčku kódu. 
    
Maximální doba, po kterou Service Fabric zpětně skončila (počká) po selhání se řídí **ActivationMaxRetryInterval**.
    
Pokud CodePackage dojde k chybě a je back-v, je potřeba, abyste si udrželi Service Fabric **CodePackageContinuousExitFailureResetInterval** , aby je bylo možné považovat za v pořádku. v takovém případě bude přepsána zpráva o stavu předchozí chyby jako ok a resetování ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage není zaregistrované. ServiceType
Pokud CodePackage zůstane aktivní a očekává se, že se s námi zaregistruje ServiceType, Service Fabric vygeneruje upozornění HealthReport po **ServiceTypeRegistrationTimeout** , že se ServiceType nezaregistroval v očekávaném časovém intervalu.

### <a name="activation-failure"></a>Chyba aktivace
Při nalezení chyby při aktivaci vždy používá Service Fabric lineární (stejné jako při selhání CodePackage). To znamená, že operace aktivace se poskytne po (0 + 10 + 20 + 30 + 40) = 100 SEC (první pokus je okamžitý). Po provedení této aktivace se nebude opakovat.
    
Maximální aktivační omezení rychlosti může být **ActivationMaxRetryInterval** a zkuste to znovu **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Chyba stahování
Service Fabric vždy používá lineární regresi, když při stahování dojde k chybě. To znamená, že operace aktivace se poskytne po (0 + 10 + 20 + 30 + 40) = 100 SEC (první pokus je okamžitý). V takovém případě se stahování neopakuje. Lineární záloha pro stažení se rovná ContinuousFailureCount **_DeploymentRetryBackoffInterval_* a může se maximum vrátit na **DeploymentMaxRetryInterval**. Podobně jako u aktivací se může operace stahování opakovat pro **ActivationMaxFailureCount**.

> [!NOTE]
> Před změnou těchto nastavení byste měli mít na paměti několik příkladů, které byste měli mít na paměti.

* Pokud CodePackage udržuje chybu a dojde k jejich vrácení, bude ServiceType zakázaný. Pokud je ale konfigurace aktivace taková, že má rychlé restartování, CodePackage se může několikrát setkat, než se ve skutečnosti blocklistede ServiceType. Příklad: Předpokládejme, že se váš CodePackage zaregistruje, zaregistruje ServiceType pomocí Service Fabric a pak dojde k chybě. V takovém případě Jakmile hostitel obdrží registraci typu, **ServiceTypeDisableGraceInterval** období se zruší. A to se může opakovat, dokud se CodePackage zpátky na hodnotu větší než  **ServiceTypeDisableGraceInterval** a pak se na uzlu blocklisted. Maytake se na ServiceType blocklisted, dokud neočekáváte jeho zobrazení.

* V případě aktivací, když Service Fabric systém musí umístit repliku na uzel, ReconfigurationAgent (RA) vyzve podsystém hostování, aby aplikaci aktivoval a znovu pokusy o aktivaci každých 15 sekund (řídí se nastavením konfigurace **RAPMessageRetryInterval** ). Pro Service Fabric o tom, že se ServiceType blocklisted, musí být operace aktivace v hostování živá po delší dobu než interval opakování a **ServiceTypeDisableGraceInterval**. Příklad: Předpokládejme, že cluster má **ActivationMaxFailureCount** nastavenou na hodnotu 5 a **ActivationRetryBackoffInterval** nastavenou na 1 sekundu. To znamená, že operace aktivace se poskytne po (0 + 1 + 2 + 3 + 4) = 10 sekund (odvolání při prvním pokusu okamžitě) a po dalším pokusu o hostování. V tomto případě se operace aktivace dokončí a nebude se opakovat po 15 sekundách. Důvodem je to, že Service Fabric vyčerpala všechna povolená opakování během 15 sekund. Každý pokus z ReconfigurationAgent proto vytvoří novou operaci aktivace v hostitelském subsystému a vzor bude pokračovat opakováním. V důsledku toho se ServiceType nikdy nebude blocklisted na uzlu. Vzhledem k tomu, že ServiceType nebude v uzlu blocklisted, replika nebude přesunuta a proveden na jiném uzlu.
> 

## <a name="deactivation"></a>Deaktivaci

Pokud je v uzlu aktivována ServicePack, je sledována pro deaktivaci. 

Deaktivace funguje dvěma způsoby:

- Pravidelně: v každé **DeactivationScanInterval** zkontroluje ServicePackages, že nikdy nehostuje repliku, a označí je jako kandidáti na deaktivaci.
- ReplicaClose: Pokud je replika zavřená, deaktivační rutina získá DecrementUsageCount. Pokud je počet převedený na 0, znamená to, že ServicePack není hostitelem žádné repliky, a proto je kandidátem na deaktivaci.

 V závislosti na režimu aktivace s [výhradním přístupem/sdílení][a2]se kandidáti na deaktivaci naplánují po **DeactivationGraceInterval** pro SharedMode a po **ExclusiveModeDeactivationGraceInterval** pro ExclusiveMode. Pokud se v době mezi tímto časem nachází umístění nové repliky, deaktivace se zruší.

### <a name="periodically"></a>Šetřete
Pojďme podiskutovat o několika příkladech periodické deaktivace:

Příklad 1: řekněme, že Deactivator provede kontrolu v čase T (**DeactivationScanInterval**). Další kontrola bude na 2T. Předpokládat, že při T + 1 došlo k aktivaci za ServicePack. Tato ServicePack nehostuje repliku, takže je nutné ji deaktivovat. Aby mohl být produkt ServicePack kandidátem na deaktivaci, musí být ve stavu bez repliky po dobu nejméně T. To znamená, že bude mít nárok na deaktivaci na 2T + 1. Proto kontrola na 2T nenajde tuto ServicePack jako kandidátovi na deaktivaci. Další cyklus deaktivace 3T naplánuje tuto ServicePack za deaktivaci, protože teď není ve stavu repliky pro čas T.  

Příklad 2: řekněme, že se aktivuje ServicePack v čase T-1 a Deactivator provede kontrolu v T. ServicePack nehostuje repliku. Pak při příští kontrole 2T tento software ServicePack jako kandidát pro deaktivaci, a proto se naplánuje deaktivace.  

Příklad 3: řekněme, že se aktivuje ServicePack v T – 1 a deaktivace provede kontrolu na T. ServicePack ještě nehostuje repliku. Nyní se replika nastavila na T + 1, tj. Hostování získá IncrementUsageCount, což znamená, že se vytvoří replika. Nyní v 2T tato pravidelná aktivace nebude naplánována na deaktivaci. Protože obsahuje repliku, deaktivace se přesune do logiky ReplicaClose popsané níže.

Příklad 4: řekněme, že váš ServicePack je velký, řekněme 10 GB a může trvat dlouhou dobu ke stažení na uzel. Po aktivaci aplikace deaktivační událost sleduje svůj životní cyklus. Teď, pokud máte **DeactivationScanInterval** konfiguraci nastavenou na malou hodnotu, můžete narazit na problémy, kdy se včas nezobrazuje čas na aktivaci uzlu, protože to je celý čas, který se má stáhnout. Chcete-li tento problém překonat, můžete si [před stažením aktualizace pro uzel][p1] nebo zvýšit **DeactivationScanInterval**. 

> [!NOTE]
> ServicePack se může deaktivovat kdekoliv (**DeactivationScanInterval** až 2 **_DeactivationScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval * *. 
>

### <a name="replica-close"></a>Zavření repliky:
Deaktivace zajišťuje počet replik, které obsahuje ServicePack. Pokud je v rámci ServicePack replika a replika je uzavřená/vypnutá, hostitel získá DecrementUsageCount. Při otevření repliky získá hostitel IncrementUsageCount. Snížení znamená, že "ServicePack" teď hostuje jednu méně repliku, a když je počet převedený na 0, pak je naplánovaná deaktivace a doba, po kterou bude deaktivovaná, je **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Příklad: řekněme, že k deaktivaci dojde v T a v 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) se plánuje deaktivovat. Pokud během této doby hosting získá IncrementUsage, znamená to, že se vytvoří replika, deaktivace se zruší.

> [!NOTE]
> Co tyto konfigurační nastavení znamená?
**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: čas poskytnutý službě ServicePack pro hostování jiné repliky, jakmile bude hostitelem jakékoli repliky. 
**DeactivationScanInterval**: minimální čas poskytnutý službě ServicePack k hostování repliky, pokud nikdy nehostuje žádnou repliku, tj. Pokud se nepoužívá.
>

### <a name="ctrlc"></a>Ctrl+C
Jakmile ServicePack předá **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** a stále nehostuje repliku, deaktivuje se non-zrušit. CodePackage se vydávají obslužné rutiny CTRL + C, která znamená, že teď musí kanál deaktivace projít, aby se proces mohl zvýšit. V tuto chvíli se při pokusu o získání nové repliky, která se pokusí o uvedení do provozu, nezdaří, protože nemůžeme přejít z deaktivace na aktivaci.

## <a name="cluster-configs"></a>Konfigurace clusteru

Konfigurace s výchozími vlivem na aktivaci/decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: výchozí 1. Prahová hodnota počtu selhání, po které se FailoverManager (FM) upozorní na zakázání typu služby v tomto uzlu a pro umístění zkuste použít jiný uzel.
**ServiceTypeDisableGraceInterval**: výchozí hodnota je 30 sekund. Časový interval, po kterém může být typ služby zakázán.
**ServiceTypeRegistrationTimeout**: výchozí hodnota je 300 s. Časový limit pro ServiceType k registraci v Service Fabric.

### <a name="activation"></a>Aktivace
**ActivationRetryBackoffInterval**: výchozí hodnota je 10 sekund. Interval omezení rychlosti při každé chybě aktivace
**ActivationMaxFailureCount**: výchozí 20. Maximální počet, který systém před tím, než se znovu pokusí o aktivaci, se nezdařil. 
**ActivationRetryBackoffExponentiationBase**: výchozí 1,5.
**ActivationMaxRetryInterval**: výchozí hodnota je 3600 s. Maximální počet Back-off pro aktivaci při selhání.
**CodePackageContinuousExitFailureResetInterval**: výchozí hodnota je 300 s. Časový limit pro resetování čítače selhání nepřetržitého ukončení pro CodePackage.

### <a name="download"></a>Stáhnout
**DeploymentRetryBackoffInterval**: výchozí hodnota 10. Záložní interval pro selhání nasazení.
**DeploymentMaxRetryInterval**: výchozí hodnota je 3600 s. Maximální počet Back-off pro nasazení při selhání.
**DeploymentMaxFailureCount**: výchozí 20. Nasazení aplikace se bude opakovat po DeploymentMaxFailureCount dobu, než dojde k selhání nasazení této aplikace na uzlu.

### <a name="deactivation"></a>Deaktivaci
**DeactivationScanInterval**: výchozí hodnota je 600 s. Minimální doba, kterou má ServicePack hostovat repliku, pokud nikdy nehostuje žádnou repliku, tj. Pokud se nepoužívá.
**DeactivationGraceInterval**: výchozí hodnota je 60 s. Čas poskytnutý ServicePack k hostování znovu další repliky, jakmile bude hostitelem jakékoli repliky v případě modelu **sdíleného** procesu.
**ExclusiveModeDeactivationGraceInterval**: výchozí hodnota 1 s. Čas poskytnutý ServicePack pro opětovné hostování jiné repliky, jakmile hostuje jakoukoli repliku v případě **exkluzivního** modelu procesu.

## <a name="next-steps"></a>Další kroky
[Zabalit aplikaci][a3] a připravit ji na nasazení.

[Nasaďte a odeberte aplikace][a4]. Tento článek popisuje, jak pomocí PowerShellu spravovat instance aplikací.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
