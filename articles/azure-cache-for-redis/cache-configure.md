---
title: Jak nakonfigurovat Azure Cache pro Redis
description: Seznamte se s výchozí konfigurací Redis pro Azure Cache for Redis a zjistěte, jak nakonfigurovat instance Azure Cache for Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 3f0de52782694e6cbc8fdb6b55d545191dbbb350
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010303"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Jak nakonfigurovat Azure Cache pro Redis
Toto téma popisuje konfigurace, které jsou k dispozici pro vaše instance Azure Cache for Redis. Toto téma také popisuje výchozí konfiguraci serveru Redis pro instance Azure Cache for Redis.

> [!NOTE]
> Další informace o konfiguraci a používání funkcí mezipaměti premium naleznete v [tématech Konfigurace trvalosti](cache-how-to-premium-persistence.md), [Jak konfigurovat clustering](cache-how-to-premium-clustering.md)a [Jak nakonfigurovat podporu virtuální sítě](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurace azure mezipaměti pro nastavení Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Nastavení Mezipaměti Azure pro redis se zobrazí a nakonfigurují v okně **Azure Cache for Redis** pomocí **nabídky Prostředků**.

![Nastavení mezipaměti Azure pro redis](./media/cache-configure/redis-cache-settings.png)

Následující nastavení můžete zobrazit a nakonfigurovat pomocí **nabídky Zdroje**.

* [Přehled](#overview)
* [Protokol aktivit](#activity-log)
* [Řízení přístupu (IAM)](#access-control-iam)
* [Značky](#tags)
* [Diagnóza a řešení problémů](#diagnose-and-solve-problems)
* [Nastavení](#settings)
    * [Přístupové klíče](#access-keys)
    * [Upřesnit nastavení](#advanced-settings)
    * [Azure Cache pro poradce Redis](#azure-cache-for-redis-advisor)
    * [Měřítko](#scale)
    * [Velikost clusteru](#cluster-size)
    * [Trvalost dat](#redis-data-persistence)
    * [Plán aktualizací](#schedule-updates)
    * [Geografická replikace](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brána firewall](#firewall)
    * [Vlastnosti](#properties)
    * [Zámky](#locks)
    * [Automatizační skript](#automation-script)
* Správa
    * [Import dat](#importexport)
    * [Exportovat data](#importexport)
    * [Restartování](#reboot)
* [Monitorování](#monitoring)
    * [Redis metriky](#redis-metrics)
    * [Pravidla výstrah](#alert-rules)
    * [Diagnostika](#diagnostics)
* Podpora & řešení potíží
    * [Stav prostředků](#resource-health)
    * [Nová žádost o podporu](#new-support-request)


## <a name="overview"></a>Přehled

**Přehled** poskytuje základní informace o mezipaměti, jako je název, porty, cenová úroveň a vybrané metriky mezipaměti.

### <a name="activity-log"></a>Protokol aktivit

Kliknutím na **protokol aktivit** zobrazíte akce provedené v mezipaměti. Filtrování můžete také použít k rozbalení tohoto zobrazení tak, aby zahrnovalo další zdroje. Další informace o práci s protokoly auditu naleznete v [tématu Audit operace se Správcem prostředků](../azure-resource-manager/management/view-activity-logs.md). Další informace o monitorování událostí Azure Cache for Redis najdete v [tématu Operace a výstrahy](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Řízení přístupu (IAM)

Část **Řízení přístupu (IAM)** poskytuje podporu pro řízení přístupu na základě rolí (RBAC) na webu Azure Portal. Tato konfigurace pomáhá organizacím splnit jejich požadavky na správu přístupu jednoduše a přesně. Další informace najdete [v tématu řízení přístupu na základě rolí na webu Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Značky

Sekce **Značky** vám pomůže uspořádat prostředky. Další informace najdete v článku [Použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostikovat a řešit problémy

Klikněte na **Diagnostikovat a vyřešte problémy,** které mají být vybaveny běžnými problémy a strategiemi pro jejich řešení.



## <a name="settings"></a>Nastavení
Část **Nastavení** umožňuje přístup k následujícím nastavením mezipaměti a jejich konfiguraci.

* [Přístupové klíče](#access-keys)
* [Upřesnit nastavení](#advanced-settings)
* [Azure Cache pro poradce Redis](#azure-cache-for-redis-advisor)
* [Měřítko](#scale)
* [Velikost clusteru](#cluster-size)
* [Trvalost dat](#redis-data-persistence)
* [Plán aktualizací](#schedule-updates)
* [Geografická replikace](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brána firewall](#firewall)
* [Vlastnosti](#properties)
* [Zámky](#locks)
* [Automatizační skript](#automation-script)



### <a name="access-keys"></a>Přístupové klíče
Kliknutím na **přístupové klávesy** zobrazíte nebo znovu vygenerujete přístupové klíče pro mezipaměť. Tyto klíče používají klienti, kteří se připojují k mezipaměti.

![Azure Cache pro přístupové klíče Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Upřesnit nastavení
Následující nastavení jsou konfigurována v okně **Upřesnit nastavení.**

* [Přístup ové porty](#access-ports)
* [Zásady paměti](#memory-policies)
* [Oznámení o klíčovém prostoru (upřesňující nastavení)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Přístup ové porty
Ve výchozím nastavení je pro nové mezipaměti zakázán přístup bez tls/ssl. Chcete-li povolit port bez tls, klepněte na tlačítko **Ne** **pro povolení přístupu pouze prostřednictvím protokolu Upřesnit** **nastavení** a potom klepněte na tlačítko **Uložit**.

> [!NOTE]
> Přístup TLS k Azure Cache pro Redis podporuje TLS 1.0, 1.1 a 1.2 v současné době, ale verze 1.0 a 1.1 jsou vyřazeny brzy.  Další podrobnosti najdete na [stránce Remove TLS 1.0 a 1.1.](cache-remove-tls-10-11.md)

![Azure Cache pro porty pro přístup Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zásady paměti
**Zásady Maxmemory**, nastavení **vyhrazená pro maximální paměť**a nastavení **rezervované v paměti maxfragmentace** v okně **Upřesnit nastavení** nakonfigurují zásady paměti pro mezipaměť.

![Azure Cache pro zásady Redis Maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zásady Maxmemory** konfiguruje zásady vyřazovacíslužby pro mezipaměť a umožňují vám vybrat si z následujících zásad vyřazení:

* `volatile-lru`- Toto je výchozí zásada vystěhování.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Další informace `maxmemory` o zásadách naleznete v [tématu Zásady vyřazovacího systému](https://redis.io/topics/lru-cache#eviction-policies).

Nastavení **rezervované v maximální paměti** konfiguruje velikost paměti v MB, která je vyhrazena pro operace bez mezipaměti, jako je například replikace během převzetí služeb při selhání. Nastavení této hodnoty umožňuje mít konzistentnější prostředí serveru Redis, když se vaše zatížení liší. Tato hodnota by měla být nastavena vyšší pro úlohy, které jsou zápis těžký. Pokud je paměť vyhrazena pro tyto operace, není k dispozici pro ukládání dat uložených v mezipaměti.

Nastavení **rezervované paměti maxfragmentace** konfiguruje velikost paměti v MB, která je vyhrazena pro fragmentaci paměti. Nastavení této hodnoty umožňuje mít konzistentnější prostředí serveru Redis, když je mezipaměť plná nebo téměř plná a poměr fragmentace je vysoký. Pokud je paměť vyhrazena pro tyto operace, není k dispozici pro ukládání dat uložených v mezipaměti.

Jedna věc, kterou je třeba zvážit při výběru nové hodnoty rezervace paměti **(rezervovaná maxmemory** nebo **maxfragmentace paměti vyhrazena)** je, jak tato změna může ovlivnit mezipaměť, která je již spuštěna s velkým množstvím dat v něm. Například pokud máte 53 GB mezipaměti s 49 GB dat, pak změňte hodnotu rezervace na 8 GB, tato změna klesne maximální dostupné paměti pro systém až 45 GB. Pokud jsou `used_memory` aktuální `used_memory_rss` nebo vaše hodnoty vyšší než nový limit 45 GB, systém bude `used_memory` muset `used_memory_rss` vystěhovat data, dokud nebudou oba a budou nižší než 45 GB. Vyřazení může zvýšit zatížení serveru a fragmentaci paměti. Další informace o metrikách `used_memory` mezipaměti, jako jsou například a `used_memory_rss`, naleznete v [tématu Dostupné metriky a intervaly vytváření přehledů](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Nastavení **vyhrazená pro maximální paměť** a maximální **fragmentaci paměti** jsou k dispozici pouze pro mezipaměti Standard a Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Oznámení o klíčovém prostoru (upřesňující nastavení)
Oznámení redis keyspace jsou konfigurována v okně **Upřesnit nastavení.** Oznámení o klíčovém prostoru umožňují klientům přijímat oznámení, když dojde k určitým událostem.

![Azure Cache pro pokročilá nastavení Redisu](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Oznámení o klíčovém prostoru a nastavení **oznamovacího prostoru jsou** k dispozici pouze pro mezipaměti Standard a Premium.
>
>

Další informace naleznete v tématu [Redis Keyspace Notifications](https://redis.io/topics/notifications). Ukázkový kód naleznete v [souboru KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) v ukázce [Hello world.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache pro poradce Redis
Okno **Azure Cache for Redis Advisor** zobrazuje doporučení pro vaši mezipaměť. Během běžných operací se nezobrazí žádná doporučení.

![Doporučení](./media/cache-configure/redis-cache-no-recommendations.png)

Pokud během operací mezipaměti nastanou nějaké podmínky, jako je vysoké využití paměti, šířka pásma sítě nebo zatížení serveru, zobrazí se v okně **Azure Cache for Redis** výstraha.

![Doporučení](./media/cache-configure/redis-cache-recommendations-alert.png)

Další informace naleznete na okně **Doporučení.**

![Doporučení](./media/cache-configure/redis-cache-recommendations.png)

Tyto metriky můžete sledovat v části [grafy monitorování](cache-how-to-monitor.md#monitoring-charts) a [využití](cache-how-to-monitor.md#usage-charts) v okně **Azure Cache for Redis.**

Každá cenová úroveň má různá omezení pro připojení klientů, paměť a šířku pásma. Pokud vaše mezipaměť přístupy maximální kapacitu pro tyto metriky po delší dobu, je vytvořeno doporučení. Další informace o metrikách a limitech zkoumaných nástrojem **Doporučení** naleznete v následující tabulce:

| Metrika Azure Cache for Redis | Další informace |
| --- | --- |
| Využití šířky pásma sítě |[Výkon mezipaměti – dostupná šířka pásma](cache-faq.md#cache-performance) |
| Připojení klienti |[Výchozí konfigurace serveru Redis - maxclients](#maxclients) |
| Zatížení serveru |[Grafy využití – zatížení serveru Redis](cache-how-to-monitor.md#usage-charts) |
| Využití paměti |[Výkon mezipaměti – velikost](cache-faq.md#cache-performance) |

Chcete-li mezipaměť upgradovat, kliknutím na **tlačítko Upgradovat nyní** změňte cenovou úroveň a [škálujte](#scale) mezipaměť. Další informace o výběru cenové úrovně najdete v tématu Co mám použít jakou [mezipaměť Azure pro Redis?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Měřítko
Kliknutím na **Měřítko** zobrazíte nebo změníte cenovou úroveň mezipaměti. Další informace o škálování najdete v tématu [Jak škálovat azure mezipaměť pro Redis](cache-how-to-scale.md).

![Cenová úroveň Azure Cache for Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Velikost clusteru Redis
Klepnutím na **tlačítko Velikost clusteru** změňte velikost clusteru pro spuštěnou mezipaměť premium s povoleným clusterováním.

![Velikost clusteru](./media/cache-configure/redis-cache-redis-cluster-size.png)

Chcete-li změnit velikost clusteru, použijte posuvník nebo zadejte číslo mezi 1 a 10 do textového pole **Počet úlomků** a klepnutím na **tlačítko OK** jej uložte.

> [!IMPORTANT]
> Clustering Redis je k dispozici pouze pro mezipaměti Premium. Další informace naleznete v tématu [Jak nakonfigurovat clustering pro premium Azure Cache pro Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Trvalost dat Redis
Chcete-li povolit, zakázat nebo nakonfigurovat trvalost dat pro prémiovou mezipaměť, klepněte na **možnost Trvalosti dat.** Azure Cache for Redis nabízí trvalost Redis pomocí [trvalosti RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) nebo [trvalosti AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Další informace naleznete v tématu [Jak nakonfigurovat trvalost pro premium Azure Cache pro Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trvalosti dat Redis je k dispozici pouze pro prémiové mezipaměti.
>
>

### <a name="schedule-updates"></a>Plán aktualizací
Okno **Plánování aktualizací** umožňuje určit okno údržby pro aktualizace serveru Redis pro vaši mezipaměť.

> [!IMPORTANT]
> Okno údržby se vztahuje jenom na aktualizace serveru Redis a nikoli na žádné aktualizace Azure nebo aktualizace operačního systému virtuálních počítače, které hostují mezipaměť.
>
>

![Plán aktualizací](./media/cache-configure/redis-schedule-updates.png)

Chcete-li určit okno údržby, zkontrolujte požadované dny a zadejte počáteční hodinu okna údržby pro každý den a klepněte na tlačítko **OK**. Doba časového intervalu údržby je v utc.

> [!IMPORTANT]
> Funkce **Plánování aktualizací** je k dispozici pouze pro mezipaměti úrovně Premium. Další informace a pokyny najdete v [tématu Azure Cache for Redis administration – Schedule updates](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Geografická replikace

Okno **geografické replikace** poskytuje mechanismus pro propojení dvou premium tier Azure Cache pro instance Redis. Jedna mezipaměť je označena jako primární propojená mezipaměť a druhá jako sekundární propojená mezipaměť. Sekundární propojená mezipaměť se stane jen pro čtení a data zapsaná do primární mezipaměti jsou replikována do sekundární propojené mezipaměti. Tuto funkci lze použít k replikaci mezipaměti napříč oblastmi Azure.

> [!IMPORTANT]
> **Geografická replikace** je k dispozici pouze pro mezipaměti úrovně Premium. Další informace a pokyny najdete v [tématu Jak nakonfigurovat geografickou replikaci pro Azure Cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Část **Virtuální síť** umožňuje konfigurovat nastavení virtuální sítě pro mezipaměť. Informace o vytvoření mezipaměti pro prémii s podporou virtuální sítě a aktualizaci jejích nastavení naleznete v tématu [Konfigurace podpory virtuálních sítí pro mezipaměť Azure Pro redis premium.](cache-how-to-premium-vnet.md)

> [!IMPORTANT]
> Nastavení virtuální sítě jsou k dispozici jenom pro prémiové mezipaměti, které byly nakonfigurovány s podporou virtuální sítě během vytváření mezipaměti.
>
>

### <a name="firewall"></a>Brána firewall

Konfigurace pravidel brány firewall je dostupná pro všechny úrovně Azure Cache pro úrovně Redis.

Chcete-li zobrazit a nakonfigurovat pravidla brány firewall pro mezipaměť, klepněte na **položku Brána firewall.**

![Brána firewall](./media/cache-configure/redis-firewall-rules.png)

Můžete zadat pravidla brány firewall s rozsahem počátečních a koncových adres IP. Při konfiguraci pravidel brány firewall se ke mezipaměti mohou připojit pouze klientská připojení ze zadaných rozsahů IP adres. Při uložení pravidla brány firewall je krátké zpoždění, než pravidlo je efektivní. Toto zpoždění je obvykle kratší než jedna minuta.

> [!IMPORTANT]
> Připojení ze systémů monitorování Azure Cache for Redis jsou vždy povolená, i když jsou nakonfigurovaná pravidla brány firewall.
>
>

### <a name="properties"></a>Vlastnosti
Kliknutím na **Vlastnosti** zobrazíte informace o mezipaměti, včetně koncového bodu mezipaměti a portů.

![Mezipaměť Azure pro vlastnosti Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zámky
Oddíl **Zámky** umožňuje uzamknout odběr, skupinu prostředků nebo prostředek, abyste zabránili ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automatizační skript

Kliknutím na **skript Automatizace** sestavíte a exportujete šablonu nasazených prostředků pro budoucí nasazení. Další informace o práci se šablonami najdete v [tématu Nasazení prostředků pomocí šablon Azure Resource Manager .](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="administration-settings"></a>Nastavení správy
Nastavení v části **Správa** umožňuje provádět následující úlohy správy mezipaměti.

![Správa](./media/cache-configure/redis-cache-administration.png)

* [Import dat](#importexport)
* [Exportovat data](#importexport)
* [Restartování](#reboot)


### <a name="importexport"></a>Import/export
Import a export je operace správy dat Azure Cache for Redis, která umožňuje importovat a exportovat data v mezipaměti importováním a exportem snímku Azure Cache for Redis Database (RDB) z mezipaměti premium do objektu blob stránky v účtu úložiště Azure. Import nebo export umožňuje migrovat mezi různými instancemi Azure Cache for Redis nebo naplnit mezipaměť daty před použitím.

Import lze použít k přenesení souborů RDB kompatibilních s redisem z libovolného serveru Redis spuštěného v jakémkoli cloudu nebo prostředí, včetně redisu běžícího na Linuxu, Windows nebo libovolného poskytovatele cloudu, jako jsou Amazon Web Services a další. Import dat je snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu Azure Cache for Redis načte soubory RDB z úložiště Azure do paměti a pak vloží klíče do mezipaměti.

Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redisem. Pomocí této funkce můžete přesunout data z jedné mezipaměti Azure pro instanci Redis na jiný nebo na jiný server Redis. Během procesu exportu se na virtuálním počítači, který je hostitelem instance serveru Azure Cache for Redis, vytvoří dočasný soubor a soubor se nahraje do určeného účtu úložiště. Po dokončení operace exportu se stavem úspěchu nebo neúspěchu je dočasný soubor odstraněn.

> [!IMPORTANT]
> Import nebo export je k dispozici pouze pro mezipaměti úrovně Premium. Další informace a pokyny najdete [v tématu Import a export dat v mezipaměti Azure pro Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Restartování
Okno **Reboot** umožňuje restartovat uzly mezipaměti. Tato možnost restartování umožňuje otestovat odolnost aplikace, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-configure/redis-cache-reboot.png)

Pokud máte prémiovou mezipaměť s povoleným clusteringem, můžete vybrat, které úlomky mezipaměti se mají restartovat.

![Restartování](./media/cache-configure/redis-cache-reboot-cluster.png)

Chcete-li restartovat jeden nebo více uzlů mezipaměti, vyberte požadované uzly a klepněte na tlačítko **Restartovat**. Pokud máte prémiovou mezipaměť s povoleným clusterováním, vyberte oddíly pro restartování a klepněte na tlačítko **Restartovat**. Po několika minutách se vybrané uzly restartují a o několik minut později jsou zpět online.

> [!IMPORTANT]
> Restartování je nyní k dispozici pro všechny cenové úrovně. Další informace a pokyny najdete v [tématu Azure Cache for Redis administration – Reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorování

V části **Monitorování** umožňuje konfigurovat diagnostiku a monitorování pro azure cache pro Redis.
Další informace o monitorování a diagnostice Azure Cache for Redis najdete v tématu [Jak monitorovat azure mezipaměť pro Redis](cache-how-to-monitor.md).

![Diagnostika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis metriky](#redis-metrics)
* [Pravidla výstrah](#alert-rules)
* [Diagnostika](#diagnostics)

### <a name="redis-metrics"></a>Redis metriky
Kliknutím na **metriky Redis** [zobrazíte metriky](cache-how-to-monitor.md#view-cache-metrics) mezipaměti.

### <a name="alert-rules"></a>Pravidla výstrah

Kliknutím na **Pravidla výstrah** nakonfigurujete výstrahy založené na metrikách Azure Cache for Redis. Další informace naleznete v tématu [Alerts](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostika

Ve výchozím nastavení se metriky mezipaměti ve službě Azure Monitor [ukládají po dobu 30 dnů](../azure-monitor/platform/data-platform-metrics.md) a pak se smažou. Chcete-li zachovat metriky mezipaměti déle než 30 dní, klepněte na tlačítko **Diagnostika** [a nakonfigurujte účet úložiště](cache-how-to-monitor.md#export-cache-metrics) používaný k ukládání diagnostiky mezipaměti.

>[!NOTE]
>Kromě archivace metrik mezipaměti do úložiště je můžete také [streamovat do centra událostí nebo je odeslat do protokolů Azure Monitoru](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Podpora & řešení potíží
Nastavení v části **Podpora + řešení potíží** poskytují možnosti řešení problémů s mezipamětí.

![Podpora a řešení potíží](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Stav prostředků](#resource-health)
* [Nová žádost o podporu](#new-support-request)

### <a name="resource-health"></a>Stav prostředků
**Stav prostředků** sleduje váš prostředek a informuje vás, pokud je spuštěn podle očekávání. Další informace o službě Azure Resource Health najdete v [tématu Přehled stavu prostředků Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Stav prostředků se v současné době nemůže hlásit o stavu azure mezipaměti pro redis instance hostované ve virtuální síti. Další informace najdete v tématu [Do všechny funkce mezipaměti pracovat při hostování mezipaměti ve virtuální síti?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Nová žádost o podporu
Kliknutím na **Nová žádost o podporu** otevřete žádost o podporu pro vaši mezipaměť.





## <a name="default-redis-server-configuration"></a>Výchozí konfigurace serveru Redis
Nové instance Azure Cache for Redis jsou nakonfigurované s následujícími výchozími hodnotami konfigurace Redis:

> [!NOTE]
> Nastavení v této části nelze `StackExchange.Redis.IServer.ConfigSet` změnit pomocí metody. Pokud je tato metoda volána s jedním z příkazů v této části, je vyvolána výjimka podobná následujícímu příkladu:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Všechny hodnoty, které jsou konfigurovatelné, jako je **například zásady maximální paměti**, jsou konfigurovatelné prostřednictvím portálu Azure nebo nástrojů pro správu příkazového řádku, jako je Azure CLI nebo PowerShell.
>
>

| Nastavení | Výchozí hodnota | Popis |
| --- | --- | --- |
| `databases` |16 |Výchozí počet databází je 16, ale můžete nakonfigurovat jiné číslo na základě cenové úrovně. <sup>1</sup> Výchozí databáze je DB 0, můžete vybrat jinou na `connection.GetDatabase(dbid)` `dbid` základě připojení `0` pomocí `databases - 1`místa, kde je číslo mezi a . |
| `maxclients` |Závisí na cenové úrovni<sup>2</sup> |Tato hodnota je maximální povolený počet připojených klientů současně. Po dosažení limitu Redis zavře všechna nová připojení, vrací chybu "maximální počet klientů bylo dosaženo". |
| `maxmemory-policy` |`volatile-lru` |Zásada maxmemory je nastavení, jak Redis `maxmemory` vybírá, co má být odstranit, když (velikost mezipaměti, kterou jste vybrali při vytváření mezipaměti) je dosaženo. S Azure Cache for Redis `volatile-lru`výchozí nastavení je , který odebere klíče s nastaveným vypršením platnosti pomocí algoritmu LRU. Toto nastavení lze nakonfigurovat na webu Azure Portal. Další informace naleznete v [tématu Zásady paměti](#memory-policies). |
| `maxmemory-samples` |3 |Pro uložení paměti jsou algoritmy LRU a minimální TTL aproximované algoritmy namísto přesných algoritmů. Ve výchozím nastavení Redis kontroluje tři klíče a vybere ten, který byl použit méně nedávno. |
| `lua-time-limit` |5 000 |Maximální doba spuštění skriptu Lua v milisekundách. Pokud je dosaženo maximální doby spuštění, Redis zaznamená, že skript je stále v provádění po maximální povolené době a začne odpovídat na dotazy s chybou. |
| `lua-event-limit` |500 |Maximální velikost fronty událostí skriptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032MB 8MB 60 |Limity vyrovnávací paměti výstupu klienta lze vynutit odpojení klientů, kteří nejsou čtení dat ze serveru dostatečně rychle z nějakého důvodu (běžným důvodem je, že Pub/Sub klient nemůže využívat zprávy tak rychle, jak vydavatel může produkovat). Další informace naleznete [https://redis.io/topics/clients](https://redis.io/topics/clients)v tématu . |

<a name="databases"></a>
<sup>1.</sup> Limit pro `databases` se liší pro každou azure mezipaměť pro úroveň cen Redis a lze nastavit při vytváření mezipaměti. Pokud `databases` během vytváření mezipaměti není zadáno žádné nastavení, je výchozí hodnota 16.

* Základní a standardní mezipaměti
  * Mezipaměť C0 (250 MB) – až 16 databází
  * Mezipaměť C1 (1 GB) – až 16 databází
  * Mezipaměť C2 (2,5 GB) – až 16 databází
  * Mezipaměť C3 (6 GB) – až 16 databází
  * Mezipaměť C4 (13 GB) – až 32 databází
  * Mezipaměť C5 (26 GB) – až 48 databází
  * Mezipaměť C6 (53 GB) – až 64 databází
* Prémiové mezipaměti
  * P1 (6 GB - 60 GB) - až 16 databází
  * P2 (13 GB - 130 GB) - až 32 databází
  * P3 (26 GB - 260 GB) - až 48 databází
  * P4 (53 GB - 530 GB) - až 64 databází
  * Všechny prémiové mezipaměti s povoleným clusterem Redis – cluster `databases` Redis podporuje pouze použití databáze 0, takže limit pro všechny mezipaměti premium s povoleným clusterem Redis je efektivně 1 a příkaz [Select](https://redis.io/commands/select) není povolen. Další informace naleznete v [tématu Je nutné provést nějaké změny v klientské aplikaci, aby bylo účelem použití clusteringu?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Další informace o databázích naleznete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> Nastavení `databases` lze nakonfigurovat pouze při vytváření mezipaměti a pouze pomocí prostředí PowerShell, CLI nebo jiných klientů pro správu. Příklad konfigurace `databases` během vytváření mezipaměti pomocí prostředí PowerShell naleznete v tématu [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` se liší pro každou azure mezipaměť pro úroveň ocenění Redis.

* Základní a standardní mezipaměti
  * Mezipaměť C0 (250 MB) – až 256 připojení
  * Mezipaměť C1 (1 GB) – až 1 000 připojení
  * Mezipaměť C2 (2,5 GB) – až 2 000 připojení
  * Mezipaměť C3 (6 GB) – až 5 000 připojení
  * Mezipaměť C4 (13 GB) – až 10 000 připojení
  * Mezipaměť C5 (26 GB) – až 15 000 připojení
  * Mezipaměť C6 (53 GB) – až 20 000 připojení
* Prémiové mezipaměti
  * P1 (6 GB - 60 GB) - až 7 500 připojení
  * P2 (13 GB - 130 GB) - až 15 000 připojení
  * P3 (26 GB - 260 GB) - až 30 000 připojení
  * P4 (53 GB - 530 GB) - až 40 000 připojení

> [!NOTE]
> Zatímco každá velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má režii s ním spojené. Příkladem takové režie by bylo využití procesoru a paměti v důsledku šifrování TLS/SSL. Maximální limit připojení pro danou velikost mezipaměti předpokládá mírně načtenou mezipaměť. Pokud zatížení z režie připojení *plus* zatížení z operací klienta překročí kapacitu pro systém, může dojít k problémům s kapacitou mezipaměti i v případě, že jste nepřekročili limit připojení pro aktuální velikost mezipaměti.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Příkazy Redis nejsou podporované v Azure Cache for Redis
> [!IMPORTANT]
> Vzhledem k tomu, že konfiguraci a správu instancí Azure Cache for Redis spravuje Microsoft, jsou zakázány následující příkazy. Pokud se pokusíte vyvolat, zobrazí se `"(error) ERR unknown command"`chybová zpráva podobná .
>
> * BGREWRITEAOF
> * BGSAVE
> * Config
> * DEBUG
> * Migrovat
> * ULOŽIT
> * Vypnutí
> * SLAVEOF
> * CLUSTER - Příkazy pro zápis clusteru jsou zakázány, ale jsou povoleny příkazy clusteru jen pro čtení.
>
>

Další informace o příkazech Redis naleznete v tématu [https://redis.io/commands](https://redis.io/commands).

## <a name="redis-console"></a>Redis konzole
Můžete bezpečně vydávat příkazy do vaší azure cache pro redis instance pomocí **Redis Console**, který je k dispozici na portálu Azure pro všechny úrovně mezipaměti.

> [!IMPORTANT]
> - Konzole Redis nefunguje s [virtuální sítí](cache-how-to-premium-vnet.md). Když je vaše mezipaměť součástí virtuální sítě, přístup k mezipaměti mají pouze klienti ve virtuální síti. Vzhledem k tomu, že konzole Redis se spouští v místním prohlížeči, který je mimo virtuální síť, nemůže se připojit k mezipaměti.
> - Ne všechny příkazy Redis jsou podporované v azure cache pro Redis. Seznam příkazů Redis, které jsou zakázané pro Azure Cache for Redis, najdete v článku předchozí [příkazy Redis, které nejsou podporované v části Azure Cache for Redis.](#redis-commands-not-supported-in-azure-cache-for-redis) Další informace o příkazech Redis naleznete v tématu [https://redis.io/commands](https://redis.io/commands).
>
>

Chcete-li získat přístup ke konzole Redis, klikněte na **konzolu** z okna **Azure Cache for Redis.**

![Redis konzole](./media/cache-configure/redis-console-menu.png)

Chcete-li vydat příkazy proti instanci mezipaměti, zadejte požadovaný příkaz do konzoly.

![Redis konzole](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Použití konzoly Redis s prémiovou clusterovnou mezipamětí

Při použití konzoly Redis s prémiovou clusterovnou mezipamětí můžete vydávat příkazy do jednoho oddílu mezipaměti. Chcete-li vydat příkaz pro konkrétní oddíl, nejprve se připojte k požadovanému úložu kliknutím na něj na výběru šicího oddílu.

![Redis konzole](./media/cache-configure/redis-console-premium-cluster.png)

Pokud se pokusíte o přístup ke klíči, který je uložen v jiném oddílu než připojený oddíl, zobrazí se chybová zpráva podobná následující zprávě:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

V předchozím příkladu je šiřidlo `myKey` 1 vybraným šiřitým oddílem, ale je umístěn o úloze 0, jak je uvedeno v `(shard 0)` části chybové zprávy. V tomto příkladu `myKey`pro přístup vyberte šitr 0 pomocí výběru šicího oddílu a pak vydejte požadovaný příkaz.


## <a name="move-your-cache-to-a-new-subscription"></a>Přesunutí mezipaměti na nové předplatné
Mezipaměť můžete přesunout do nového předplatného kliknutím na **Přesunout**.

![Přesunutí mezipaměti Azure pro Redis](./media/cache-configure/redis-cache-move.png)

Informace o přesunutí prostředků z jedné skupiny prostředků do druhé a z jednoho předplatného do jiného najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Další kroky
* Další informace o práci s příkazy Redis najdete v tématu [Jak lze spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
