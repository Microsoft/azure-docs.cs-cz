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
ms.date: 9/17/2018
ms.author: subramar
ms.openlocfilehash: 3f321775ba112471760e627e6b43ed17ff8c5b6b
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182871"
---
# <a name="application-upgrade-parameters"></a>Parametry upgradu aplikace
Tento článek popisuje různé parametry, které se použijí při upgradu aplikace Azure Service Fabric. Parametry upgradu aplikace řídit vypršení časových limitů a kontroly stavu, které se použijí při upgradu a určují zásady, které se musí použít při upgradu se nezdaří.

Parametry aplikace použít k upgradu pomocí Powershellu nebo sady Visual Studio. Povinných a volitelných parametrů pro prostředí PowerShell nebo sady Visual Studio jsou popsány v tabulkách požadované parametry a volitelných parametrů následujícím způsobem.

Upgrady aplikací jsou spuštěných prostřednictvím jeden ze tří režimů uživatelsky volitelných upgradu. Oba režimy má svou vlastní sadu parametrů aplikace:
- Monitorováno
- Nemonitorované automaticky
- Nemonitorované ruční

Upgrady aplikací Service Fabric pomocí Powershellu [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) příkazu. Režim upgradu je vybraná předáním buď **monitorované**, **UnmonitoredAuto**, nebo **UnmonitoredManual** parametr [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Visual Studio Service Fabric application upgradu parametry jsou nastavené přes dialogové okno nastavení Upgrade Visual Studio. Režim upgradu sady Visual Studio je vybraná pomocí **režimu upgradu** pole rozevíracího seznamu buď **monitorované**, **UnmonitoredAuto**, nebo **UnmonitoredManual** . Další informace najdete v tématu [konfigurace upgradu aplikace Service Fabric v sadě Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

## <a name="required-parameters"></a>Požadované parametry
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

## <a name="optional-parameters"></a>Volitelné parametry

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
| MaxPercentUnhealthyPartitionsPerService|PS VS |Výchozí a doporučená hodnota je 0. Zadejte maximální počet oddílů ve službě, která může být není v pořádku, než služba se považuje za není v pořádku. |
| MaxPercentUnhealthyReplicasPerPartition|PS VS |Výchozí a doporučená hodnota je 0. Zadejte maximální počet replik v oddílu, který může být není v pořádku, než oddílu se považoval za poškozený. |
| ServiceTypeHealthPolicyMap | PS VS | Představuje zásad stavu, používá k vyhodnocení stavu služeb, které patří typ služby. Přijímá vstup tabulky hash v následujícím formátu: @ {"ServiceTypeName": "MaxPercentUnhealthyServices MaxPercentUnhealthyPartitionsPerService MaxPercentUnhealthyReplicasPerPartition,"} Příklad: @{"ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5"} |
| TimeoutSec | PS VS | Určuje časový limit v sekundách pro operace. |
| UpgradeDomainTimeoutSec |PS VS |Maximální doba (v sekundách) pro upgrade jedné upgradovací doména. Při dosažení tento časový limit upgradu zastaví a pokračuje podle nastavení *FailureAction*. Výchozí hodnota je nikdy (nekonečné) a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| UpgradeReplicaSetCheckTimeoutSec |PS VS |**Bezstavová služba**– v rámci jedné upgradovací doména pokusí ověřte, že jsou k dispozici další instance služby Service Fabric. Pokud cílový počet instancí je více než jeden, Service Fabric čeká víc než jedna instance k dispozici, až maximální hodnotu časového limitu. Tento časový limit je určen pomocí *UpgradeReplicaSetCheckTimeoutSec* vlastnost. Pokud vyprší časový limit, Service Fabric bude pokračovat v upgradu, bez ohledu na počet instancí služby. Pokud cílový počet instancí, Service Fabric nečeká a bude okamžitě pokračovat v upgradu.<br><br>**Stavové služby**– v jedné upgradovací doméně, Service Fabric se pokusí zajistit, že kvorum replik. Service Fabric počká kvora k dispozici, až maximální hodnotu časového limitu (určená *UpgradeReplicaSetCheckTimeoutSec* vlastnost). Pokud vyprší časový limit, Service Fabric bude pokračovat v upgradu, bez ohledu na to kvora. Toto nastavení je sada jako nikdy (nekonečné) při posunutí vpřed a 1 200 sekund při vrácení zpět. |
| UpgradeTimeoutSec |PS VS |Časový limit (v sekundách), která se vztahuje na celý upgrade. Když se dosáhne tento časový limit upgradu zastaví a *FailureAction* se aktivuje. Výchozí hodnota je nikdy (nekonečné) a měli odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| WhatIf | PS | Povolené hodnoty jsou **True** a **False**. Ukazuje, co by se stalo při spuštění rutiny. Rutina není spuštěna. |

*MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*, a *MaxPercentUnhealthyReplicasPerPartition* jednu může být zadaná kritéria Typ služby pro instanci aplikace. Nastavení těchto parametrů za služba umožňuje pro aplikace obsahovat typy různých služeb s jinou vyhodnocení zásad. Například můžete mít typ služby bezstavové brány *MaxPercentUnhealthyPartitionsPerService* , která se liší od typu stavové stroje služby pro konkrétní instanci aplikace.

## <a name="next-steps"></a>Další postup
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

[Upgrade vaší aplikace pomocí příkazového řádku Service Fabric v Linuxu](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vás provede upgrade aplikace pomocí příkazového řádku Service Fabric.

[Upgrade vaší aplikace pomocí modulu plug-in Eclipse pro Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).
