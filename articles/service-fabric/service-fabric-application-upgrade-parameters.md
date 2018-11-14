---
title: 'Upgrade aplikací: parametry upgradu | Dokumentace Microsoftu'
description: Popisuje parametry související s upgrade aplikace Service Fabric, včetně kontroly stavu k provedení a zásady, které automaticky vrácení upgradu zpět.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: subramar
ms.openlocfilehash: 71e7abef725abf95cc20de8d1283d0efea6c3687
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615853"
---
# <a name="application-upgrade-parameters"></a>Parametry upgradu aplikace
Tento článek popisuje různé parametry, které se použijí při upgradu aplikace Azure Service Fabric. Parametry upgradu aplikace řídit vypršení časových limitů a kontroly stavu, které se použijí při upgradu a určují zásady, které se musí použít při upgradu se nezdaří. Parametry aplikace použít k upgradu pomocí:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Upgrady aplikací jsou spuštěných prostřednictvím jeden ze tří režimů uživatelsky volitelných upgradu. Oba režimy má svou vlastní sadu parametrů aplikace:
- Monitorováno
- Nemonitorované automaticky
- Nemonitorované ruční

Použít povinných a volitelných parametrů jsou popsány v každé části následujícím způsobem.

## <a name="visual-studio-and-powershell-parameters"></a>Parametry sady Visual Studio a prostředí PowerShell

Upgrady aplikací Service Fabric pomocí Powershellu [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) příkazu. Režim upgradu je vybraná předáním buď **monitorované**, **UnmonitoredAuto**, nebo **UnmonitoredManual** parametr [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Visual Studio Service Fabric application upgradu parametry jsou nastavené přes dialogové okno nastavení Upgrade Visual Studio. Režim upgradu sady Visual Studio je vybraná pomocí **režimu upgradu** pole rozevíracího seznamu buď **monitorované**, **UnmonitoredAuto**, nebo **UnmonitoredManual** . Další informace najdete v tématu [konfigurace upgradu aplikace Service Fabric v sadě Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Požadované parametry
(PS = prostředí PowerShell, VS = sady Visual Studio)

| Parametr | Platí pro | Popis |
| --- | --- | --- |
ApplicationName |PS| Název aplikace, která se upgraduje. Příklady: fabric: / VisualObjects fabric: / ClusterMonitor. |
ApplicationTypeVersion|PS|Typ verze aplikace, aby upgradu cíle. |
FailureAction |PS VS|Povolené hodnoty jsou **vrácení zpět**, **ruční**, a **neplatný**. Kompenzační akce, která má provést při *monitorované* upgradu zjistí monitorování zásad nebo stavu porušení zásad. <br>**Vrácení zpět** Určuje, že upgrade bude automaticky vrátit k verzi před upgradem. <br>**Ruční** označuje, že upgrade přepne *UnmonitoredManual* režim upgradu. <br>**Neplatný** označuje, že selhání akce je neplatná.|
Monitorováno |PS|Určuje, zda je monitorovaný režim upgradu. Až rutina dokončí upgrade upgradovací doméně, pokud stav upgradu domény a clusteru splňují zásady stavu, které definujete, Service Fabric upgraduje další upgradovací doméně. Pokud doména upgradu nebo clusteru nesplňuje zásady stavu, aktualizace se nezdaří a Service Fabric vrátí zpět upgrade upgradovací domény, nebo se vrátí do režimu ručního za určené zásady. Toto je doporučený režim pro upgrady aplikací v produkčním prostředí. |
UpgradeMode | VS | Povolené hodnoty jsou **monitorované** (výchozí), **UnmonitoredAuto**, nebo **UnmonitoredManual**. Zobrazit parametry prostředí PowerShell pro oba režimy v tomto článku najdete podrobnosti. |
UnmonitoredAuto | PS | Označuje, že je režim upgradu nemonitorované automaticky. Po Service Fabric se upgraduje jednu upgradovací doménu, Service Fabric se upgraduje další upgradovací doméně bez ohledu na stav aplikace. Tento režim se nedoporučuje pro produkční prostředí a je užitečná pouze během vývoje aplikace. |
UnmonitoredManual | PS | Označuje, že je režim upgradu nemonitorované ručně. Po Service Fabric se upgraduje jednu upgradovací doménu, čeká můžete upgradovat další upgradovací doméně pomocí *Resume-ServiceFabricApplicationUpgrade* rutiny. |

### <a name="optional-parameters"></a>Volitelné parametry

Parametry hodnocení stavu jsou volitelné. Pokud kritéria hodnocení stavu nejsou zadané při spuštění upgradu, Service Fabric používá zásady stavu aplikace zadaný v souboru ApplicationManifest.xml instance aplikace.

Použijte vodorovný posuvník v dolní části tabulky zobrazíte úplný popis pole.

(PS = prostředí PowerShell, VS = sady Visual Studio)

| Parametr | Platí pro | Popis |
| --- | --- | --- |
| ApplicationParameter |PS VS| Určuje přepsání pro parametry aplikace.<br>Parametry applcation Powershellu jsou zadané jako dvojice název/hodnota zatřiďovací tabulku. Například @{"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>V dialogovém okně publikovat aplikaci Service Fabric v lze zadat parametry aplikace Visual Studio **soubor parametrů aplikace** pole.
| Potvrdit |PS| Povolené hodnoty jsou **True** a **False**. Výzvy k potvrzení před spuštěním rutiny. |
| ConsiderWarningAsError |PS VS |Povolené hodnoty jsou **True** a **False**. Výchozí je hodnota **False**. Zpracovávat události upozornění stavu aplikace jako chyby při vyhodnocování stavu aplikace v průběhu upgradu. Ve výchozím nastavení Service Fabric není vyhodnocen události stavu upozornění jako chyby (chyby), takže upgradu můžete pokračovat i v případě, že jsou události upozornění. |
| DefaultServiceTypeHealthPolicy | PS VS |Určuje zásady stavu pro monitorované inovace ve formátu MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices typu výchozí. Například 5,10,15 označuje následující hodnoty: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10 MaxPercentUnhealthyServices = 15. |
| Platnost | PS VS | Povolené hodnoty jsou **True** a **False**. Označuje, že proces upgradu přeskočí zprávu upozornění a vynutí upgrade i v případě, že nedošlo ke změně číslo verze. To je užitečné pro místní testování ale se nedoporučuje pro použití v produkčním prostředí, protože vyžaduje odebrání stávajícího nasazení, což způsobí, že výpadkům a potenciálním únikem informací. |
| ForceRestart |PS VS |Při aktualizaci konfigurace nebo balíček dat bez aktualizace kódu služby, pouze v případě, ForceRestart je nastavena na restartování služby **True**. Po dokončení aktualizace se Service Fabric upozorní službu nový balíček pro konfiguraci nebo balíček dat je k dispozici. Služba je zodpovědná za použití změn. V případě potřeby můžete restartovat službu samotný. |
| HealthCheckRetryTimeoutSec |PS VS |Doba trvání (v sekundách) že Service Fabric stále provádí vyhodnocování stavu před deklarací upgrade, protože se nepovedlo. Výchozí hodnota je 600 sekund. V této hodnotě duration spustí po *HealthCheckWaitDurationSec* je dosaženo. V rámci této *HealthCheckRetryTimeout*, Service Fabric může provádět několik kontroly stavu o stavu aplikace. Výchozí hodnota je 10 minut a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| HealthCheckStableDurationSec |PS VS |Doba trvání (v sekundách) Chcete-li ověřit, že je aplikace stabilní před přesunutím k další upgradovací doméně nebo dokončení upgradu. Tato čekací doba se používá při prevenci nezjištěným změnám stavu ihned po provedení kontroly stavu. Výchozí hodnota je 120 sekund a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| HealthCheckWaitDurationSec |PS VS | Doba (v sekundách) po dokončení upgradu upgradovací domény před Service Fabric vyhodnocuje stav aplikace. V této hodnotě duration lze také považovat za čas, kdy aplikace by měla být spuštěná, než ji lze považovat za v pořádku. Pokud úspěšně proběhne kontrola stavu, proces upgradu pokračuje k další upgradovací doméně.  Pokud se nezdaří Kontrola stavu Service Fabric počká [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) než to zkusíte znovu stav zkontrolujte znovu, dokud *HealthCheckRetryTimeoutSec* je dosaženo. Výchozí a doporučená hodnota je 0 sekund. |
| MaxPercentUnhealthyDeployedApplications|PS VS |Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (najdete v článku [stavu části](service-fabric-health-introduction.md)), který může být není v pořádku, než aplikace se považoval za poškozený a upgradu se nezdaří. Tento parametr definuje stav aplikace na uzlu a umožňuje detekovat problémy při upgradu. Obvykle získejte repliky aplikace s vyrovnáváním zatížení do jiného uzlu, který umožňuje aplikaci zobrazovat v pořádku, což umožní inovace, aby bylo možné pokračovat. Zadáním striktní *MaxPercentUnhealthyDeployedApplications* stavu, Service Fabric můžete rychle zjistit problém s balíčkem aplikace a získat rychlé inovace převzetí. |
| MaxPercentUnhealthyServices |PS VS |Parametr *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet služeb v instanci aplikace, který může být není v pořádku, než aplikace se považoval za poškozený a upgradu se nezdaří. |
| MaxPercentUnhealthyPartitionsPerService|PS VS |Parametr *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet oddílů ve službě, která může být není v pořádku, než služba se považuje za není v pořádku. |
| MaxPercentUnhealthyReplicasPerPartition|PS VS |Parametr *DefaultServiceTypeHealthPolicy* a *ServiceTypeHealthPolicyMap*. Výchozí a doporučená hodnota je 0. Zadejte maximální počet replik v oddílu, který může být není v pořádku, než oddílu se považoval za poškozený. |
| ServiceTypeHealthPolicyMap | PS VS | Představuje zásad stavu, používá k vyhodnocení stavu služeb, které patří typ služby. Přijímá vstup tabulky hash v následujícím formátu: @ {"ServiceTypeName": "MaxPercentUnhealthyServices MaxPercentUnhealthyPartitionsPerService MaxPercentUnhealthyReplicasPerPartition,"} Příklad: @{"ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5"} |
| TimeoutSec | PS VS | Určuje časový limit v sekundách pro operace. |
| UpgradeDomainTimeoutSec |PS VS |Maximální doba (v sekundách) pro upgrade jedné upgradovací doména. Při dosažení tento časový limit upgradu zastaví a pokračuje podle nastavení *FailureAction*. Výchozí hodnota je nikdy (nekonečné) a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| UpgradeReplicaSetCheckTimeoutSec |PS VS |Měří v sekundách.<br>**Bezstavová služba**– v rámci jedné upgradovací doména pokusí ověřte, že jsou k dispozici další instance služby Service Fabric. Pokud cílový počet instancí je více než jeden, Service Fabric čeká víc než jedna instance k dispozici, až maximální hodnotu časového limitu. Tento časový limit je určen pomocí *UpgradeReplicaSetCheckTimeoutSec* vlastnost. Pokud vyprší časový limit, Service Fabric bude pokračovat v upgradu, bez ohledu na počet instancí služby. Pokud cílový počet instancí, Service Fabric nečeká a bude okamžitě pokračovat v upgradu.<br><br>**Stavové služby**– v jedné upgradovací doméně, Service Fabric se pokusí zajistit, že kvorum replik. Service Fabric počká kvora k dispozici, až maximální hodnotu časového limitu (určená *UpgradeReplicaSetCheckTimeoutSec* vlastnost). Pokud vyprší časový limit, Service Fabric bude pokračovat v upgradu, bez ohledu na to kvora. Toto nastavení je sada jako nikdy (nekonečné) při posunutí vpřed a 1 200 sekund při vrácení zpět. |
| UpgradeTimeoutSec |PS VS |Časový limit (v sekundách), která se vztahuje na celý upgrade. Když se dosáhne tento časový limit upgradu zastaví a *FailureAction* se aktivuje. Výchozí hodnota je nikdy (nekonečné) a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| WhatIf | PS | Povolené hodnoty jsou **True** a **False**. Ukazuje, co by se stalo při spuštění rutiny. Rutina není spuštěna. |

*MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*, a *MaxPercentUnhealthyReplicasPerPartition* jednu může být zadaná kritéria Typ služby pro instanci aplikace. Nastavení těchto parametrů za služba umožňuje pro aplikace obsahovat typy různých služeb s jinou vyhodnocení zásad. Například můžete mít typ služby bezstavové brány *MaxPercentUnhealthyPartitionsPerService* , která se liší od typu stavové stroje služby pro konkrétní instanci aplikace.

## <a name="sfctl-parameters"></a>SFCTL parametry

Upgrady aplikací Service Fabric pomocí rozhraní příkazového řádku Service Fabric [upgrade aplikace sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) spolu s následující povinné a nepovinné parametry příkazu.

### <a name="required-parameters"></a>Požadované parametry

| Parametr | Popis |
| --- | --- |
| id aplikace  |ID aplikace, která se upgraduje. <br> To je obvykle celý název aplikaci bez služby ' prostředků infrastruktury: "schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "~" znak. Například, pokud je název aplikace "fabric: / myapp/app1", identita aplikace by "myapp ~ app1" v 6.0 + a "myapp/app1" v předchozích verzích.|
verze aplikace |Typ verze aplikace, aby upgradu cíle.|
parameters  |Seznam JSON kódovaný parametr aplikace funkci přepsání hodnot a použijí při upgradu aplikace.|

### <a name="optional-parameters"></a>Volitelné parametry
| Parametr | Popis |
| --- | --- |
Výchozí služba stavu zásad | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) kódovaný specifikace zásad stavu, ve výchozím nastavení používá k vyhodnocení stavu daného typu služby. Mapa je ve výchozím nastavení prázdné. |
selhání akce | Povolené hodnoty jsou **vrácení zpět**, **ruční**, a **neplatný**. Kompenzační akce, která má provést při *monitorované* upgradu zjistí monitorování zásad nebo stavu porušení zásad. <br>**Vrácení zpět** Určuje, že upgrade bude automaticky vrátit k verzi před upgradem. <br>**Ruční** označuje, že upgrade přepne *UnmonitoredManual* režim upgradu. <br>**Neplatný** označuje, že selhání akce je neplatná.|
Vynutit restartování | Při aktualizaci konfigurace nebo balíček dat bez aktualizace kódu služby, pouze v případě, ForceRestart je nastavena na restartování služby **True**. Po dokončení aktualizace se Service Fabric upozorní službu nový balíček pro konfiguraci nebo balíček dat je k dispozici. Služba je zodpovědná za použití změn. V případě potřeby můžete restartovat službu samotný. |
Stav –--časový limit opakování kontroly | Množství času opakovat vyhodnocování stavu, pokud aplikace nebo clusteru není v pořádku před *FailureAction* provádí. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. Výchozí: PT0H10M0S. |
Stav kontroly stabilní trvání | Množství času, aplikace nebo clusteru musí zůstane v dobrém stavu před provedením upgradu k další upgradovací doméně. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. Výchozí: PT0H2M0S. |
stav –--Čekání na kontrolu | Množství času po dokončení upgradu domény před použitím zásad stavu. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. Výchozí: 0.|
maximální počet není v pořádku, aplikace | Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (najdete v článku [stavu části](service-fabric-health-introduction.md)), který může být není v pořádku, než aplikace se považoval za poškozený a upgradu se nezdaří. Tento parametr definuje stav aplikace na uzlu a umožňuje detekovat problémy při upgradu. Obvykle získejte repliky aplikace s vyrovnáváním zatížení do jiného uzlu, který umožňuje aplikaci zobrazovat v pořádku, což umožní inovace, aby bylo možné pokračovat. Zadáním striktní *maximální počet není v pořádku aplikace* stavu, Service Fabric můžete rychle zjistit problém s balíčkem aplikace a získat rychlé inovace převzetí. Vyjádřené číslem v rozmezí 0 až 100. |
režim | Povolené hodnoty jsou **monitorované**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Výchozí hodnota je **UnmonitoredAuto**. Podívejte se, Visual Studio a PowerShell *požadované parametry* části popisy těchto hodnot.|
repliky sady kontrola-časový limit |Měří v sekundách. <br>**Bezstavová služba**– v rámci jedné upgradovací doména pokusí ověřte, že jsou k dispozici další instance služby Service Fabric. Pokud cílový počet instancí je více než jeden, Service Fabric čeká víc než jedna instance k dispozici, až maximální hodnotu časového limitu. Tento časový limit je určen pomocí *repliky sady kontrola-časový limit* vlastnost. Pokud vyprší časový limit, Service Fabric bude pokračovat v upgradu, bez ohledu na počet instancí služby. Pokud cílový počet instancí, Service Fabric nečeká a bude okamžitě pokračovat v upgradu.<br><br>**Stavové služby**– v jedné upgradovací doméně, Service Fabric se pokusí zajistit, že kvorum replik. Service Fabric počká kvora k dispozici, až maximální hodnotu časového limitu (určená *repliky sady kontrola-časový limit* vlastnost). Pokud vyprší časový limit, Service Fabric bude pokračovat v upgradu, bez ohledu na to kvora. Toto nastavení je sada jako nikdy (nekonečné) při posunutí vpřed a 1 200 sekund při vrácení zpět. |
zásady služby stavu | JSON kódovaný mapování zásad stavu typ služby za název typu služby. Mapa je prázdný jako výchozí. [Parametr formátu JSON. ](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Obsahuje kód JSON pro části "Value" **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**, a **MaxPercentUnhealthyReplicasPerPartition**. V části Visual Studio a volitelné parametry Powershellu popisy těchto parametrů.
timeout | Určuje časový limit v sekundách pro operace. Výchozí: 60. |
časový limit upgradu domény | Množství času každé domény upgradu musí dokončit před *FailureAction* provádí. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. Výchozí hodnota je nikdy (nekonečné) a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. Výchozí: P10675199DT02H48M05.4775807S. |
časový limit upgradu | Množství času každé domény upgradu musí dokončit před *FailureAction* provádí. Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. Výchozí hodnota je nikdy (nekonečné) a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. Výchozí: P10675199DT02H48M05.4775807S.|
upozornění jako chyby | Povolené hodnoty jsou **True** a **False**. Výchozí je hodnota **False**. Je možné předat v jako příznak. Zpracovávat události upozornění stavu aplikace jako chyby při vyhodnocování stavu aplikace v průběhu upgradu. Ve výchozím nastavení Service Fabric není vyhodnocen události stavu upozornění jako chyby (chyby), takže upgradu můžete pokračovat i v případě, že jsou události upozornění. |

## <a name="next-steps"></a>Další postup
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

[Upgrade vaší aplikace pomocí příkazového řádku Service Fabric v Linuxu](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vás provede upgrade aplikace pomocí příkazového řádku Service Fabric.

[Upgrade vaší aplikace pomocí modulu plug-in Eclipse pro Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).
