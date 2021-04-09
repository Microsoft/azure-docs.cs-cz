---
title: Jak nakonfigurovat Azure cache pro Redis
description: Principy výchozí konfigurace Redis pro Azure cache pro Redis a informace o tom, jak nakonfigurovat mezipaměť Azure pro instance Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: ba021d280f71504ad5c1e1aea0dc1c8c6b507b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581080"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Jak nakonfigurovat Azure cache pro Redis
Toto téma popisuje konfigurace dostupné pro instance Redis v mezipaměti Azure. Toto téma také popisuje výchozí konfiguraci serveru Redis pro Azure cache pro instance Redis.

> [!NOTE]
> Další informace o konfiguraci a používání funkce Premium cache najdete v tématech konfigurace [trvalosti](cache-how-to-premium-persistence.md), [Konfigurace clusteringu](cache-how-to-premium-clustering.md)a [Postup konfigurace podpory Virtual Network](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurace mezipaměti Azure pro nastavení Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Nastavení mezipaměti Azure pro Redis se zobrazují a konfigurují v okně **Azure cache pro Redis** pomocí **nabídky prostředků**.

![Mezipaměť Azure pro nastavení Redis](./media/cache-configure/redis-cache-settings.png)

Pomocí **nabídky prostředků** můžete zobrazit a nakonfigurovat následující nastavení.

* [Přehled](#overview)
* [Protokol aktivit](#activity-log)
* [Řízení přístupu (IAM)](#access-control-iam)
* [Značky](#tags)
* [Diagnóza a řešení problémů](#diagnose-and-solve-problems)
* [Nastavení](#settings)
    * [Přístupové klíče](#access-keys)
    * [Rozšířená nastavení](#advanced-settings)
    * [Mezipaměť Azure pro poradce Redis](#azure-cache-for-redis-advisor)
    * [Škálování](#scale)
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
    * [Importovat data](#importexport)
    * [Exportovat data](#importexport)
    * [Restartování](#reboot)
* [Monitorování](#monitoring)
    * [Redis metriky](#redis-metrics)
    * [Pravidla upozornění](#alert-rules)
    * [Diagnostika](#diagnostics)
* Podpora nastavení & řešení potíží
    * [Stav prostředků](#resource-health)
    * [Nová žádost o podporu](#new-support-request)


## <a name="overview"></a>Přehled

**Přehled** poskytuje základní informace o vaší mezipaměti, jako je název, porty, cenová úroveň a vybrané metriky mezipaměti.

### <a name="activity-log"></a>Protokol aktivit

Kliknutím na **Protokol aktivit** zobrazíte akce provedené v mezipaměti. Pomocí filtrování můžete také rozšířit toto zobrazení tak, aby zahrnovalo další prostředky. Další informace o práci s protokoly auditu najdete v tématu [operace auditu s správce prostředků](../azure-resource-manager/management/view-activity-logs.md). Další informace o monitorování mezipaměti Azure pro události Redis najdete v tématu věnovaném [operacím a výstrahám](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Řízení přístupu (IAM)

Část **řízení přístupu (IAM)** poskytuje podporu řízení přístupu na základě rolí Azure (Azure RBAC) v Azure Portal. Tato konfigurace pomáhá organizacím splnit požadavky na správu přístupu jednoduše a přesně. Další informace najdete v tématu [řízení přístupu na základě role Azure v Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Značky

Oddíl **značky** vám pomůže organizovat prostředky. Další informace najdete v článku [Použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostikovat a řešit problémy

Klikněte na **Diagnostika a řešení problémů** , které se budou poskytovat společně s běžnými problémy a strategiemi pro jejich řešení.



## <a name="settings"></a>Nastavení
Část **Nastavení** vám umožní získat přístup k následujícím nastavením pro mezipaměť a nakonfigurovat je.

* [Přístupové klíče](#access-keys)
* [Rozšířená nastavení](#advanced-settings)
* [Mezipaměť Azure pro poradce Redis](#azure-cache-for-redis-advisor)
* [Škálování](#scale)
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
Kliknutím na **přístupové klíče** zobrazíte nebo znovu vygenerujete přístupové klíče pro vaši mezipaměť. Tyto klíče používají klienti, kteří se připojují k vaší mezipaměti.

![Mezipaměť Azure pro přístupové klíče Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Rozšířená nastavení
V okně **Upřesnit nastavení** jsou nakonfigurovaná následující nastavení.

* [Přístupové porty](#access-ports)
* [Zásady paměti](#memory-policies)
* [Oznámení o prostoru na místě (rozšířené nastavení)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Přístupové porty
Ve výchozím nastavení je přístup bez TLS/SSL pro nové mezipaměti zakázaný. Pokud chcete povolit port bez TLS, klikněte na **ne** pro **Povolit přístup jenom přes SSL** v okně **Upřesnit nastavení** a pak klikněte na **Uložit**.

> [!NOTE]
> Přístup TLS ke službě Azure cache pro Redis podporuje v současné době TLS 1,0, 1,1 a 1,2, ale brzy se vyřadí verze 1,0 a 1,1.  Další podrobnosti najdete na naší [stránce odebrání TLS 1,0 a 1,1](cache-remove-tls-10-11.md) .

![Mezipaměť Azure pro porty přístupu Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zásady paměti
Nastavení zásad paměti pro mezipaměť **Maxmemory zásady**, **Maxmemory rezervované** a **maxfragmentationmemory** v okně **Upřesnit nastavení** .

![Mezipaměť Azure pro zásady Maxmemory pro Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zásady Maxmemory** nakonfigurují zásady vyřazení pro mezipaměť a umožňují vám vybírat z následujících zásad vyřazení:

* `volatile-lru` – Toto je výchozí zásada vyřazení.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Další informace o `maxmemory` zásadách najdete v tématu [Zásady vyřazení](https://redis.io/topics/lru-cache#eviction-policies).

Nastavení **rezervované pro maxmemory** konfiguruje velikost paměti v MB na instanci v clusteru, která je vyhrazena pro operace, které nejsou uložené v mezipaměti, jako je například replikace během převzetí služeb při selhání. Nastavení této hodnoty vám umožní mít jednotnější prostředí Redis serveru, když se vaše zatížení mění. Tato hodnota by měla být nastavená na vyšší pro úlohy, které jsou náročné na zápis. Pokud je paměť vyhrazena pro tyto operace, není k dispozici pro ukládání dat uložených v mezipaměti.

Nastavení **rezervované pro maxfragmentationmemory** konfiguruje velikost paměti v MB na instanci v clusteru, která je vyhrazena pro fragmentaci paměti. Když nastavíte tuto hodnotu, budete mít k dispozici jednotnější možnosti Redis serveru, když je mezipaměť plná nebo téměř kompletní, a poměr fragmentace je vysoký. Pokud je paměť vyhrazena pro tyto operace, není k dispozici pro ukládání dat uložených v mezipaměti.

Jedna věc, kterou je potřeba vzít v úvahu při výběru nové hodnoty rezervace paměti (**maxmemory-rezervováno** nebo **maxfragmentationmemory**), je to, jak může tato změna ovlivnit mezipaměť, která už je spuštěná s velkým objemem dat. Pokud máte například mezipaměť 53 GB s 49 GB dat, změňte hodnotu rezervace na 8 GB. Tato změna způsobí, že maximální dostupná paměť systému vyřadí hodnotu až 45 GB. Pokud vaše aktuální `used_memory` `used_memory_rss` hodnota nebo hodnoty jsou vyšší než nové omezení 45 GB, bude systém muset data vyřadit, dokud `used_memory` `used_memory_rss` nebudou a nižší než 45 GB. Vyřazení může zvýšit zatížení serveru a fragmentaci paměti. Další informace o metrikách mezipaměti `used_memory` , jako jsou a `used_memory_rss` , najdete v tématu [dostupné metriky a intervaly generování sestav](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Nastavení rezervovaná v **maxmemory** a **maxfragmentationmemory** jsou dostupná jenom pro mezipaměti úrovně Standard a Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Oznámení o prostoru na místě (rozšířené nastavení)
V okně **Upřesnit nastavení** jsou nakonfigurovaná oznámení o prostoru Redis. Upozornění na místo na disku umožňují klientům přijímat oznámení, když dojde k určitým událostem.

![Rozšířená nastavení mezipaměti Azure pro Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Oznámení o prostoru na místě a nastavení upozornění na místo pro přístup k **událostem** jsou dostupná jenom pro mezipaměti Standard a Premium.
>
>

Další informace najdete v tématu [oznámení o prostorech Redis](https://redis.io/topics/notifications). Vzorový kód najdete v souboru [KeySpaceNotifications. cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) v ukázce [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Mezipaměť Azure pro poradce Redis
Okno **Poradce Azure cache pro Redis** zobrazí doporučení pro vaši mezipaměť. Při normálních operacích se nezobrazují žádná doporučení.

![Snímek obrazovky, který ukazuje, kde se mají doporučení zobrazovat](./media/cache-configure/redis-cache-no-recommendations.png)

V případě, že během operací mezipaměti, jako je například využití vysoké paměti, Šířka pásma nebo zatížení serveru, dojde k jakýmkoli podmínkám, zobrazí se výstraha v okně **Azure cache pro Redis** .

![Snímek obrazovky, který ukazuje, kde se výstrahy zobrazují v části mezipaměť Azure pro Redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

Další informace najdete v okně **doporučení** .

![Doporučení](./media/cache-configure/redis-cache-recommendations.png)

Tyto metriky můžete monitorovat v částech [grafy monitorování](cache-how-to-monitor.md#monitoring-charts) a [grafy využití](cache-how-to-monitor.md#usage-charts) v okně **Azure cache pro Redis** .

Každá cenová úroveň má odlišná omezení pro připojení klientů, paměť a šířku pásma. Pokud vaše mezipaměť přistupuje k maximální kapacitě pro tyto metriky v rámci trvajícího časového období, vytvoří se doporučení. Další informace o metrikách a omezeních zkontrolovaných nástrojem **Recommendations** Tool najdete v následující tabulce:

| Mezipaměť Azure pro metriku Redis | Další informace |
| --- | --- |
| Využití šířky pásma sítě |[Výkon mezipaměti – dostupná šířka pásma](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Připojení klienti |[Výchozí konfigurace serveru Redis – maxclients](#maxclients) |
| Zatížení serveru |[Grafy využití – zatížení serveru Redis](cache-how-to-monitor.md#usage-charts) |
| Využití paměti |[Výkon mezipaměti – velikost](cache-planning-faq.md#azure-cache-for-redis-performance) |

Pokud chcete upgradovat mezipaměť, klikněte na **upgradovat** , aby se změnila cenová úroveň a [škálovat](#scale) mezipaměť. Další informace o volbě cenové úrovně najdete v tématu [Volba správné úrovně](cache-overview.md#choosing-the-right-tier) .


### <a name="scale"></a>Měřítko
Kliknutím na tlačítko **škálovat** můžete zobrazit nebo změnit cenovou úroveň pro vaši mezipaměť. Další informace o škálování najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md).

![Cenová úroveň mezipaměti Azure pro Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Velikost clusteru Redis
Kliknutím na **Velikost clusteru** změníte velikost clusteru pro spuštěnou mezipaměť Premium s povoleným clusteringem.

![Velikost clusteru](./media/cache-configure/redis-cache-redis-cluster-size.png)

Chcete-li změnit velikost clusteru, použijte posuvník nebo zadejte číslo v rozmezí 1 až 10 v textovém poli **horizontálních oddílů Count** a kliknutím na tlačítko **OK** uložte.

> [!IMPORTANT]
> Clustering Redis je k dispozici jenom pro mezipaměti úrovně Premium. Další informace najdete v tématu [Konfigurace clusteringu pro službu Azure cache v úrovni Premium pro Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Trvalost dat Redis
Kliknutím na **Trvalost dat** povolíte, zakážete nebo nakonfigurujete Trvalost dat pro mezipaměť Premium. Azure cache for Redis nabízí trvalost Redisy buď v případě trvalého uložení RDB, nebo AOF.

Další informace najdete v tématu [Konfigurace trvalosti pro službu Azure cache Premium pro Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trvalost dat Redis je k dispozici pouze pro mezipaměti úrovně Premium.
>
>

### <a name="schedule-updates"></a>Plán aktualizací
V okně **naplánovat aktualizace** můžete určit časové období údržby pro aktualizace serveru Redis pro mezipaměť.

> [!IMPORTANT]
> Časové období údržby se vztahuje jenom na aktualizace serveru Redis a ne na aktualizace nebo aktualizace Azure v operačním systému virtuálních počítačů, které hostují mezipaměť.
>
>

![Plán aktualizací](./media/cache-configure/redis-schedule-updates.png)

Chcete-li určit časový interval pro správu a údržbu, zaškrtněte požadované dny a zadejte časový interval pro správu a údržbu pro každý den a klikněte na tlačítko **OK**. Čas časového období údržby je UTC.

Další informace a pokyny najdete v tématu [Správa Azure cache pro Redis – plánování aktualizací](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Geografická replikace

Okno **geografické replikace** poskytuje mechanismus pro propojení dvě mezipaměti Azure úrovně Premium pro instance Redis. Jedna mezipaměť je určena jako primární propojená mezipaměť a druhá jako sekundární propojená mezipaměť. Sekundární propojená mezipaměť se bude jen pro čtení a data zapsaná do primární mezipaměti se replikují do sekundární propojené mezipaměti. Tato funkce se dá použít k replikaci mezipaměti napříč oblastmi Azure.

> [!IMPORTANT]
> **Geografická replikace** je dostupná jenom pro mezipaměti úrovně Premium. Další informace a pokyny najdete v tématu [jak nakonfigurovat geografickou replikaci pro Azure cache pro Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Část **Virtual Network** vám umožní nakonfigurovat nastavení virtuální sítě pro mezipaměť. Informace o vytvoření mezipaměti Premium s podporou virtuální sítě a aktualizaci nastavení najdete v článku [jak nakonfigurovat Virtual Network podporu pro službu Azure cache Premium pro Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Nastavení virtuální sítě je dostupné jenom pro mezipaměti úrovně Premium, které byly nakonfigurované s podporou virtuální sítě během vytváření mezipaměti.
>
>

### <a name="firewall"></a>Brána firewall

Konfigurace pravidel brány firewall je dostupná pro všechny úrovně Redis v mezipaměti Azure.

Kliknutím na **firewall** zobrazíte a nakonfigurujete pravidla brány firewall pro mezipaměť.

![Brána firewall](./media/cache-configure/redis-firewall-rules.png)

Můžete zadat pravidla brány firewall s rozsahem počátečních a koncových IP adres. Když jsou pravidla brány firewall nakonfigurovaná, můžou se k ní připojit jenom připojení klientů ze zadaných rozsahů IP adres. Po uložení pravidla brány firewall dojde k krátké prodlevě, než bude pravidlo platné. Toto zpoždění je obvykle méně než jedna minuta.

> [!IMPORTANT]
> Připojení z mezipaměti Azure pro systémy monitorování Redis jsou vždycky povolená, i když jsou nakonfigurovaná pravidla brány firewall.
>
>

### <a name="properties"></a>Vlastnosti
Kliknutím na **vlastnosti** zobrazíte informace o mezipaměti, včetně koncového bodu mezipaměti a portů.

![Azure cache pro vlastnosti Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zámky
Část **zámky** umožňuje uzamknout předplatné, skupinu prostředků nebo prostředek a zabránit tak ostatním uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automatizační skript

Klikněte na **skript Automation** a sestavte a exportujte šablonu nasazených prostředků pro budoucí nasazení. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Nastavení správy
Nastavení v části **Správa** umožňují provádět následující úlohy správy pro mezipaměť.

![Správa](./media/cache-configure/redis-cache-administration.png)

* [Importovat data](#importexport)
* [Exportovat data](#importexport)
* [Restartování](#reboot)


### <a name="importexport"></a>Import/export
Import/Export je mezipaměť Azure pro operaci správy dat Redis, která umožňuje importovat a exportovat data v mezipaměti pomocí importu a exportu snímku Azure cache for Redis Database (RDB) z mezipaměti Premium do objektu blob stránky v účtu Azure Storage. Import/export umožňuje migrovat mezi různými Azure cache pro instance Redis nebo naplnit mezipaměť daty před použitím.

Import se dá použít k převedení Redis kompatibilních souborů RDB z libovolného serveru Redis spuštěného v jakémkoli cloudu nebo prostředí, včetně Redis, které běží na Linux, Windows nebo jakémkoli poskytovateli cloudu, jako je Amazon Web Services a další. Import dat představuje snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu načte Azure cache pro Redis soubory RDB z Azure Storage do paměti a pak vloží klíče do mezipaměti.

Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redis. Tuto funkci můžete použít k přesunu dat z jedné instance Azure cache pro instanci Redis do jiného nebo na jiný server Redis. Během procesu exportu se na virtuálním počítači, který hostuje instanci serveru Azure cache pro Redis, vytvoří dočasný soubor a soubor se nahraje do určeného účtu úložiště. Když se operace exportu dokončí buď se stavem úspěch, nebo neúspěchem, dočasný soubor se odstraní.

> [!IMPORTANT]
> Import/Export je k dispozici jenom pro mezipaměti úrovně Premium. Další informace a pokyny najdete v tématu [Import a export dat v Azure cache pro Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Restartování
Okno **restartování** vám umožní restartovat uzly mezipaměti. Tato schopnost restartování umožňuje testovat aplikaci, aby byla odolná proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-configure/redis-cache-reboot.png)

Pokud máte mezipaměť Premium s povoleným clusteringem, můžete vybrat, které horizontálních oddílů mezipaměti se mají restartovat.

![Snímek obrazovky, který ukazuje, kde vybrat, které horizontálních oddílů mezipaměti se mají restartovat.](./media/cache-configure/redis-cache-reboot-cluster.png)

Chcete-li restartovat jeden nebo více uzlů mezipaměti, vyberte požadované uzly a klikněte na tlačítko **restartovat**. Pokud máte mezipaměť Premium s povoleným clusteringem, vyberte horizontálních oddílů pro restartování a pak klikněte na **restartovat**. Po několika minutách se vybraný uzel (y) restartuje a později se znovu dovede k online několika minutám.

> [!IMPORTANT]
> Pro všechny cenové úrovně je teď k dispozici restart. Další informace a pokyny najdete v tématu [Správa Azure cache pro Redis – restart](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorování

Část **monitorování** vám umožní nakonfigurovat diagnostiku a monitorování pro mezipaměť Azure pro Redis.
Další informace o službě Azure cache pro monitorování a diagnostiku Redis najdete v tématu [monitorování mezipaměti Azure pro Redis](cache-how-to-monitor.md).

![Diagnostika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis metriky](#redis-metrics)
* [Pravidla upozornění](#alert-rules)
* [Diagnostika](#diagnostics)

### <a name="redis-metrics"></a>Redis metriky
Kliknutím na **metriky Redis** [zobrazíte metriky](cache-how-to-monitor.md#view-cache-metrics) pro vaši mezipaměť.

### <a name="alert-rules"></a>Pravidla upozornění

Kliknutím na **pravidla výstrah** můžete nakonfigurovat výstrahy založené na službě Azure cache pro metriky Redis. Další informace najdete v tématu [výstrahy](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostika

Ve výchozím nastavení se metriky mezipaměti v Azure Monitor [ukládají po dobu 30 dnů](../azure-monitor/essentials/data-platform-metrics.md) a pak se odstraní. Pokud chcete uchovat metriky mezipaměti déle než 30 dní, klikněte na **Diagnostika** a [Nakonfigurujte účet úložiště](cache-how-to-monitor.md#export-cache-metrics) , který se používá k ukládání diagnostiky mezipaměti.

>[!NOTE]
>Kromě archivace metrik mezipaměti do úložiště je můžete také [streamovat do centra událostí nebo je odeslat do protokolů Azure monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Podpora nastavení & řešení potíží
Nastavení v části **Podpora a řešení potíží** poskytují možnosti pro řešení problémů s mezipamětí.

![Podpora a řešení potíží](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Stav prostředků](#resource-health)
* [Nová žádost o podporu](#new-support-request)

### <a name="resource-health"></a>Stav prostředků
**Stav prostředku** sleduje váš prostředek a oznamuje, zda je spuštěný podle očekávání. Další informace o službě Azure Resource Health najdete v článku [Přehled Azure Resource Health](../service-health/resource-health-overview.md).

> [!NOTE]
> Služba Resource Health v tuto chvíli nemůže nahlásit stav mezipaměti Azure pro instance Redis hostované ve virtuální síti. Další informace najdete v tématu [práce s funkcemi mezipaměti při hostování mezipaměti ve virtuální síti?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Nová žádost o podporu
Kliknutím na **Nová žádost o podporu** otevřete žádost o podporu pro vaši mezipaměť.





## <a name="default-redis-server-configuration"></a>Výchozí konfigurace serveru Redis
Nová mezipaměť Azure pro instance Redis je nakonfigurovaná s následujícími výchozími hodnotami konfigurace Redis:

> [!NOTE]
> Nastavení v této části nelze změnit pomocí `StackExchange.Redis.IServer.ConfigSet` metody. Pokud je tato metoda volána jedním z příkazů v této části, je vyvolána výjimka, která je podobná následujícímu příkladu:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Všechny hodnoty, které lze konfigurovat, jako je například **Max-Memory-Policy**, lze konfigurovat prostřednictvím nástrojů pro správu Azure Portal nebo příkazového řádku, jako je Azure CLI nebo PowerShell.
>
>

| Nastavení | Výchozí hodnota | Description |
| --- | --- | --- |
| `databases` |16 |Výchozí počet databází je 16, ale můžete na základě cenové úrovně nakonfigurovat jiné číslo. <sup>1</sup> výchozí databáze je DB 0. pro jednotlivá připojení můžete vybrat jiný, `connection.GetDatabase(dbid)` a to pomocí, kde `dbid` je číslo mezi `0` a `databases - 1` . |
| `maxclients` |Závisí na cenové úrovni<sup>2</sup> . |Tato hodnota je maximální povolený počet připojených klientů ve stejnou dobu. Po dosažení limitu Redis ukončí všechna nová připojení a vrátí chybu maximální počet klientů, které se dosáhly. |
| `maxmemory-policy` |`volatile-lru` |Zásada Maxmemory je nastavení, jak Redis vybere, co se má odebrat, když `maxmemory` se dosáhne (velikost nabídky mezipaměti, kterou jste vybrali při vytváření mezipaměti). V případě služby Azure cache pro Redis je výchozí nastavení `volatile-lru` , což odstraní klíče s nastavenou hodnotou vypršení platnosti pomocí LRU algoritmu. Toto nastavení lze nakonfigurovat v Azure Portal. Další informace najdete v tématu [zásady paměti](#memory-policies). |
| `maxmemory-samples` |3 |Aby se ušetřila paměť, LRU a minimální algoritmy TTL jsou přibližné algoritmy místo přes přesné algoritmy. Ve výchozím nastavení Redis zkontroluje tři klíče a vybere ten, který byl naposledy použit méně. |
| `lua-time-limit` |5 000 |Maximální doba provádění lua skriptu v milisekundách Pokud je dosaženo maximální doby spuštění, protokol Redis zaznamená, že skript je stále spuštěný po maximálním povoleném čase, a začne odpovídat na dotazy s chybou. |
| `lua-event-limit` |500 |Maximální velikost fronty událostí skriptu |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Limity výstupní vyrovnávací paměti klienta se dají použít k vynucení odpojení klientů, kteří nečtou data ze serveru z nějakého důvodu dostatečně rychle (běžným důvodem je to, že klient typu Pub/Sub nemůže spotřebovávat zprávy tak rychle, jak je může vydavatel vytvořit). Další informace najdete na webu [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> U `databases` každé cenové úrovně služby Azure cache pro Redis se omezení pro liší a dá se nastavit při vytváření mezipaměti. Pokud `databases` při vytváření mezipaměti není zadané žádné nastavení, výchozí hodnota je 16.

* Mezipaměti úrovně Basic a Standard
  * C0 (250 MB) mezipaměť-až 16 databází
  * Mezipaměť C1 (1 GB) – až 16 databází
  * C2 (2,5 GB) cache-až 16 databází
  * Mezipaměť C3 (6 GB) cache-až 16 databází
  * C4 (13 GB) mezipaměti až 32 databází
  * C5 (26 GB) mezipaměti až 48 databází
  * C6 (53 GB) cache až 64 databází
* Mezipaměti úrovně Premium
  * P1 (6 GB až 60 GB) – až 16 databází
  * P2 (13 GB až 130 GB) – až 32 databází
  * P3 (26 GB až 260 GB) – až 48 databází
  * P4 (53 GB až 530 GB) – až 64 databází
  * Všechny mezipaměti úrovně Premium s povoleným clusterem Redis – cluster Redis podporuje jenom použití databáze 0, takže `databases` limit pro jakoukoliv mezipaměť Premium s povoleným clusterem Redis je efektivně 1 a příkaz [Select](https://redis.io/commands/select) není povolený. Další informace najdete v tématu musím dělat [změny v klientské aplikaci, aby používaly clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Další informace o databázích najdete v tématu [co jsou databáze Redis?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases`Nastavení se dá nakonfigurovat jenom během vytváření mezipaměti a jenom pomocí PowerShellu, rozhraní příkazového řádku nebo jiných klientů pro správu. Příklad konfigurace `databases` během vytváření mezipaměti pomocí prostředí PowerShell najdete v části [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` se liší pro každou cenovou úroveň Azure cache pro Redis.

* Mezipaměti úrovně Basic a Standard
  * C0 (250 MB) mezipaměť až 256 připojení
  * Mezipaměť C1 (1 GB) do 1 000 připojení
  * C2 (2,5 GB) mezipaměti až 2 000 připojení
  * Mezipaměť C3 (6 GB) mezipaměti až 5 000 připojení
  * C4 (13 GB) mezipaměti až 10 000 připojení
  * C5 (26 GB) mezipaměti až 15 000 připojení
  * C6 (53 GB) cache až 20 000 připojení
* Mezipaměti úrovně Premium
  * P1 (6 GB až 60 GB) – až 7 500 připojení
  * P2 (13 GB až 130 GB) – až 15 000 připojení
  * P3 (26 GB až 260 GB) – až 30 000 připojení
  * P4 (53 GB až 530 GB) – až 40 000 připojení

> [!NOTE]
> I když každá velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má spojenou režii. Příkladem takové režie by bylo využití CPU a paměti v důsledku šifrování TLS/SSL. Maximální limit připojení pro danou velikost mezipaměti předpokládá lehce načtenou mezipaměť. Pokud načtení z režie připojení *plus* zatížení z operací klienta překročí kapacitu systému, může mezipaměť zaznamenat problémy s kapacitou i v případě, že jste nepřekročili limit připojení pro aktuální velikost mezipaměti.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Příkazy Redis nejsou podporované v mezipaměti Azure pro Redis.
> [!IMPORTANT]
> Vzhledem k tomu, že konfigurace a Správa mezipaměti Azure pro instance Redis spravuje společnost Microsoft, jsou tyto příkazy zakázané. Pokud se pokusíte je vyvolat, zobrazí se chybová zpráva podobná této `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * Konfigurace
> * DEBUG
> * PŘENES
> * ULOŽIT
> * VYPNUTÍ
> * SLAVEOF
> * Příkazy zápisu cluster-cluster jsou zakázané, ale jsou povolené příkazy clusteru jen pro čtení.
>
>

Další informace o příkazech Redis naleznete v tématu [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Konzola Redis
Pomocí **konzoly Redis**, která je k dispozici v Azure Portal pro všechny úrovně mezipaměti, můžete bezpečně vystavit příkazy do mezipaměti Azure cache pro instance Redis.

> [!IMPORTANT]
> - Konzola Redis nefunguje s [virtuální](cache-how-to-premium-vnet.md)sítí. Když je mezipaměť součástí virtuální sítě, budou mít přístup k mezipaměti jenom klienti ve virtuální síti. Vzhledem k tomu, že se konzola Redis spouští v místním prohlížeči, který je mimo virtuální síť, nemůže se připojit ke svojí mezipaměti.
> - V mezipaměti Azure pro Redis nejsou podporované všechny příkazy Redis. Seznam příkazů Redis, které jsou pro Azure cache pro Redis zakázané, najdete v části předchozí [příkazy Redis, které se v Azure cache pro Redis nepodporují](#redis-commands-not-supported-in-azure-cache-for-redis) . Další informace o příkazech Redis naleznete v tématu [https://redis.io/commands](https://redis.io/commands) .
>
>

Přístup ke konzole Redis získáte tak, že kliknete na **Konzola** v okně **Azure cache pro Redis** .

![Snímek obrazovky, který zvýrazní tlačítko konzoly.](./media/cache-configure/redis-console-menu.png)

Chcete-li vydávat příkazy pro instanci mezipaměti, zadejte požadovaný příkaz do konzoly.

![Thas obrazovky zobrazuje konzolu Redis se vstupním příkazem a výsledky.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Použití konzoly Redis s Clusterovou mezipamětí Premium

Při použití konzoly Redis s Clusterovou mezipamětí Premium můžete vydávat příkazy pro jednu horizontálních oddílů mezipaměti. Pokud chcete vydat příkaz konkrétnímu horizontálních oddílů, nejdřív se připojte k požadovanému horizontálních oddílů kliknutím na výběr horizontálních oddílů.

![Konzola Redis](./media/cache-configure/redis-console-premium-cluster.png)

Pokud se pokusíte o přístup k klíči, který je uložený v jiném horizontálních oddílů než připojeném horizontálních oddílů, zobrazí se chybová zpráva podobná následující zprávě:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

V předchozím příkladu je horizontálních oddílů 1 vybraný horizontálních oddílů, ale `myKey` je umístěn v horizontálních oddílů 0, jak je uvedeno v `(shard 0)` části chybové zprávy. V tomto příkladu můžete pro přístup `myKey` Vybrat horizontálních oddílů 0 pomocí nástroje pro výběr horizontálních oddílů a pak vydávat požadovaný příkaz.


## <a name="move-your-cache-to-a-new-subscription"></a>Přesunout mezipaměť do nového předplatného
Mezipaměť můžete přesunout do nového předplatného kliknutím na **přesunout**.

![Přesunout Azure cache pro Redis](./media/cache-configure/redis-cache-move.png)

Informace o přesunu prostředků z jedné skupiny prostředků do jiné a z jednoho předplatného na jiný najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Další kroky
* Další informace o práci s Redis příkazy najdete v tématu [Jak můžu spustit příkazy Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
