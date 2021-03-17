---
title: 'Upgrade aplikace: parametry upgradu'
description: Popisuje parametry týkající se upgradu aplikace Service Fabric, včetně kontrol stavu, které se mají provést, a zásady, které automaticky zruší upgrade.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018473"
---
# <a name="application-upgrade-parameters"></a>Parametry upgradu aplikace
Tento článek popisuje různé parametry, které se použijí při upgradu aplikace Service Fabric Azure. Parametry upgradu aplikace řídí časové limity a kontroly stavu, které se použijí během upgradu, a určují zásady, které se musí použít, když se upgrade nepovede. Parametry aplikace se vztahují na upgrady pomocí:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Upgrady aplikací se zahajují prostřednictvím jednoho ze tří režimů upgradu, které lze vybrat uživatelem. Každý režim má svou vlastní sadu parametrů aplikace:
- Monitor
- Nemonitorované automatické
- Nemonitorovaná ruční

Příslušné povinné a volitelné parametry jsou popsány v jednotlivých částech následujícím způsobem.

## <a name="visual-studio-and-powershell-parameters"></a>Parametry sady Visual Studio a prostředí PowerShell

Service Fabric upgrady aplikací pomocí prostředí PowerShell použijte příkaz [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) . Režim upgradu je vybrán předáním parametru **Monitored**, **UnmonitoredAuto** nebo **UnmonitoredManual** na hodnotu [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Parametry upgradu aplikace Visual Studio Service Fabric se nastavují prostřednictvím dialogu Nastavení upgradu sady Visual Studio. Režim upgradu sady Visual Studio je vybrán v rozevíracím seznamu **režim upgradu** na hodnotu **monitorovat**, **UnmonitoredAuto** nebo **UnmonitoredManual**. Další informace najdete v tématu [Konfigurace upgradu aplikace Service Fabric v aplikaci Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Požadované parametry
(PS = PowerShell, VS = Visual Studio)

| Parametr | Platí pro | Description |
| --- | --- | --- |
ApplicationName |PS| Název aplikace, která se upgraduje Příklady: Fabric:/VisualObjects, Fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Verze typu aplikace, na kterou se upgrade zaměřuje |
FailureAction |PS, VS|Povolené hodnoty jsou **vrácení**, **Ruční** a **neplatné**. Kompenzační akce, která se má provést, když *monitorovaný* upgrade narazí na zásady monitorování nebo porušení zásad stavu. <br>**Vrácení zpět** určuje, že se upgrade automaticky vrátí zpět na verzi předběžného upgradu. <br>**Ručně** indikuje, že se upgrade přepne do režimu upgradu *UnmonitoredManual* . <br>**Neplatný** označuje, že akce selhání není platná.|
Monitor |PS|Indikuje, že je monitorovaný režim upgradu. Když rutina dokončí upgrade pro upgradovací doménu, pokud stav domény upgradu a clusteru splňuje zásady stavu, které definujete, Service Fabric upgraduje další upgradovací doménu. Pokud upgradovací doména nebo cluster nesplňuje zásady stavu, upgrade se nezdařil a Service Fabric vrátí zpět upgrade pro upgradovací doménu nebo se vrátí k ručnímu režimu na základě zadané zásady. Toto je doporučený režim pro upgrady aplikací v produkčním prostředí. |
UpgradeMode | sada VS | Povolené hodnoty se **monitorují** (výchozí), **UnmonitoredAuto** nebo **UnmonitoredManual**. Podrobnosti najdete v tématu parametry PowerShellu pro každý režim v tomto článku. |
UnmonitoredAuto | PS | Indikuje, že režim upgradu není monitorovaný automaticky. Po Service Fabric upgraduje upgradovací doménu Service Fabric upgraduje další upgradovací doménu bez ohledu na stav aplikace. Tento režim není doporučen pro produkční prostředí a je užitečný pouze během vývoje aplikace. |
UnmonitoredManual | PS | Indikuje, že režim upgradu není monitorovaný ručně. Po Service Fabric upgraduje upgradovací doménu, čeká na upgrade další upgradovací domény pomocí rutiny *Resume-ServiceFabricApplicationUpgrade* . |

### <a name="optional-parameters"></a>Volitelné parametry

Parametry vyhodnocení stavu jsou volitelné. Pokud kritéria hodnocení stavu nejsou určena při spuštění upgradu, Service Fabric používá zásady stavu aplikace zadané v ApplicationManifest.xml instance aplikace.

> [!div class="mx-tdBreakAll"]
> | Parametr | Platí pro | Description |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Určuje přepsání parametrů aplikace.<br>Parametry aplikace PowerShell jsou zadány jako páry název/hodnota zatřiďovací tabulky. Například @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Parametry aplikace Visual Studio lze zadat v dialogovém okně Publikovat Service Fabric aplikaci v poli **soubor parametrů aplikace** .
> | Confirm |PS| Povolené hodnoty jsou **true** a **false**. Před spuštěním rutiny zobrazí výzvu k potvrzení. |
> | ConsiderWarningAsError |PS, VS |Povolené hodnoty jsou **true** a **false**. Výchozí hodnota je **false**. Považovat události stavu varování za chyby při vyhodnocování stavu aplikace během upgradu. Ve výchozím nastavení Service Fabric nevyhodnocují události stavu upozornění na chyby (chyby), takže upgrade může pokračovat i v případě, že dojde k událostem upozornění. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Určuje zásady stavu pro výchozí typ služby, který se má použít pro monitorovaný upgrade ve formátu MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Například 5, 10, 15 označuje následující hodnoty: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Povolené hodnoty jsou **true** a **false**. Indikuje, že proces upgradu přeskočí zprávu upozornění a vynutí upgrade i v případě, že se číslo verze nezměnilo. To je užitečné pro místní testování, ale nedoporučujeme je používat v produkčním prostředí, protože vyžaduje odebrání stávajícího nasazení, které způsobuje výpadky a potenciální ztrátu dat. |
> | ForceRestart |PS, VS |Pokud aktualizujete konfigurační nebo datový balíček bez aktualizace kódu služby, služba se restartuje pouze v případě, že je vlastnost ForceRestart nastavena na **hodnotu true**. Po dokončení aktualizace Service Fabric upozorní službu, že je k dispozici nový konfigurační balíček nebo datový balíček. Služba je zodpovědná za použití změn. V případě potřeby se služba může sám restartovat. |
> | HealthCheckRetryTimeoutSec |PS, VS |Doba (v sekundách), po kterou Service Fabric nadále provádět vyhodnocení stavu před tím, než se upgrade deklaruje jako neúspěšný. Výchozí hodnota je 600 sekund. Toto trvání začíná po dosažení *HealthCheckWaitDurationSec* . V rámci tohoto *HealthCheckRetryTimeout* mohou Service Fabric provádět více kontrol stavu aplikace. Výchozí hodnota je 10 minut a měla by být vhodně přizpůsobená pro vaši aplikaci. |
> | HealthCheckStableDurationSec |PS, VS |Doba (v sekundách), po kterou se před přechodem k další upgradovací doméně nebo dokončení upgradu ověří, že je aplikace stabilní. Tato čekací doba se používá k tomu, aby se zabránilo nezjištěné změně stavu hned po provedení kontroly stavu. Výchozí hodnota je 120 sekund a měla by být upravena odpovídajícím způsobem pro vaši aplikaci. |
> | HealthCheckWaitDurationSec |PS, VS | Doba čekání (v sekundách) po dokončení upgradu v upgradovací doméně, než Service Fabric vyhodnotí stav aplikace. Toto trvání je možné také zvážit v době, kdy by měla aplikace běžet, než bude možné ji považovat za v pořádku. Pokud kontrolu stavu projde, pokračuje proces upgradu na další upgradovací doménu.  Pokud se ověření stavu nepovede, Service Fabric počká na [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) , než se znovu pokusí o kontrolu stavu, dokud nedosáhne *HealthCheckRetryTimeoutSec* . Výchozí a doporučená hodnota je 0 sekund. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (viz [oddíl stav](service-fabric-health-introduction.md)), který může být špatný, než se aplikace považuje za poškozenou a upgrade se nezdařil. Tento parametr definuje stav aplikace na uzlu a pomáhá detekovat problémy během upgradu. Obvykle repliky aplikace získávají vyrovnávání zatížení v jiném uzlu, což umožňuje, aby se aplikace zobrazovala v dobrém stavu, což umožní pokračovat v upgradu. Když zadáte striktní *MaxPercentUnhealthyDeployedApplications* stav, Service Fabric může rychle zjistit problém s balíčkem aplikace a pomůže vám to rychle se pokusit o rychlý upgrade. |
> | MaxPercentUnhealthyServices |PS, VS |Parametr pro *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet služeb v instanci aplikace, které mohou být poškozeny, než je aplikace považována za poškozenou a upgrade se nezdařil. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Parametr pro *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet oddílů ve službě, které mohou být chybné, než se služba považuje za poškozenou. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Parametr pro *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet replik v oddílu, který může být chybný, než se oddíl považuje za špatný. |
> | ServiceTypeHealthPolicyMap | PS, VS | Představuje zásady stavu používané k vyhodnocení stavu služeb, které patří k typu služby. Převezme zadání zatřiďovací tabulky v následujícím formátu: @ {"ServiceType": "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices"} například: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | Určuje časový limit pro operaci v sekundách. |
> | UpgradeDomainTimeoutSec |PS, VS |Maximální doba (v sekundách) pro upgrade jedné upgradovací domény. Pokud je tento časový limit dosažený, upgrade se zastaví a bude pokračovat na základě nastavení pro *FailureAction*. Výchozí hodnota není nikdy (Infinitá) a měla by být vhodně přizpůsobená pro vaši aplikaci. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Měří se v sekundách.<br>**Bezstavová služba**– v rámci jedné upgradovací domény Service Fabric se pokusí zajistit, aby byly k dispozici další instance služby. Pokud je počet cílových instancí více než jeden, Service Fabric čeká, až bude k dispozici více než jedna instance, až do maximální hodnoty časového limitu. Tento časový limit je určen pomocí vlastnosti *UpgradeReplicaSetCheckTimeoutSec* . Pokud časový limit vyprší, Service Fabric pokračuje s upgradem bez ohledu na počet instancí služby. Pokud je počet instancí cíle jeden, Service Fabric nečeká a okamžitě pokračuje v upgradu.<br><br>**Stavová služba**– v rámci jedné upgradovací domény Service Fabric se pokusí zajistit, že sada replik má kvorum. Service Fabric čeká, až bude k dispozici kvorum, až do maximální hodnoty časového limitu (určené vlastností *UpgradeReplicaSetCheckTimeoutSec* ). Pokud časový limit vyprší, Service Fabric pokračuje s upgradem bez ohledu na kvorum. Toto nastavení se nastaví jako nikdy (nekonečné) při vracení po obnovení a 1200 sekund při vracení zpět. |
> | UpgradeTimeoutSec |PS, VS |Časový limit (v sekundách), který se použije pro celý upgrade. Pokud je tento časový limit dostupný, upgrade se zastaví a *FailureAction* se aktivuje. Výchozí hodnota není nikdy (Infinitá) a měla by být vhodně přizpůsobená pro vaši aplikaci. |
> | WhatIf | PS | Povolené hodnoty jsou **true** a **false**. Zobrazuje, co by se stalo při spuštění rutiny. Rutina není spuštěna. |

Kritéria *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* a *MaxPercentUnhealthyReplicasPerPartition* lze zadat na typ služby pro instanci aplikace. Nastavením těchto parametrů na službu umožníte, aby aplikace obsahovala různé typy služeb s různými zásadami hodnocení. Například typ služby brány bez stavu může mít *MaxPercentUnhealthyPartitionsPerService* , který se liší od typu služby stavového stroje pro určitou instanci aplikace.

## <a name="sfctl-parameters"></a>Parametry SFCTL

Service Fabric upgrady aplikací pomocí rozhraní příkazového řádku Service Fabric použijte příkaz pro [Upgrade aplikace sfctl](./service-fabric-sfctl-application.md#sfctl-application-upgrade) spolu s následujícími povinnými a nepovinnými parametry.

### <a name="required-parameters"></a>Požadované parametry

| Parametr | Popis |
| --- | --- |
| ID aplikace  |ID aplikace, která se upgraduje <br> Většinou se jedná o úplný název aplikace bez schématu URI Fabric:. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem. Například pokud je název aplikace "Fabric:/MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích.|
verze aplikace |Verze typu aplikace, na kterou se upgrade zaměřuje|
parameters  |Seznam přepsání parametrů aplikace, který se má použít při upgradu aplikace, je zakódovaný do formátu JSON.|

### <a name="optional-parameters"></a>Volitelné parametry

| Parametr | Popis |
| --- | --- |
výchozí nastavení – zásada služby | Specifikace formátu [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) pro zásady stavu, která se používá ve výchozím nastavení, aby vyhodnotila stav typu služby. Mapa je ve výchozím nastavení prázdná. |
Chyba – akce | Povolené hodnoty jsou **vrácení**, **Ruční** a **neplatné**. Kompenzační akce, která se má provést, když *monitorovaný* upgrade narazí na zásady monitorování nebo porušení zásad stavu. <br>**Vrácení zpět** určuje, že se upgrade automaticky vrátí zpět na verzi předběžného upgradu. <br>**Ručně** indikuje, že se upgrade přepne do režimu upgradu *UnmonitoredManual* . <br>**Neplatný** označuje, že akce selhání není platná.|
vynutit – restartovat | Pokud aktualizujete konfigurační nebo datový balíček bez aktualizace kódu služby, služba se restartuje pouze v případě, že je vlastnost ForceRestart nastavena na **hodnotu true**. Po dokončení aktualizace Service Fabric upozorní službu, že je k dispozici nový konfigurační balíček nebo datový balíček. Služba je zodpovědná za použití změn. V případě potřeby se služba může sám restartovat. |
stav-check-opakování – časový limit | Doba, po kterou se má opakovat vyhodnocení stavu, když je aplikace nebo cluster v pořádku, než se spustí *FailureAction* Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. Výchozí: PT0H10M0S. |
stav-check-stabilní – doba trvání | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než bude upgrade pokračovat na další upgradovací doménu. Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. Výchozí: PT0H2M0S. |
stav-check-čekací doba | Doba, po kterou se má po dokončení domény upgradu počkat, než se použijí zásady stavu Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. Výchozí hodnota: 0.|
Max – není v pořádku – aplikace | Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (viz [oddíl stav](service-fabric-health-introduction.md)), který může být špatný, než se aplikace považuje za poškozenou a upgrade se nezdařil. Tento parametr definuje stav aplikace na uzlu a pomáhá detekovat problémy během upgradu. Obvykle repliky aplikace získávají vyrovnávání zatížení v jiném uzlu, což umožňuje, aby se aplikace zobrazovala v dobrém stavu, což umožní pokračovat v upgradu. Když zadáte striktní stav, který je *v pořádku,* Service Fabric může rychle zjistit problém s balíčkem aplikace a pomůže vám vytvořit rychlý upgrade při selhání. Reprezentované jako číslo mezi 0 a 100. |
režim | Povolené hodnoty jsou **monitorované**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Výchozí hodnota je **UnmonitoredAuto**. Popisy těchto hodnot najdete v části věnované *parametrům* pro Visual Studio a PowerShell.|
replika-set-check-timeout |Měří se v sekundách. <br>**Bezstavová služba**– v rámci jedné upgradovací domény Service Fabric se pokusí zajistit, aby byly k dispozici další instance služby. Pokud je počet cílových instancí více než jeden, Service Fabric čeká, až bude k dispozici více než jedna instance, až do maximální hodnoty časového limitu. Tento časový limit je zadaný pomocí vlastnosti *replika-set-check-timeout* . Pokud časový limit vyprší, Service Fabric pokračuje s upgradem bez ohledu na počet instancí služby. Pokud je počet instancí cíle jeden, Service Fabric nečeká a okamžitě pokračuje v upgradu.<br><br>**Stavová služba**– v rámci jedné upgradovací domény Service Fabric se pokusí zajistit, že sada replik má kvorum. Service Fabric čeká, až bude k dispozici kvorum, až do maximální hodnoty časového limitu (určené vlastností *replika-set-check-timeout* ). Pokud časový limit vyprší, Service Fabric pokračuje s upgradem bez ohledu na kvorum. Toto nastavení se nastaví jako nikdy (nekonečné) při vracení po obnovení a 1200 sekund při vracení zpět. |
Služba – stav – zásady | Mapa zakódovaná pomocí formátu JSON s typem služby zásady stavu na název typu služby Mapa je prázdná jako výchozí. [Formát JSON parametru.](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). KÓD JSON pro část "hodnota" obsahuje **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** a **MaxPercentUnhealthyReplicasPerPartition**. Popisy těchto parametrů najdete v části věnované volitelným parametrům sady Visual Studio a PowerShellu.
timeout | Určuje časový limit pro operaci v sekundách. Výchozí: 60. |
upgrade-doména – časový limit | Doba, po kterou musí být každá upgradovací doména dokončena před provedením *FailureAction* . Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. Výchozí hodnota není nikdy (Infinitá) a měla by být vhodně přizpůsobená pro vaši aplikaci. Výchozí: P10675199DT02H48M 05.4775807 S. |
upgrade – časový limit | Doba, po kterou musí být každá upgradovací doména dokončena před provedením *FailureAction* . Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. Výchozí hodnota není nikdy (Infinitá) a měla by být vhodně přizpůsobená pro vaši aplikaci. Výchozí: P10675199DT02H48M 05.4775807 S.|
upozornění – jako chyba | Povolené hodnoty jsou **true** a **false**. Výchozí hodnota je **false**. Může být předán jako příznak. Považovat události stavu varování za chyby při vyhodnocování stavu aplikace během upgradu. Ve výchozím nastavení Service Fabric nevyhodnocují události stavu upozornění na chyby (chyby), takže upgrade může pokračovat i v případě, že dojde k událostem upozornění. |

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

[Upgrade aplikace pomocí Service Fabric CLI v systému Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vás provede upgradem aplikace pomocí Service Fabric CLI.

[Upgrade aplikace pomocí modulu plug-in Service Fabric zatmění](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.
