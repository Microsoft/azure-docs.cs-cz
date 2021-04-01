---
title: Azure Service Fabric hostování aktivace a životního cyklu deaktivace
description: Seznamte se s životním cyklem aplikace a ServicePackem na uzlu.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831818"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Životní cyklus hostování Azure Service Fabric

Tento článek poskytuje přehled událostí, ke kterým dochází v Azure Service Fabric, když je na uzlu aktivována aplikace. Vysvětluje různé konfigurace clusteru, které řídí chování.

Než budete pokračovat, ujistěte se, že rozumíte konceptům a vztahům, které jsou vysvětleny v tématu [modelování aplikace v Service Fabric][a1]. 

> [!NOTE]
> Tento článek používá pro specializované způsoby určitou terminologii. Pokud není uvedeno jinak:
>
> - *Replika* odkazuje jak na repliku stavové služby, tak na instanci bezstavové služby.
> - *CodePackage* se považuje za ekvivalent procesu ServiceHost, který registruje serviceType. Hostuje repliky služeb tohoto ServiceType.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Aktivovat ApplicationPackage nebo ServicePack

Aktivace ApplicationPackage nebo ServicePack:

1. Stáhněte si ApplicationPackage (například *ApplicationManifest.xml*).
2. Nastavte prostředí pro aplikaci. Mezi tyto kroky patří například vytváření uživatelů.
3. Spustit sledování aplikace pro deaktivaci.
4. Stáhněte si aktualizace ServicePack (například *ServiceManifest.xml*, kód, konfigurační soubory a balíčky dat).
5. Nastavte prostředí pro ServicePack. Mezi tyto kroky patří například nastavení brány firewall a přidělování portů pro koncové body.
6. Začít sledovat ServicePack pro deaktivaci.
7. Spusťte SetupEntryPoint pro CodePackages a počkejte, než se dokončí.
8. Spusťte MainEntryPoint pro CodePackages.

### <a name="servicetype-blocklisting"></a>ServiceType – Blocklisting
`ServiceTypeDisableFailureThreshold` Určuje počet povolených selhání aktivace, stahování a CodePackage. Po dosažení prahové hodnoty je ServiceType naplánován pro Blocklisting. První Chyba aktivace, selhání stažení nebo naplánování neCodePackagech chyb Blocklisting. 

`ServiceTypeDisableGraceInterval`Konfigurace určuje interval odkladu před blocklistedem ServiceType na uzlu. Vzhledem k tomu, že se tento proces přehrává, je aktivace, stažení a CodePackage restart opakována paralelně. Opakováním znamená, že CodePackage je naplánováno na opětovné spuštění po havárii nebo Service Fabric se pokusí znovu stáhnout balíčky.

Když je ServiceType blocklisted, zobrazí se chyba stavu: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

ServiceType se na uzlu povolí znovu, pokud dojde k některé z následujících událostí:
- Aktivace byla úspěšná. Nebo pokud dojde k chybě, dosáhne `ActivationMaxFailureCount` maximálního počtu opakování.
- Stahování bylo úspěšné. Nebo pokud dojde k chybě, dosáhne `DeploymentMaxFailureCount` maximálního počtu opakování.
- CodePackage, ve kterém došlo k chybě, spustí a úspěšně zaregistruje ServiceType.

`ActivationMaxFailureCount` a `DeploymentMaxFailureCount` jsou maximální počet pokusů, které Service Fabric provede aktivaci nebo stažení aplikace na uzlu. Po dosažení maxima Service Fabric povolí znovu ServiceType pro aktivaci. 

Tyto prahové hodnoty poskytují službu další příležitost pro aktivaci. Pokud je aktivace úspěšná, problém je automaticky zacelený. 

Nově umístěná nebo aktivovaná replika může aktivovat novou operaci aktivace nebo stahování. Tato akce buď bude úspěšná, nebo znovu spustí Blocklisting ServiceType.

> [!NOTE]
> Selhání CodePackage, které neregistruje ServiceType, nemá vliv na ServiceType. Pouze selhání CodePackage, který hostuje repliku, má vliv na ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage chyby
Když dojde k selhání CodePackage, Service Fabric k opětovnému spuštění používá omezení rychlosti. Omezení rychlosti se použije bez ohledu na to, jestli CodePackage zaregistroval typ s Service Fabric modulem runtime.

Hodnota omezení rychlosti je `Min(RetryTime, ActivationMaxRetryInterval)` . Hodnota se zvýší v konstantních, lineárních nebo exponenciálních částkách na základě `ActivationRetryBackoffExponentiationBase` nastavení konfigurace:

- **Konstanta**: IF `ActivationRetryBackoffExponentiationBase == 0` a then `RetryTime = ActivationRetryBackoffInterval` .
- **Lineární**: Pokud  `ActivationRetryBackoffExponentiationBase == 0` a pak `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` , kde `ContinuousFailureCount` je počet havárií CodePackage nebo selhání aktivace.
- **Exponenciální**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)`
    
Chování můžete řídit změnou hodnot. Například pokud chcete provést několik rychlých pokusů o restartování, můžete použít lineární částky nastavením `ActivationRetryBackoffExponentiationBase` na `0` a nastavením `ActivationRetryBackoffInterval` na `10` . Takže pokud dojde k selhání CodePackage, interval spouštění bude po 10 sekundách. Pokud balíček stále selhává, omezení rychlosti se změní na 20 sekund, 30 sekund, 40 sekund atd., dokud nedojde k úspěšné aktivaci CodePackage nebo deaktivace CodePackage. 
    
Maximální doba, po kterou Service Fabric zpětně (tj. čeká na vyřízení) po selhání, kterou řídí `ActivationMaxRetryInterval` .
    
Pokud dojde k selhání CodePackage a dojde k zálohování, musí být v průběhu časového období určeného `CodePackageContinuousExitFailureResetInterval` . Po tomto intervalu Service Fabric považuje CodePackage za dobrý. Service Fabric pak přepíše zprávu o stavu předchozí chyby jako OK a resetuje `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>CodePackage není zaregistrované. ServiceType
Pokud CodePackage zůstane v pořádku a očekává se, že se má zaregistrovat ServiceType, Service Fabric vygeneruje zprávu o stavu upozornění po `ServiceTypeRegistrationTimeout` . Tato sestava označuje, že ServiceType se nezaregistroval během očekávaného časového období.

### <a name="activation-failure"></a>Chyba aktivace
Když Service Fabric při aktivaci najde chybu, vždy použije lineární omezení rychlosti, protože se jedná o chybu CodePackage. Aktivační operace se zadává po opakování v těchto intervalech: (0 + 10 + 20 + 30 + 40) = 100 sekund. (První pokus je okamžitý.) Po této sekvenci se aktivace nebude opakovat.
    
Maximální aktivační omezení rychlosti může být `ActivationMaxRetryInterval` . Opakování může být `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Chyba stahování
Service Fabric vždy používá lineární omezení rychlosti, když při stahování najde chybu. Aktivační operace se zadává po opakování v těchto intervalech: (0 + 10 + 20 + 30 + 40) = 100 sekund. (První pokus je okamžitý.) Po této sekvenci se stahování nebude opakovat. 

Lineární omezení rychlosti pro stažení se rovná `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . Maximální omezení rychlosti může být `DeploymentMaxRetryInterval` . Podobně jako u aktivací můžou operace stahování na limit opakovat `ActivationMaxFailureCount` .

> [!NOTE]
> Před změnou těchto nastavení Pamatujte na následující příklady:
>
>* Pokud CodePackage udržuje chybu a dojde k jejímu zálohování, bude tento ServiceType zakázán. Pokud ale konfigurace aktivace má rychlé restartování, může se CodePackage několikrát setkat předtím, než se ve skutečnosti blocklistedý ServiceType. 
>
>    Řekněme například, že váš CodePackage se objeví, zaregistruje ServiceType pomocí Service Fabric a pak dojde k chybě. V takovém případě, po hostování obdrží registraci typu, `ServiceTypeDisableGraceInterval` je období zrušeno. Tento proces se může opakovat, dokud se CodePackage zpět na hodnotu větší než `ServiceTypeDisableGraceInterval` perioda. Pak bude ServiceType blocklisted na uzlu. Blocklisting ServiceType může trvat déle, než očekáváte.
>
>* V případě aktivací, když Service Fabric systém musí umístit repliku na uzel, agent rekonfigurace požádá hostujícímu subsystému, aby aplikaci aktivoval. Pokaždé 15 sekund opakuje žádost o aktivaci. (Doba trvání závisí na `RAPMessageRetryInterval` nastavení konfigurace.) Service Fabric nedokáže zjistit, že se ServiceType blocklisted, pokud se operace aktivace v hostování neuvolní po delší dobu, než je interval opakování a `ServiceTypeDisableGraceInterval` . 
>
>    Předpokládejme například, že cluster `ActivationMaxFailureCount` je nastaven na hodnotu 5 a `ActivationRetryBackoffInterval` je nastaven na hodnotu 1 sekunda. V tomto případě aktivační operace zadává po intervalech (0 + 1 + 2 + 3 + 4) = 10 sekund. (První pokus je okamžitý.) Po této sekvenci se hostitel bude pokoušet o opakování. Operace aktivace skončí a nebude se opakovat po 15 sekundách. 
>
>    Service Fabric vyčerpala všechna povolená opakování během 15 sekund. Každý pokus od agenta opětovné konfigurace proto vytvoří novou operaci aktivace v hostitelském podsystému a vzor se opakuje. V důsledku toho není ServiceType nikdy blocklisted na uzlu. Vzhledem k tomu, že se v uzlu ServiceType nebude blocklisted, replika se nepřesouvá a zkouší na jiném uzlu.
> 

## <a name="deactivation"></a>Deaktivaci

Pokud je v uzlu aktivována ServicePack, je sledována pro deaktivaci. Deaktivace funguje dvěma způsoby:

- **Pravidelná deaktivace**: `DeactivationScanInterval` systém kontroluje balíčky služeb, které *nikdy* nehostují repliku, a označí je jako kandidáty na deaktivaci.
- **Deaktivace ReplicaClose**: Pokud je replika zavřená, deaktivační rutina získá `DecrementUsageCount` . Počet je 0, pokud se nehostuje žádná replika, a proto je jako kandidát pro deaktivaci k dispozici.

Režim aktivace určuje, kdy jsou kandidáti naplánováni deaktivace. Ve sdíleném režimu se kandidáti na deaktivaci naplánují po `DeactivationGraceInterval` . Ve výhradním režimu jsou naplánovány po `ExclusiveModeDeactivationGraceInterval` . Pokud mezi tyto časy přijde nové umístění repliky, deaktivace se zruší. 

Další informace najdete v tématu [exkluzivní režim a sdílený režim][a2].

### <a name="periodic-deactivation"></a>Pravidelná deaktivace
Tady je několik příkladů periodické deaktivace:

* **Příklad 1**: řekněme, že Deactivator spustí kontrolu v čase T (a `DeactivationScanInterval` ). Další kontrola bude na 2T. Předpokládat, že při T + 1 došlo k aktivaci za ServicePack. Tato ServicePack nehostuje repliku, takže je potřeba ji deaktivovat. 

    Aby byli kandidátem na deaktivaci, je potřeba, aby včas nemusela hostovat žádné repliky. Bude mít nárok na deaktivaci na 2T + 1. Proto kontrola na 2T neidentifikuje tuto ServicePack jako kandidát pro deaktivaci. 

    Další cyklus deaktivace 3T naplánuje tuto ServicePack za deaktivaci, protože teď balíček je ve stavu bez repliky pro čas T.  

* **Příklad 2**: řekněme, že se aktivovala ServicePack v čase t-1 a deaktivační modul spouští kontrolu v T. ServicePack nehostuje repliku. Při příštím prověřování se 2T jako kandidát pro deaktivaci, aby se naplánovala deaktivace.  

* **Příklad 3**: řekněme, že se aktivovala ServicePack v t – 1 a deaktivační modul spouští kontrolu v t. ServicePack ještě nehostuje repliku. Nyní se replika umístí na T + 1. To znamená, že hostování získá `IncrementUsageCount` , což znamená, že se vytvoří replika. 

    V 2T se tato oprava nebude naplánovat na deaktivaci. Vzhledem k tomu, že balíček obsahuje repliku, deaktivace bude následovat po logice ReplicaClose, jak je popsáno v další části tohoto článku.

* **Příklad 4**: řekněme, že vaše ServicePack je 10 GB. Vzhledem k tomu, že je balíček velký, trvá stažení na uzlu déle. Při aktivaci aplikace deaktivační událost sleduje svůj životní cyklus. Pokud `DeactivationScanInterval` je nastavená malá hodnota, nemusíte mít k aktivaci na uzlu čas potřebný ke stažení. Chcete-li tento problém vyřešit, můžete [si na uzel předem stáhnout aktualizaci ServicePack][p1] nebo zvýšit `DeactivationScanInterval` . 

> [!NOTE]
> ServicePack se může deaktivovat kdekoliv ( `DeactivationScanInterval` až 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>Deaktivace ReplicaClose

> [!NOTE]
> Tato část se týká následujících nastavení konfigurace:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: čas poskytnutý službě ServicePack k hostování jiné repliky, pokud již hostuje jakoukoli repliku. 
> - **DeactivationScanInterval**: minimální čas poskytnutý ServicePack k hostování repliky, pokud *nikdy* nehostuje žádnou repliku, tj. Pokud se nepoužila.
>

Systém udržuje počet replik, které obsahuje ServicePack. Pokud je v rámci ServicePack replika a replika je uzavřená nebo vypnutá, hostování získá `DecrementUsageCount` . Při otevření repliky získá hostitel `IncrementUsageCount` . 

Snížení udává, že počet replik, které služba ServicePack za provozuje, se snížila o jednu repliku. Pokud je počet odložen na hodnotu 0, je naplánována deaktivace služby ServicePack. Čas, po který bude deaktivován, je `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Řekněme například, že dojde k snížení hodnoty na T a že je naplánována deaktivace služby ServicePack na 2T + X ( `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` ). Během této doby, pokud hostitel získá výjimku `IncrementUsage` , protože je vytvořena replika, deaktivace je zrušena.

### <a name="ctrl--c"></a>Ctrl + C
Pokud aplikace ServicePack předá `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` a stále nehostuje repliku, nelze zrušit deaktivaci. CodePackages obdrží obslužnou rutinu CTRL + C. Takže teď je potřeba, aby kanál deaktivace dokončil proces. 

V této chvíli se při pokusu o uvedení nové repliky pro stejnou ServicePack, nezdaří, protože proces nemůže přejít z deaktivace na aktivaci.

## <a name="cluster-configurations"></a>Konfigurace clusterů

Tato část obsahuje seznam konfigurací, které mají výchozí hodnoty, které mají vliv na aktivaci a deaktivaci.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: výchozí: 1. Prahová hodnota pro počet selhání; Po dosažení této prahové hodnoty se FailoverManager upozorní na zakázání typu služby na uzlu a pro umístění zkuste použít jiný uzel.
- **ServiceTypeDisableGraceInterval**: výchozí: 30 sekund. Časový interval, po kterém může být typ služby zakázán.
- **ServiceTypeRegistrationTimeout**: výchozí: 300 sekund. Časový limit pro zápis do Service Fabric.

### <a name="activation"></a>Aktivace
- **ActivationRetryBackoffInterval**: výchozí: 10 sekund. Omezení rychlosti interval pro každé selhání aktivace.
- **ActivationMaxFailureCount**: výchozí: 20. Maximální počet, pro který se systém před tím, než se aktivuje, zopakuje aktivaci. 
- **ActivationRetryBackoffExponentiationBase**: výchozí: 1,5.
- **ActivationMaxRetryInterval**: výchozí: 3 600 sekund. Maximální interval opakování omezení rychlosti pro aktivaci po selháních.
- **CodePackageContinuousExitFailureResetInterval**: výchozí: 300 sekund. Interval časového limitu pro resetování čítače selhání nepřetržitého ukončení pro CodePackage.

### <a name="download"></a>Stáhnout
- **DeploymentRetryBackoffInterval**: výchozí: 10. Omezení rychlosti interval pro selhání nasazení.
- **DeploymentMaxRetryInterval**: výchozí: 3 600 sekund. Maximální interval omezení rychlosti nasazení po selhání.
- **DeploymentMaxFailureCount**: výchozí: 20. Nasazení aplikace se bude opakovat po `DeploymentMaxFailureCount` dobu před selháním nasazení této aplikace na uzlu.

### <a name="deactivation"></a>Deaktivaci
- **DeactivationScanInterval**: výchozí: 600 sekund. Minimální doba, kterou má ServicePack sloužit k hostování repliky, pokud nikdy nehostuje žádnou repliku (tj. Pokud se nepoužívá).
- **DeactivationGraceInterval**: výchozí: 60 sekund. V modelu *sdíleného* procesu je čas poskytnutý ServicePack znovu hostitelem jiné repliky poté, co již hostuje jakoukoli repliku.
- **ExclusiveModeDeactivationGraceInterval**: výchozí: 1 sekunda. V modelu *exkluzivního* procesu, čas poskytnutý útočníkovi znovu hostovat další repliku poté, co už hostuje jakoukoli repliku.

## <a name="next-steps"></a>Další kroky

- [Zabalit aplikaci][a3] a připravit ji na nasazení.
- [Nasaďte a odeberte aplikace][a4] v PowerShellu.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
