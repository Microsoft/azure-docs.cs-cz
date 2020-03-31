---
title: 'Upgrade aplikace: parametry upgradu'
description: Popisuje parametry související s inovací aplikace Service Fabric, včetně kontrol stavu, které mají být provádět, a zásad pro automatické zrušení upgradu.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 42b5c52181cfb006ae57e43c183b96a059a9c63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377969"
---
# <a name="application-upgrade-parameters"></a>Parametry upgradu aplikace
Tento článek popisuje různé parametry, které platí při upgradu aplikace Azure Service Fabric. Parametry upgradu aplikace řídí časové výtky a kontroly stavu, které jsou použity během upgradu, a určují zásady, které musí být použity při selhání upgradu. Parametry aplikace platí pro upgrady pomocí:
- PowerShell
- Visual Studio
- SFCTL
- [Odpočinku](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Upgrady aplikací jsou inicializovány prostřednictvím jednoho ze tří režimů upgradu volitelného uživatelem. Každý režim má svou vlastní sadu parametrů aplikace:
- Sledovat
- Nemonitorované auto
- Nesledovaný manuál

Příslušné požadované a volitelné parametry jsou popsány v každé části následovně.

## <a name="visual-studio-and-powershell-parameters"></a>Parametry Visual Studia a Prostředí PowerShell

Upgrady aplikací Service Fabric pomocí prostředí PowerShell používají příkaz [Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) Režim upgradu je vybrán předáním parametru **Monitored**, **UnmonitoredAuto**nebo **UnmonitoredManual** do [programu Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Parametry upgradu aplikace Visual Studio Service Fabric jsou nastaveny pomocí dialogového okna Nastavení upgradu sady Visual Studio. Režim upgradu sady Visual Studio je vybrán pomocí rozevíracího pole **Režim upgradu** na **monitorované**, **UnmonitoredAuto**nebo **UnmonitoredManual**. Další informace naleznete [v tématu Konfigurace inovace aplikace Service Fabric v sadě Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Požadované parametry
(PS=PowerShell, VS=Visual Studio)

| Parametr | Platí pro | Popis |
| --- | --- | --- |
ApplicationName |PS| Název aplikace, která je právě upgradována. Příklady: fabric:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Verze typu aplikace, na kterou se upgrade zaměřuje. |
Akce selhání |PS, VS|Povolené hodnoty jsou **Vrácení zpět**, **Ruční**a **Neplatné**. Kompenzační akce, která má být vpřípadě *sledovaného* upgradu, narazí na zásady monitorování nebo porušení zásad stavu. <br>**Vrácení zpět** určuje, že upgrade se automaticky vrátí zpět na verzi před upgradem. <br>**Příručka** označuje, že upgrade se přepne do režimu upgradu *UnmonitoredManual.* <br>**Neplatný** označuje, že akce selhání je neplatná.|
Sledovat |PS|Označuje, že je sledován režim upgradu. Po dokončení aktualizace rutiny pro upgradovací doménu, pokud stav domény upgradu a clusteru splňují zásady stavu, které definujete, Service Fabric upgraduje další doménu upgradu. Pokud upgradovací doména nebo cluster nesplňuje zásady stavu, upgrade se nezdaří a service fabric vrátí upgrade pro upgradovací doménu nebo se vrátí do ručního režimu podle zadané zásady. Toto je doporučený režim pro upgrady aplikací v provozním prostředí. |
Upgrade režimu | sada VS | Povolené hodnoty jsou **monitorovány** (výchozí), **UnmonitoredAuto**nebo **UnmonitoredManual**. Podrobnosti najdete v tématu parametry prostředí PowerShell pro každý režim v tomto článku. |
NemonitorovanýAuto | PS | Označuje, že režim upgradu je nemonitorovaný automaticky. Po service fabric upgradu je upgrade domény upgradu, Service Fabric upgraduje další upgrade domény bez ohledu na stav aplikace. Tento režim se nedoporučuje pro výrobu a je užitečné pouze při vývoji aplikace. |
Nemonitorovaná příručka | PS | Označuje, že režim upgradu je nemonitorovaný manuál. Poté, co service fabric upgraduje inovaci, čeká na upgrade další upgradovací domény pomocí rutiny *Resume-ServiceFabricApplicationUpgrade.* |

### <a name="optional-parameters"></a>Volitelné parametry

Parametry hodnocení stavu jsou volitelné. Pokud nejsou při spuštění upgradu zadána kritéria hodnocení stavu, service fabric používá zásady stavu aplikace zadané v applicationManifest.xml instance aplikace.

> [!div class="mx-tdBreakAll"]
> | Parametr | Platí pro | Popis |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Určuje přepsání parametrů aplikace.<br>Parametry aplikace PowerShell jsou určeny jako dvojice hodnothodnot hashtable name/value. Například @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Parametry aplikace sady Visual Studio lze zadat v dialogovém okně Publikovat aplikaci aplikace Service Fabric v poli **Soubor parametrů aplikace.**
> | Confirm |PS| Povolené hodnoty jsou **True** a **False**. Před spuštěním rutiny zobrazí výzvu k potvrzení. |
> | Zvážit WarningAsChyba |PS, VS |Povolené hodnoty jsou **True** a **False**. Výchozí je hodnota **False**. Považovat události stavu upozornění pro aplikaci jako chyby při vyhodnocování stavu aplikace během upgradu. Ve výchozím nastavení Service Fabric nevyhodnocuje události stavu upozornění jako selhání (chyby), takže upgrade může pokračovat i v případě, že existují události upozornění. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Určuje zásady stavu pro výchozí typ služby, který se má použít pro sledovaný upgrade ve formátu MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Například 5,10,15 označuje následující hodnoty: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Povolené hodnoty jsou **True** a **False**. Označuje, že proces upgradu přeskočí varovnou zprávu a vynutí upgrade i v případě, že se číslo verze nezměnilo. To je užitečné pro místní testování, ale nedoporučuje se pro použití v produkčním prostředí, protože vyžaduje odebrání existujícího nasazení, které způsobuje prostoje a potenciální ztrátu dat. |
> | Aplikace ForceRestart |PS, VS |Pokud aktualizujete konfiguraci nebo datový balíček bez aktualizace kódu služby, bude služba restartována pouze v případě, že je vlastnost ForceRestart nastavena na **hodnotu True**. Po dokončení aktualizace Service Fabric upozorní službu, že nový konfigurační balíček nebo datový balíček je k dispozici. Služba je zodpovědná za použití změn. V případě potřeby se služba může restartovat sama. |
> | Kontrola stavuRetryTimeoutSec |PS, VS |Doba trvání (v sekundách), kterou service fabric pokračuje v hodnocení stavu před deklarováním upgradu jako neúspěšného. Výchozí hodnota je 600 sekund. Tato doba trvání začíná po *dosažení HealthCheckWaitDurationSec.* V rámci tohoto *HealthCheckRetryTimeout*, Service Fabric může provádět více kontrol stavu stavu aplikace. Výchozí hodnota je 10 minut a měla by být přizpůsobena odpovídajícím způsobem pro vaši aplikaci. |
> | HealthCheckStableDurationSec |PS, VS |Doba trvání (v sekundách) k ověření, že aplikace je stabilní před přechodem na další doménu upgradu nebo dokončení upgradu. Tato doba čekání se používá k zabránění nezjištěných změn stavu ihned po provedení kontroly stavu. Výchozí hodnota je 120 sekund a měla by být odpovídajícím způsobem přizpůsobena pro vaši aplikaci. |
> | StavCheckWaitDurationSec |PS, VS | Doba čekání (v sekundách) po dokončení upgradu v doméně upgradu před Service Fabric vyhodnotí stav aplikace. Toto trvání lze také považovat za čas, kdy by měla být aplikace spuštěna, než může být považována za v pořádku. Pokud kontrola stavu projde, proces upgradu pokračuje do další domény upgradu.  Pokud kontrola stavu selže, Service Fabric čeká [upgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) před opakováním kontroly stavu znovu, dokud *healthCheckRetryTimeoutSec* je dosaženo. Výchozí a doporučená hodnota je 0 sekund. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (viz [část Stav),](service-fabric-health-introduction.md)které mohou být v pořádku, než bude aplikace považována za nefunkční a upgrade se nezdaří. Tento parametr definuje stav aplikace v uzlu a pomáhá detekovat problémy během upgradu. Repliky aplikace obvykle získat zatížení vyvážené do druhého uzlu, který umožňuje aplikaci zobrazit v pořádku, což umožňuje pokračovat v upgradu. Zadáním přísného stavu *MaxPercentUnhealthyDeployedApplications* service fabric může rychle zjistit problém s balíčkem aplikace a pomoci vytvořit rychlý upgrade selhání. |
> | MaxPercentUnhealthyServices |PS, VS |Parametr *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet služeb v instanci aplikace, které mohou být v pořádku, než je aplikace považována za nefunkční a neprovede upgrade. |
> | MaxPercentUnHealthyPartitionsPerService|PS, VS |Parametr *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet oddílů ve službě, které mohou být v pořádku, než je služba považována za nefunkční. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Parametr *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet replik v oddílu, který může být v pořádku, než je oddíl považován za nefunkční. |
> | ServiceTypeHealthPolicyMap | PS, VS | Představuje zásady stavu používané k vyhodnocení stavu služeb, které patří k typu služby. Přebírá vstup tabulky hash v následujícím formátu: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Například: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | Časový čas | PS, VS | Určuje časový limit v sekundách operace. |
> | UpgradeDomainTimeoutSec |PS, VS |Maximální doba (v sekundách) pro upgrade jedné domény upgradu. Pokud je dosaženo tohoto časového omezení, upgrade se zastaví a pokračuje na základě nastavení *pro FailureAction*. Výchozí hodnota je nikdy (Infinite) a měla by být přizpůsobena odpovídajícím způsobem pro vaši aplikaci. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Měřeno v sekundách.<br>**Bezstavová služba**-- V rámci jedné domény upgradu service fabric se pokusí zajistit, že další instance služby jsou k dispozici. Pokud počet cílových instancí je více než jeden, Service Fabric čeká na více než jednu instanci k dispozici, až do maximální hodnoty časového limitu. Tento časový režim je určen pomocí *vlastnosti UpgradeReplicaSetSetTimeoutSec.* Pokud vyprší časový limit, Service Fabric pokračuje s upgradem, bez ohledu na počet instancí služby. Pokud počet cílových instancí je jeden, Service Fabric nečeká a okamžitě pokračuje s upgradem.<br><br>**Stavová služba**-- V rámci jedné domény upgradu service fabric se pokusí zajistit, že sada replik má kvorum. Service Fabric čeká na kvorum k dispozici, až do maximální hodnoty časového limitu (určené *UpgradeReplicaSetCheckTimeoutSec* vlastnost). Pokud vyprší časový limit, Service Fabric pokračuje s upgradem, bez ohledu na kvorum. Toto nastavení je nastaveno jako nikdy (nekonečné) při převrácení vpřed a 1200 sekund při vrácení zpět. |
> | UpgradeTimeoutSec |PS, VS |Časový čas (v sekundách), který platí pro celý upgrade. Pokud je dosaženo tohoto časového očase, upgrade se zastaví a spustí se *akce FailureAction.* Výchozí hodnota je nikdy (Infinite) a měla by být přizpůsobena odpovídajícím způsobem pro vaši aplikaci. |
> | WhatIf | PS | Povolené hodnoty jsou **True** a **False**. Zobrazuje, co by se stalo při spuštění rutiny. Rutina není spuštěna. |

Kritéria *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*a *MaxPercentUnhealthyReplicasPerPartition* lze zadat pro typ služby pro instanci aplikace. Nastavení těchto parametrů na službu umožňuje aplikaci obsahovat různé typy služeb s různými zásadami hodnocení. Například bezstavový typ služby brány může mít *MaxPercentUnhealthyPartitionsPerService,* který se liší od stavového typu služby motoru pro konkrétní instanci aplikace.

## <a name="sfctl-parameters"></a>Parametry SFCTL

Upgrady aplikace Service Fabric pomocí rozhraní PŘÍKAZOVÉHO ŘÁDKU Service Fabric používají příkaz [upgradu aplikace sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) spolu s následujícími požadovanými a volitelnými parametry.

### <a name="required-parameters"></a>Požadované parametry

| Parametr | Popis |
| --- | --- |
| id aplikace  |ID aplikace, která je právě upgradována. <br> Obvykle se jedná o úplný název aplikace bez schématu IDENTIFIKÁTORURI .) Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " . Například pokud název aplikace je 'fabric:/myapp/app1', identita aplikace\~by 'myapp app1' v 6.0+ a 'myapp/app1' v předchozích verzích.|
verze aplikace |Verze typu aplikace, na kterou se upgrade zaměřuje.|
parameters  |JSON kódovaný seznam parametrů aplikace přepíše, které mají být použity při upgradu aplikace.|

### <a name="optional-parameters"></a>Volitelné parametry

| Parametr | Popis |
| --- | --- |
výchozí politika služby a stavu | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) kódované specifikace zásady stavu používá ve výchozím nastavení k vyhodnocení stavu typu služby. Mapa je ve výchozím nastavení prázdná. |
selhání | Povolené hodnoty jsou **Vrácení zpět**, **Ruční**a **Neplatné**. Kompenzační akce, která má být vpřípadě *sledovaného* upgradu, narazí na zásady monitorování nebo porušení zásad stavu. <br>**Vrácení zpět** určuje, že upgrade se automaticky vrátí zpět na verzi před upgradem. <br>**Příručka** označuje, že upgrade se přepne do režimu upgradu *UnmonitoredManual.* <br>**Neplatný** označuje, že akce selhání je neplatná.|
restart síly | Pokud aktualizujete konfiguraci nebo datový balíček bez aktualizace kódu služby, bude služba restartována pouze v případě, že je vlastnost ForceRestart nastavena na **hodnotu True**. Po dokončení aktualizace Service Fabric upozorní službu, že nový konfigurační balíček nebo datový balíček je k dispozici. Služba je zodpovědná za použití změn. V případě potřeby se služba může restartovat sama. |
časový čas kontroly stavu | Doba, po kterou je třeba opakovat vyhodnocení stavu, pokud není aplikace nebo cluster v pořádku, než je akce *Selhání* provedena. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. Výchozí: PT0H10M0S. |
stabilní doba kontroly stavu | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než upgrade přejde na další doménu upgradu. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. Výchozí: PT0H2M0S. |
doba trvání kontroly stavu | Doba čekání po dokončení domény upgradu před použitím zásad stavu. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. Výchozí nastavení: 0.|
max-nezdravé aplikace | Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (viz [část Stav),](service-fabric-health-introduction.md)které mohou být v pořádku, než bude aplikace považována za nefunkční a upgrade se nezdaří. Tento parametr definuje stav aplikace v uzlu a pomáhá detekovat problémy během upgradu. Repliky aplikace obvykle získat zatížení vyvážené do druhého uzlu, který umožňuje aplikaci zobrazit v pořádku, což umožňuje pokračovat v upgradu. Zadáním přísného stavu *max-unzdravé aplikace* service fabric můžete rychle zjistit problém s balíčkem aplikace a pomoci vytvořit selhání rychlé houpgradu. Reprezentováno jako číslo mezi 0 a 100. |
režim | Povolené hodnoty jsou **monitorovány**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Výchozí hodnota je **UnmonitoredAuto**. Popis těchto hodnot najdete v části Visual Studio a Požadované *parametry* prostředí PowerShell.|
časový čas sady replik |Měřeno v sekundách. <br>**Bezstavová služba**-- V rámci jedné domény upgradu service fabric se pokusí zajistit, že další instance služby jsou k dispozici. Pokud počet cílových instancí je více než jeden, Service Fabric čeká na více než jednu instanci k dispozici, až do maximální hodnoty časového limitu. Tento časový opovace je určen pomocí *repliky set-check-time* vlastnost. Pokud vyprší časový limit, Service Fabric pokračuje s upgradem, bez ohledu na počet instancí služby. Pokud počet cílových instancí je jeden, Service Fabric nečeká a okamžitě pokračuje s upgradem.<br><br>**Stavová služba**-- V rámci jedné domény upgradu service fabric se pokusí zajistit, že sada replik má kvorum. Service Fabric čeká na kvorum k dispozici, až do maximální hodnoty časového limitu (určené *vlastností replika set-check-out).* Pokud vyprší časový limit, Service Fabric pokračuje s upgradem, bez ohledu na kvorum. Toto nastavení je nastaveno jako nikdy (nekonečné) při převrácení vpřed a 1200 sekund při vrácení zpět. |
politika poskytování služeb a zdraví | JSON kódované mapy s politikou stavu typu služby podle názvu typu služby. Mapa je prázdná být výchozí. [Parametr JSON formát.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). JSON pro část "Hodnota" obsahuje **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**a **MaxPercentUnhealthyReplicasPerPartition**. Popis těchto parametrů najdete v části Visual Studio a Volitelné parametry prostředí PowerShell.
timeout | Určuje časový limit v sekundách operace. Výchozí hodnota: 60. |
časový plán upgradu domény | Doba, po kterou musí být každá upgradovací doména dokončena před provedením *akce FailureAction.* Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. Výchozí hodnota je nikdy (Infinite) a měla by být přizpůsobena odpovídajícím způsobem pro vaši aplikaci. Výchozí hodnota: P10675199DT02H48M05.4775807s. |
časový plán upgradu | Doba, po kterou musí být každá upgradovací doména dokončena před provedením *akce FailureAction.* Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. Výchozí hodnota je nikdy (Infinite) a měla by být přizpůsobena odpovídajícím způsobem pro vaši aplikaci. Výchozí hodnota: P10675199DT02H48M05.4775807s.|
upozornění jako chyba | Povolené hodnoty jsou **True** a **False**. Výchozí je hodnota **False**. Může být předán jako vlajka. Považovat události stavu upozornění pro aplikaci jako chyby při vyhodnocování stavu aplikace během upgradu. Ve výchozím nastavení Service Fabric nevyhodnocuje události stavu upozornění jako selhání (chyby), takže upgrade může pokračovat i v případě, že existují události upozornění. |

## <a name="next-steps"></a>Další kroky
[Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

[Upgrade aplikace pomocí služby Fabric CLI na Linuxu](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vás provede upgrade aplikace pomocí service fabric CLI.

[Upgrade aplikace pomocí service fabric eclipse plugin](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Pomocí aplikace můžete upgrady aplikací provést tak, že se naučíte používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [upřesnit témata](service-fabric-application-upgrade-advanced.md).

Běžné problémy při upgradu aplikací opravíte odkazem na kroky při [řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).
