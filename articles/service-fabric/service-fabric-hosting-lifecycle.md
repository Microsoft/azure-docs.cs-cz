---
title: Azure Service Fabric hostování aktivace a deaktivace životního cyklu
description: Vysvětluje životní cyklus aplikace a ServicePack na uzlu.
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87405122"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Životní cyklus hostování Azure Service Fabric
Tento článek poskytuje přehled událostí, ke kterým dochází, když se aplikace aktivuje na uzlu a v různých konfiguracích clusteru používaných k řízení chování.

Než budete pokračovat, ujistěte se, že rozumíte různým konceptům a vztahům, které jsou vysvětleny v tématu [modelování aplikace v Service Fabric][a1]. 

> [!NOTE]
> V tomto článku, pokud výslovně nezmiňujete o něco jiného:
>
> - *Replika* odkazuje jak na repliku stavové služby, tak na instanci bezstavové služby.
> - *CodePackage* se považuje za ekvivalent procesu *ServiceHost* , který registruje *ServiceType*a hostuje repliky služeb tohoto *ServiceType*.
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
**ServiceTypeDisableFailureThreshold** určuje počet selhání (aktivace, stahování, CodePackage selhání), po jejichž uplynutí je pro Blocklisting naplánována hodnota serviceType. Proto by se první aktivace/stažení nezdařila nebo CodePackagea chyba, měla by se aktivovat plán pro ServiceType Blocklisting. Konfigurace **ServiceTypeDisableGraceInterval** určuje interval odkladu, po jehož uplynutí je ServiceType označen jako blocklisted v tomto uzlu. Všimněte si, že všechny tyto akce se mají aktivovat/stáhnout/CodePackage restartování by mělo být stále v režimu opakování a bude sledováno hostujícím podsystémem. Opakování, například: CodePackage bude naplánováno na opětovné spuštění po havárii nebo se Service Fabric pokusí stáhnout balíčky znovu.
Po blocklisted by se měla zobrazit chyba "" System. hosting "ohlásil chybu pro vlastnost" ServiceTypeRegistration: ServiceType ". ServiceType se zakázal na uzlu.

ServiceType se povolí zpátky na uzlu. 
- Pokud operace aktivace uspěje nebo dosáhne **ActivationMaxFailureCount** opakování při selhání.
- Pokud je operace stahování úspěšná nebo při selhání dosáhne **DeploymentMaxFailureCount** opakování.
- Pokud se CodePackage, který selhal, spustí zálohování a úspěšně zaregistruje ServiceType.

Důvod, proč se znovu povolí ServiceType po **ActivationMaxFailureCount** / **DeploymentMaxFailureCount** , je maximální počet pokusů Service Fabric o aktivaci nebo stažení aplikace na uzlu. Pokud to nepomůže, aktuální operace se neopakuje a vzhledem k tomu, že Service Fabric chce poskytnout službě jinou příležitost pro aktivaci, což může být úspěšné, výsledkem je to, že se problém vyřeší automaticky, je vázaný na životní cyklus operace aktivace/stahování. Nová operace aktivace/stahování aktivovaná umístěním repliky může znovu aktivovat ServiceType Blocklisting nebo může být úspěšná.

> [!NOTE]
> Pokud vaše CodePackage, který neregistruje ServiceType, dochází k chybě, nemá vliv na ServiceType. Pouze CodePackage hostující selhání repliky bude mít vliv na ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage chyby
Když dojde k selhání CodePackage, Service Fabric k opětovnému spuštění používá Back-off a je nezávisle na tom, zda balíček kódu zaregistroval typ s námi nebo ne.

Hodnota Back-off je vždycky minimální (RetryTime, **ActivationMaxRetryInterval**) a tato hodnota může být konstantní, lineární nebo exponenciální na základě konfigurace **ActivationRetryBackoffExponentiationBase** .

- Konstanta: IF **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- Lineární: Pokud  **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** , kde ContinousFailureCount je počet chyb CodePackage nebo se aktivace nezdařila.
- Exponenciální: RetryTime = (**ActivationRetryBackoffInterval** v sekundách) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Chování můžete řídit tak, jak chcete, jako je rychlé restartování. Pojďme se na to lineárně obrátit. To znamená, že pokud dojde k selhání CodePackage, pak bude interval spuštění po 10, 20, 30 40 sec až do deaktivace CodePackage. 
    
Maximální doba, po kterou Service Fabric zpětně skončila (počká) po selhání se řídí **ActivationMaxRetryInterval**
    
Pokud CodePackage dojde k chybě a je back-v, je potřeba, abyste si udrželi Service Fabric **CodePackageContinuousExitFailureResetInterval** , aby je bylo možné považovat za v dobrém stavu, a to tak, že se zpráva o stavu přepíše jako ok a resetuje se ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage není zaregistrované. ServiceType
Pokud CodePackage zůstane aktivní a očekává se, že se s námi zaregistruje ServiceType, ale v takovém případě Service Fabric vygeneruje upozornění HealthReport po **ServiceTypeRegistrationTimeout** , že se ServiceType nenakonfiguroval v rámci časového limitu.

### <a name="activation-failure"></a>Chyba aktivace
Při nalezení chyby při aktivaci vždy používá Service Fabric lineární (stejné jako při selhání CodePackage). To znamená, že operace aktivace se poskytne po (0 + 10 + 20 + 30 + 40) = 100 SEC (první pokus je okamžitý). Po provedení této aktivace se nebude opakovat.
    
Maximální aktivační omezení rychlosti může být **ActivationMaxRetryInterval** a zkuste to znovu **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Chyba stahování
Service Fabric vždy používá lineární regresi, když při stahování dojde k chybě. To znamená, že operace aktivace se poskytne po (0 + 10 + 20 + 30 + 40) = 100 SEC (první pokus je okamžitý). Po tomto stažení se tento pokus neopakuje. Lineární záloha pro stažení se rovná ContinuousFailureCount ***DeploymentRetryBackoffInterval** a může se maximum vrátit na **DeploymentMaxRetryInterval**. Podobně jako u aktivací se může operace stahování opakovat pro **ActivationMaxFailureCount**.

> [!NOTE]
> Před změnou konfigurací je zde několik příkladů, které byste měli mít na paměti.

* Pokud CodePackage zachová chybu a vrátí se zpět, ServiceType se zakáže. Pokud ale konfigurace aktivace funguje tak, že má rychlé restartování, může se CodePackage několikrát zobrazit, než uvidí zakázaný ServiceType. Příklad: Předpokládejme, že se váš CodePackage zaregistruje, zaregistruje ServiceType pomocí Service Fabric a pak dojde k chybě. V takovém případě Jakmile hostitel obdrží registraci typu, **ServiceTypeDisableGraceInterval** období se zruší. To se může opakovat, dokud se CodePackage nevrátí na hodnotu větší než  **ServiceTypeDisableGraceInterval** a pak se na uzlu deaktivuje serviceType. To znamená, že před zakázáním ServiceType v uzlu může to chvíli trvat.

* V případě aktivací, když Service Fabric systém musí umístit repliku na uzel, RA (ReconfigurationAgent) požádá o aktivaci aplikace a znovu zkusí požadavek na aktivaci každých 15 sekund (**RAPMessageRetryInterval**). Aby systém Service Fabric zjistil, že je ServiceType zakázaný, operace aktivace v hostování musí být živá po delší dobu než interval opakování a **ServiceTypeDisableGraceInterval**. Například: Umožněte, aby cluster **ActivationMaxFailureCount** konfigurační nastavení na hodnotu 5 a **ActivationRetryBackoffInterval** nastaveno na hodnotu 1 s. To znamená, že operace aktivace se poskytne po (0 + 1 + 2 + 3 + 4) = 10 sec (první pokus je okamžitý) a po tomto hostování se bude opakovat. V tomto případě se operace aktivace dokončí a nebude se opakovat po 15 sekundách. K ní došlo, protože Service Fabric vyčerpala všechny opakované pokusy během 15 sekund. Každý pokus z ReconfigurationAgent proto vytvoří novou operaci aktivace v hostitelském subsystému a vzor se zachová opakujícím se a ServiceType se v uzlu nikdy nevypne. Vzhledem k tomu, že se ServiceType nebude na uzlu zakázaný, nepřesunou se replika komponenty SF systému (FailoverManager) do jiného uzlu.
> 

## <a name="deactivation"></a>Deaktivaci

Pokud je v uzlu aktivována ServicePack, je sledována pro deaktivaci. Deactivator je entita, která ho sleduje.
Deaktivační modul funguje dvěma způsoby:

1.  Pravidelně: v každé **DeactivationScanInterval**zkontroluje ServicePackages, která nikdy nehostuje repliku, a označí je jako kandidáty na deaktivaci.
2.  ReplicaClose: Pokud je replika zavřená, deaktivační rutina získá DecrementUsageCount. Pokud je počet převedený na 0, znamená to, že "ServicePack" není hostitelem žádné repliky, a proto je kandidátem na deaktivaci.

 V závislosti na režimu aktivace [exkluzivní/Share][a2]se kandidáty na deaktivaci naplánovaly po **DeactivationGraceInterval** pro SharedMode/ **ExclusiveModeDeactivationGraceInterval** pro ExclusiveMode. Pokud v době mezi tím přijde nové umístění repliky, v případě, že je zrušena, deaktivace se zruší.

### <a name="periodically"></a>Šetřete
Příklad 1: řekněme, že Deactivator provede kontrolu v čase T (**DeactivationScanInterval**). Další kontrola bude na 2T. Předpokládat, že při T + 1 došlo k aktivaci za ServicePack. Tato ServicePack nehostuje repliku, takže je nutné ji deaktivovat. Aby mohl být produkt ServicePack kandidátem na deaktivaci, musí být ve stavu bez repliky po dobu nejméně T. To znamená, že bude mít nárok na deaktivaci na 2T + 1. Proto kontrola na 2T nenajde tuto ServicePack jako kandidátovi na deaktivaci. Další cyklus deaktivace 3T naplánuje tuto ServicePack za deaktivaci, protože teď není ve stavu repliky pro čas T.  

Příklad 2: řekněme, že se aktivuje ServicePack v čase T-1 a Deactivator provede kontrolu v T. ServicePack nehostuje repliku. Pak při příští kontrole 2T tento software ServicePack jako kandidát pro deaktivaci, a proto se naplánuje deaktivace.  

Příklad 3: řekněme, že se aktivuje ServicePack v T – 1 a deaktivace provede kontrolu na T. ServicePack ještě nehostuje repliku. Nyní se replika nastavila na T + 1, tj. Hostování získá IncrementUsageCount, což znamená, že se vytvoří replika. Nyní v 2T tato pravidelná aktivace nebude naplánována na deaktivaci. Nyní se deaktivace přesune do logiky ReplicaClose, která je vysvětlena níže.

Příklad 4: řekněme, že je váš ServicePack velká, jako je 10 GB, a pak může trvat dlouhou dobu ke stažení na uzel. Po aktivaci aplikace deaktivační událost sleduje svůj životní cyklus. Teď, pokud máte malou konfiguraci **DeactivationScanInterval** , můžete narazit na problémy, kdy by se v uzlu nedostal čas na aktivaci, protože to je vše, co je potřeba při stahování. Chcete-li vyřešit tento problém, můžete si [před stažením aktualizace pro tento uzel předem stáhnout aktualizaci ServicePack][p1]. 

> [!NOTE]
> To znamená, že se dá deaktivovat ServicePack odkudkoli mezi (**DeactivationScanInterval** až 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Zavření repliky:
Deactivator udržuje počet replik, které obsahuje ServicePack. Pokud je v rámci ServicePack replika a replika je uzavřená/vypnutá, hostitel získá DecrementUsageCount. Při otevření repliky získá hostitel IncrementUsageCount. Snížení znamená, že "ServicePack" teď hostuje jednu méně repliku, a když je počet převedený na 0, pak je naplánovaná deaktivace a doba, po kterou bude deaktivovaná, je **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Příklad: řekněme, že k deaktivaci dojde v T a v 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) se plánuje deaktivovat. Pokud během této doby hosting získá IncrementUsage, znamená to, že se vytvoří replika, deaktivace se zruší.

> [!NOTE]
>Takže tato konfigurace v podstatě znamená: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: čas poskytnutý pro ServicePack k hostování opětovného hostování jiné repliky, jakmile bude hostitelem jakékoli repliky. 
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
