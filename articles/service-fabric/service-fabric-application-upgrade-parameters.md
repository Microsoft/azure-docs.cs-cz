---
title: 'Upgrade aplikace: upgrade parametry | Microsoft Docs'
description: Popisuje parametry, na které se vztahují k upgradování aplikace Service Fabric, včetně kontroly stavu k provedení a zásad automaticky zrušit upgrade.
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
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: eb319b0f4e910163572ee62d8bdee735f27be592
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212615"
---
# <a name="application-upgrade-parameters"></a>Parametry upgradu aplikace
Tento článek popisuje různé parametry, které se vztahují během upgradu aplikace Azure Service Fabric. Parametry patří název a verzi aplikace. Jsou knoflíky, která řídí vypršení časových limitů a kontroly stavu, které se použijí během upgradu a určují zásady, které je nutné použít, když se upgrade nezdaří.

<br>

| Parametr | Popis |
| --- | --- |
| ApplicationName |Název aplikace, která se upgraduje. Příklady: fabric: / VisualObjects, fabric: / ClusterMonitor |
| TargetApplicationTypeVersion |Zadejte verzi aplikace, která upgradu cíle. |
| FailureAction |Akci provedenou Service Fabric, pokud se upgrade nezdaří. Aplikace může být vrácena zpět na verzi před aktualizací (vrácení) nebo upgradu je pravděpodobně zastavená v aktuální upgradovací doméně. V takovém případě se režim upgradu také změní na ruční. Povolené hodnoty jsou vrácení zpět a ručně. |
| HealthCheckWaitDurationSec |Dobu čekání (v sekundách), po dokončení upgradu v upgradovací doméně před Service Fabric vyhodnocuje stav aplikace. Tato doba trvání lze považovat také za dobu, kterou aplikace by měl být spuštěn, než ho lze považovat za v pořádku. Pokud úspěšně projde kontrolou stavu, proces upgradu pokračuje k další upgradovací doméně.  Pokud se nezdaří Kontrola stavu, Service Fabric čeká intervalu (UpgradeHealthCheckInterval) před opakováním kontrolou stavu znovu, dokud nebude dosaženo HealthCheckRetryTimeout. Výchozí a doporučená hodnota je 0 sekund. |
| HealthCheckRetryTimeoutSec |Doba trvání (v sekundách) této Service Fabric i nadále provádět vyhodnocování stavu než prohlásí upgrade, protože se nezdařilo. Výchozí hodnota je 600 sekund. Tato doba trvání spustí po dosažení HealthCheckWaitDuration. V rámci této HealthCheckRetryTimeout Service Fabric může provádět více kontroly stavu stavu aplikace. Výchozí hodnota je 10 minut a je nutné odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| HealthCheckStableDurationSec |Doba trvání (v sekundách) Chcete-li ověřit, že aplikace je stabilní před přesunutím k další upgradovací doméně nebo dokončení upgradu. Tato čekací doba trvání se používá při prevenci nezjištěné změny stavu správné po provedení kontroly stavu. Výchozí hodnota je 120 sekundách a by odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| UpgradeDomainTimeoutSec |Maximální doba (v sekundách) pro upgrade na jednu doménu upgradu. Pokud tento časový limit bude dosažen, upgradu zastaví a pokračuje podle nastavení UpgradeFailureAction. Výchozí hodnota je nikdy (nekonečné) a by odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| UpgradeTimeout |Časový limit (v sekundách), platí pro celou upgrade. Pokud tento časový limit bude dosažen, aktivuje se upgradu zastaví a UpgradeFailureAction. Výchozí hodnota je nikdy (nekonečné) a by odpovídajícím způsobem přizpůsobit pro vaši aplikaci. |
| UpgradeHealthCheckInterval |Frekvence, že je zaškrtnuté stav. Tento parametr je zadán v části ClusterManager *clusteru* *manifest*a není určena jako součást upgradu rutiny. Výchozí hodnota je 60 sekund. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Vyhodnocení stavu služby během upgradu aplikace
<br>
Kritéria hodnocení stavu jsou volitelné. Když kritérií hodnocení stavu nejsou zadané při spuštění upgradu, Service Fabric používá zásady stavu aplikace, zadaný v ApplicationManifest.xml instance aplikace.

<br>

| Parametr | Popis |
| --- | --- |
| ConsiderWarningAsError |Výchozí hodnota je False. Považovat za stavu události upozornění pro aplikaci chyby při vyhodnocování stavu aplikace v průběhu upgradu. Ve výchozím nastavení Service Fabric nelze vyhodnotit stav události upozornění na selhání (chyby), tak v upgradu můžete pokračovat, i když jsou události upozornění. |
| MaxPercentUnhealthyDeployedApplications |Výchozí a doporučená hodnota je 0. Zadejte maximální počet nasazených aplikací (najdete v článku [stavu části](service-fabric-health-introduction.md)), může být není v pořádku, než aplikace není v pořádku a upgradu se nezdaří. Tento parametr definuje v uzlu stav aplikace a pomáhá rozpoznat problémy při upgradu. Repliky aplikace obvykle, získat, Vyrovnávání zatížení do jiného uzlu, který umožňuje aplikaci zobrazovat v pořádku, což umožňuje upgradu pokračovat. Zadáním striktní MaxPercentUnhealthyDeployedApplications stavu, Service Fabric rychle zjistit problém s balíčkem aplikace a pomohli vytvořit rychlé upgradu selhání. |
| MaxPercentUnhealthyServices |Výchozí a doporučená hodnota je 0. Zadejte maximální počet služeb v instanci aplikace, která může být není v pořádku, než aplikace není v pořádku a upgradu se nezdaří. |
| MaxPercentUnhealthyPartitionsPerService |Výchozí a doporučená hodnota je 0. Zadejte maximální počet oddílů ve službě, která může být není v pořádku, než služba není v pořádku. |
| MaxPercentUnhealthyReplicasPerPartition |Výchozí a doporučená hodnota je 0. Zadejte maximální počet replik v oddílu, který může být není v pořádku, než oddílu není v pořádku. |
| UpgradeReplicaSetCheckTimeout |<p>**Bezstavové služby**– v jedné doméně upgradu, Service Fabric pokusí se ujistěte, že jsou k dispozici další instance služby. Pokud cílový počet instancí je víc než jedna, Service Fabric čeká víc než jedna instance k dispozici, až maximální hodnotu časového limitu. Tento časový limit je zadán pomocí vlastnosti UpgradeReplicaSetCheckTimeout. Pokud vyprší časový limit, Service Fabric pokračuje v upgradu, bez ohledu na počet instancí služby. Pokud cílový počet instancí, Service Fabric nečeká a okamžitě pokračuje v upgradu.</p><p>**Stavové služby**– v jedné doméně upgradu, Service Fabric pokusí zajistěte, aby sady replik kvora. Service Fabric čeká kvora být k dispozici, až maximální hodnotu časového limitu (určeného vlastností UpgradeReplicaSetCheckTimeout). Pokud vyprší časový limit, Service Fabric pokračuje v upgradu, bez ohledu na to kvora. Toto nastavení je sada jako nikdy (nekonečné) při vracení dál a 1200 sekund při vrácení zpět.</p> |
| ForceRestart |Při aktualizaci konfigurace nebo datového balíčku bez aktualizace service kód, restartování služby pouze v případě, že je vlastnost ForceRestart nastavena na hodnotu true. Po dokončení aktualizace Service Fabric upozorní služby a zda je k dispozici nový balíček konfigurace nebo datový balíček. Služba je zodpovědná za použití změny. V případě potřeby můžete restartovat službu sám sebe. |

<br>
<br>
Jeden typ služby pro instanci aplikace lze zadat kritéria MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService a MaxPercentUnhealthyReplicasPerPartition. Nastavení těchto parametrů za služby umožňuje pro aplikace obsahovat typy různé služby pomocí různých vyhodnocení zásad. Typ služby bezstavové brány může mít například MaxPercentUnhealthyPartitionsPerService, který je odlišný od typu stavový stroj služby pro konkrétní instanci aplikace.

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade vaší aplikace pomocí prostředí Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.

[Upgrade vaší aplikace pomocí Service Fabric rozhraní příkazového řádku v systému Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vás provede upgrade aplikace pomocí Service Fabric rozhraní příkazového řádku.

[Upgrade vaší aplikace pomocí modulu plug-in Eclipse Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Zkontrolujte upgradů aplikace kompatibilní podle naučit se používat [serializace dat](service-fabric-application-upgrade-data-serialization.md).

Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady](service-fabric-application-upgrade-troubleshooting.md).
