---
title: Postup konfigurace Azure mezipaměti Redis | Dokumentace Microsoftu
description: Principy výchozí konfigurace Redis pro mezipaměť Azure Redis a další informace o konfiguraci mezipaměti Azure pro instance Redis
services: azure-cache-for-redis
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: d938b8a7551f77f00b7c18421f07972a508f21ef
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019541"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Postup konfigurace mezipaměti Azure Redis
Toto téma popisuje dostupné konfigurace pro mezipaměť Azure pro instance Redis. Toto téma také popisuje výchozí konfigurace serveru Redis pro mezipaměť Azure pro instance Redis.

> [!NOTE]
> Další informace o konfiguraci a použití prémiových funkcí mezipaměti najdete v tématu [konfigurace trvalosti](cache-how-to-premium-persistence.md), [konfigurace clusteringu](cache-how-to-premium-clustering.md), a [konfigurace podpory služby Virtual Network ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurace mezipaměti Azure Redis nastavení
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Mezipaměť Azure Redis nastavení jsou zobrazit a konfigurovat na **mezipaměti Azure Redis** pomocí okna **nabídce prostředků**.

![Mezipaměť Azure Redis nastavení](./media/cache-configure/redis-cache-settings.png)

Můžete zobrazit a nakonfigurovat následující nastavení použití **nabídce prostředků**.

* [Přehled](#overview)
* [Protokol aktivit](#activity-log)
* [Řízení přístupu (IAM)](#access-control-iam)
* [Značky](#tags)
* [Diagnóza a řešení problémů](#diagnose-and-solve-problems)
* [Nastavení](#settings)
    * [Přístupové klíče](#access-keys)
    * [Upřesňující nastavení](#advanced-settings)
    * [Mezipaměť Azure pro Redis Advisor](#redis-cache-advisor)
    * [Škálování](#scale)
    * [Velikost clusteru redis](#cluster-size)
    * [Trvalost dat Redis](#redis-data-persistence)
    * [Plán aktualizací](#schedule-updates)
    * [Geografická replikace](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brána firewall](#firewall)
    * [Vlastnosti](#properties)
    * [Zámky](#locks)
    * [Automatizační skript](#automation-script)
* [Správa](#administration)
    * [Import dat](#importexport)
    * [Export dat](#importexport)
    * [Restartování](#reboot)
* [Monitorování](#monitoring)
    * [Metrika mezipaměti redis](#redis-metrics)
    * [Pravidla upozornění](#alert-rules)
    * [Diagnostika](#diagnostics)
* [Podpora a řešení potíží nastavení](#support-amp-troubleshooting-settings)
    * [Služba Resource health](#resource-health)
    * [Nová žádost o podporu](#new-support-request)


## <a name="overview"></a>Přehled

**Přehled** poskytuje vám základní informace o mezipaměti, jako je například název, porty, cenová úroveň a metriky mezipaměti vybrané.

### <a name="activity-log"></a>Protokol aktivit

Klikněte na tlačítko **protokolu aktivit** Chcete-li zobrazit akce prováděné v mezipaměti. Můžete také použít filtrování rozbalte toto zobrazení můžete zahrnout další prostředky. Další informace o práci s protokoly auditu najdete v tématu [Audit operací pomocí Resource Manageru](../azure-resource-manager/resource-group-audit.md). Další informace o sledování mezipaměti Azure Redis událostí najdete v tématu [výstrahy a operace](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Řízení přístupu (IAM)

**Řízení přístupu (IAM)** oddíl poskytuje podporu pro řízení přístupu na základě rolí (RBAC) na webu Azure Portal. Tato konfigurace pomáhá organizacím vyhovuje jejich požadavkům na řízení přístupu, jednoduše a přesně. Další informace najdete v tématu [řízení přístupu na základě rolí na portálu Azure portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Značky

**Značky** část vám pomůže organizovat vašich prostředků. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostikovat a řešit problémy

Klikněte na tlačítko **diagnostikovat a řešit problémy** poskytnuty častých problémů a strategie pro jejich řešení.



## <a name="settings"></a>Nastavení
**Nastavení** část umožňuje přístup k a nakonfigurujte následující nastavení ke svojí mezipaměti.

* [Přístupové klíče](#access-keys)
* [Upřesňující nastavení](#advanced-settings)
* [Mezipaměť Azure pro Redis Advisor](#redis-cache-advisor)
* [Škálování](#scale)
* [Velikost clusteru redis](#cluster-size)
* [Trvalost dat Redis](#redis-data-persistence)
* [Plán aktualizací](#schedule-updates)
* [Geografická replikace](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brána firewall](#firewall)
* [Vlastnosti](#properties)
* [Zámky](#locks)
* [Automatizační skript](#automation-script)



### <a name="access-keys"></a>Přístupové klíče
Klikněte na tlačítko **přístupové klíče** k zobrazení nebo znovu vygenerovat přístupové klíče ke svojí mezipaměti. Tyto klíče se používají klienti připojení k mezipaměti.

![Mezipaměť Azure pro přístupové klíče služby Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Upřesnit nastavení
Následující nastavení se konfigurují na **upřesňující nastavení** okno.

* [Přístupové porty](#access-ports)
* [Zásady paměti](#memory-policies)
* [Oznámení keyspace (rozšířené nastavení)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Přístupové porty
Přístup bez SSL je ve výchozím nastavení pro nové mezipaměti zakázaný. Pokud chcete povolit port bez SSL, klikněte na tlačítko **č** pro **povolit přístup jenom přes SSL** na **upřesňující nastavení** okna a pak klikněte na tlačítko **Uložit**.

> [!NOTE]
> Ve výchozím nastavení SSL přístup do mezipaměti Azure Redis podporuje protokol TLS 1.0. Minimální podporovaná verze TLS dá se zvýšit TLS 1.2 v případě potřeby pomocí **minimální verze protokolu TLS** rozevírací seznam pro **upřesňující nastavení** okna a pak klikněte na tlačítko **Uložit**.

![Azure mezipaměti Redis přístupové porty](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zásady paměti
**Zásady Maxmemory**, **vyhrazené maxmemory**, a **vyhrazené maxfragmentationmemory** nastavení na **upřesňující nastavení** okno Konfigurace zásad paměť pro ukládání do mezipaměti.

![Mezipaměť Azure Redis zásady Maxmemory.](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zásady Maxmemory** nakonfiguruje zásady vyřazení mezipaměti a umožňuje zvolit z následujících zásad vyřazení:

* `volatile-lru` – Toto je výchozí zásady vyřazení.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Další informace o `maxmemory` zásady, najdete v článku [zásady vyřazení](http://redis.io/topics/lru-cache#eviction-policies).

**Vyhrazené maxmemory** nastavení konfiguruje množství paměti v Megabajtech, který je vyhrazený pro operace nesouvisející s mezipamětí, jako je replikace během převzetí služeb při selhání. Pokud tuto hodnotu nastavíte umožňuje mít více konzistentní prostředí serveru Redis při zatížení se liší. Tato hodnota by měla být nastavená na vyšší pro úlohy, které jsou zápisu náročná na výkon. Když paměti je vyhrazen pro tyto operace, není k dispozici pro úložiště dat uložených v mezipaměti.

**Vyhrazené maxfragmentationmemory** nastavení konfiguruje množství paměti v Megabajtech, který je vyhrazen a vyřešit tak fragmentace paměti. Tuto hodnotu nastavíte umožňuje mít více konzistentní prostředí serveru Redis, když mezipaměť je plná nebo blízko úplné a fragmentaci je vysoký poměr. Když paměti je vyhrazen pro tyto operace, není k dispozici pro úložiště dat uložených v mezipaměti.

Jedna věc, které je třeba zvážit při výběru novou hodnotu rezervace paměti (**vyhrazené maxmemory** nebo **vyhrazené maxfragmentationmemory**) je, jak tato změna může ovlivnit mezipaměti, které už běží s velké objemy dat v něm. Například pokud jste 53 GB mezipaměti s 49 GB dat, pak změňte hodnotu rezervace na 8 GB, tato změna vyřadit maximální dostupnou paměť pro systém až 45 GB. Pokud vaše aktuální `used_memory` nebo `used_memory_rss` hodnoty jsou vyšší než nové omezení 45 GB, pak systém bude mít vyřazení dat do obou `used_memory` a `used_memory_rss` jsou nižší než 45 GB. Vyřazení zvýšit fragmentace zatížení a paměti serveru. Další informace o mezipaměti metriky, jako `used_memory` a `used_memory_rss`, naleznete v tématu [dostupných metrik a vytváření sestav intervalech](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> **Vyhrazené maxmemory** a **vyhrazené maxfragmentationmemory** nastavení jsou dostupné jenom pro Standard a Premium ukládá do mezipaměti.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Oznámení keyspace (rozšířené nastavení)
Redis jsou oznámení nakonfigurovaná na prostor klíčů **upřesňující nastavení** okno. Oznámení keyspace klientům povolit příjem oznámení při výskytu určitých událostí.

![Mezipaměť Azure pro Redis upřesňující nastavení](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Oznámení keyspace a **oznámení události keyspace** nastavení jsou dostupná jenom pro Standard a Premium mezipamětí.
> 
> 

Další informace najdete v tématu [Redis oznámení Keyspace](http://redis.io/topics/notifications). Ukázkový kód, naleznete v tématu [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) soubor [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) vzorku.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Mezipaměť Azure pro Redis Advisor
**Mezipaměti Azure redis Cache Advisoru** okně zobrazí doporučení ke svojí mezipaměti. Při běžném provozu se žádná doporučení týkající se zobrazují. 

![Doporučení](./media/cache-configure/redis-cache-no-recommendations.png)

Pokud všechny podmínky, ke kterým došlo během operace vaší mezipaměti, jako je například využití velkého množství paměti a šířky pásma sítě, zatížení serveru, zobrazí se upozornění na **mezipaměti Azure Redis** okno.

![Doporučení](./media/cache-configure/redis-cache-recommendations-alert.png)

Další informace najdete v **doporučení** okno.

![Doporučení](./media/cache-configure/redis-cache-recommendations.png)

Tyto metriky můžete sledovat na [grafy monitorování](cache-how-to-monitor.md#monitoring-charts) a [grafy využití](cache-how-to-monitor.md#usage-charts) oddíly **mezipaměti Azure Redis** okno.

Každá cenová úroveň má jiné omezení pro připojení klientů, paměti a šířky pásma. Pokud vaše mezipaměť blíží maximální kapacity pro tyto metriky za delší dobu, vytvoří se doporučení. Další informace o metriky a zkontroluje omezení **doporučení** nástroj, najdete v následující tabulce:

| Mezipaměť Azure Redis metriky | Další informace |
| --- | --- |
| Využití šířky pásma sítě |[Výkon mezipaměti - dostupnou šířku pásma](cache-faq.md#cache-performance) |
| Připojení klienti |[Výchozí konfigurace serveru Redis - maxclients](#maxclients) |
| Zatížení serveru |[Použití grafů – zatížení serveru Redis](cache-how-to-monitor.md#usage-charts) |
| Využití paměti |[Výkon mezipaměti – velikost](cache-faq.md#cache-performance) |

Aktualizace mezipaměti, klikněte na tlačítko **upgradovat** Chcete-li změnit cenovou úroveň a [škálování](#scale) mezipaměti. Další informace o volbě cenové úrovně, naleznete v tématu [pro velikost a kterou nabídku Redis jaké Azure Cache mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Měřítko
Klikněte na tlačítko **škálování** chcete zobrazit nebo změnit cenovou úroveň pro mezipaměť. Další informace o škálování najdete v tématu [postup škálování Azure mezipaměti Redis](cache-how-to-scale.md).

![Mezipaměť Azure pro Redis cenová úroveň](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Velikost clusteru redis
Klikněte na tlačítko **velikost clusteru Redis (PREVIEW)** ke změně velikosti clusteru pro spuštěný mezipaměť premium s aktivovaným clusteringem.

> [!NOTE]
> Všimněte si, že při ukládání do mezipaměti Azure redis Cache Premium úrovně je ve verzi všeobecné dostupnosti, velikost clusteru Redis funkce je momentálně ve verzi preview.
> 
> 

![Velikost clusteru redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Chcete-li změnit velikost clusteru pomocí posuvníku nebo zadejte číslo z rozsahu 1 až 10 v **počet horizontálních oddílů** textového pole a klikněte na tlačítko **OK** uložte.

> [!IMPORTANT]
> Redis clustering je k dispozici pouze pro úrovni Premium. Další informace najdete v tématu [postup konfigurace clusterů pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Trvalost dat Redis
Klikněte na tlačítko **trvalost dat Redis** Pokud chcete povolit, zakázat nebo nakonfigurovat data trvalost pro mezipaměť premium. Mezipaměti Redis Azure nabízí trvalost Redis pomocí [RDB trvalost](cache-how-to-premium-persistence.md#configure-rdb-persistence) nebo [AOF trvalost](cache-how-to-premium-persistence.md#configure-aof-persistence).

Další informace najdete v tématu [konfigurace trvalosti pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trvalost dat redis je dostupná jenom pro úrovni Premium.
> 
> 

### <a name="schedule-updates"></a>Plán aktualizací
**Naplánovat aktualizace** okno umožňuje určit časové období údržby pro aktualizace serveru Redis ke svojí mezipaměti. 

> [!IMPORTANT]
> Časové období údržby platí jenom pro Redis serveru aktualizace a ne Azure aktualizací nebo aktualizace operačního systému virtuálních počítačů, které jsou hostiteli mezipaměti.
> 
> 

![Plán aktualizací](./media/cache-configure/redis-schedule-updates.png)

Zadejte časové období údržby, zkontrolujte požadované dnů a zadat počáteční hodina časového období údržby pro každý den a klikněte na tlačítko **OK**. Časového období údržby se ve standardu UTC.

> [!IMPORTANT]
> **Naplánovat aktualizace** funkce je dostupná jenom pro mezipaměť na úrovni Premium. Další informace a pokyny najdete v tématu [mezipaměti Azure pro správu Redis - naplánovat aktualizace](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Geografická replikace

**Geografickou replikaci** okno poskytuje mechanismus pro propojení dvou úroveň Premium mezipaměti Azure pro instance Redis. Jeden mezipaměti je určený jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Propojené sekundární mezipaměť bude jen pro čtení a data zapsaná do primární mezipaměti se replikuje do sekundární propojené mezipaměti. Tuto funkci je možné replikovat do mezipaměti v různých oblastech Azure.

> [!IMPORTANT]
> **Geografická replikace** je dostupná jenom pro mezipaměť na úrovni Premium. Další informace a pokyny najdete v tématu [konfigurace geografické replikace pro Azure Cache pro Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
**Virtuální sítě** oddíl umožňuje konfigurovat nastavení virtuální sítě ke svojí mezipaměti. Další informace o vytváření cache ve verzi premium s virtuální sítí podporují a aktualizuje jeho nastavení, najdete v článku [jak provést podporu virtuální sítě pro Azure Cache úrovně Premium pro Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Nastavení virtuální sítě jsou dostupné jenom pro úrovně premium mezipamětí, které byly nakonfigurovány s podporou virtuální sítě během vytváření mezipaměti. 
> 
> 

### <a name="firewall"></a>Brána firewall

Konfigurace pravidel brány firewall je k dispozici pro všechny mezipaměti Azure Redis úrovní.

Klikněte na tlačítko **brány Firewall** k zobrazení a konfigurace pravidel brány firewall pro mezipaměť.

![Brána firewall](./media/cache-configure/redis-firewall-rules.png)

Zadejte pravidla brány firewall s počáteční a koncový rozsah IP adres. Když jsou nakonfigurovaná pravidla brány firewall, můžete připojit pouze připojení klientů z zadané rozsahy IP adres do mezipaměti. Při uložení pravidlo brány firewall, je prodleva před pravidlo platí. Toto zpoždění je obvykle méně než jedna minuta.

> [!IMPORTANT]
> Připojení z mezipaměti Azure Redis systémy pro monitorování jsou vždy povoleny, i když jsou nakonfigurovaná pravidla brány firewall.
> 
> 

### <a name="properties"></a>Vlastnosti
Klikněte na tlačítko **vlastnosti** zobrazíte informace o mezipaměti, včetně koncový bod mezipaměti a porty.

![Mezipaměť Azure Redis vlastností](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zámky
**Uzamkne** část umožňuje zamknout předplatné, skupinu prostředků nebo prostředek Neumožnit dalším uživatelům ve vaší organizaci omylem odstranit nebo upravit důležitých prostředků. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automatizační skript

Klikněte na tlačítko **automatizační skript** k sestavení a exportovat šablonu vaše nasazené prostředky pro budoucí nasazení. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Nastavení správy
Nastavení **správu** části umožňují provádět následující úlohy správy ke svojí mezipaměti. 

![Správa](./media/cache-configure/redis-cache-administration.png)

* [Import dat](#importexport)
* [Export dat](#importexport)
* [Restartování](#reboot)


### <a name="importexport"></a>Import/export
Import/Export je Azure Cache pro operace správy dat Redis, který umožňuje importovat a exportovat data v mezipaměti na základě importu a exportu Azure Cache snímku Redis databáze (RDB) z mezipaměti úrovně premium pro objekt blob stránky v účtu služby Azure Storage. Import/Export umožňuje migraci mezi různé mezipaměti Azure pro instance Redis nebo naplňte mezipaměť daty před použitím.

Import je možné uvést kompatibilní soubory RDB Redis z jakéhokoli serveru Redis spuštěná v jakémkoli cloudu nebo prostředí, včetně Redis běžící na Linuxu, Windows nebo libovolného poskytovatele cloudových služeb, jako je například Amazon Web Services a další. Import dat je snadný způsob, jak vytvořit mezipaměť s předem naplněných daty. Během procesu importu mezipaměti Azure Redis načte soubory RDB ze služby Azure storage do paměti a poté vloží klíčů do mezipaměti.

Export umožňuje exportovat data uložená v mezipaměti Azure Redis k Redis kompatibilní soubory RDB. Tato funkce slouží k přesunu dat z jedné mezipaměti Azure pro instanci Redis do jiné nebo na jiný server Redis. Během procesu exportu dočasný soubor vytvořen na virtuálním počítači, který je hostitelem ukládání do mezipaměti Azure pro instanci serveru Redis a se soubor nahraje do účtu úložiště určený. Po dokončení operace exportu se stavem úspěch nebo neúspěch dočasný soubor bude odstraněn.

> [!IMPORTANT]
> Import/Export dostupná jenom pro mezipaměť na úrovni Premium. Další informace a pokyny najdete v tématu [importovat a exportovat data v mezipaměti Azure Redis](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Restartování
**Restartování** okno umožňuje restartovat uzly mezipaměti. Tato možnost restartování umožňuje testovat aplikaci pro odolnost proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-configure/redis-cache-reboot.png)

Pokud máte cache ve verzi premium s aktivovaným clusteringem, můžete vybrat které horizontální oddíl mezipaměti až po restartování.

![Restartování](./media/cache-configure/redis-cache-reboot-cluster.png)

Chcete restartovat jeden nebo více uzly mezipaměti, vyberte požadované uzly a klikněte na tlačítko **restartování**. Pokud máte cache ve verzi premium s aktivovaným clusteringem, vyberte změnou počtu horizontálních oddílů, restartujte počítač a potom klikněte na **restartování**. Po několika minutách se restartování vybraných uzlů a jsou zpět do režimu online později za několik minut.

> [!IMPORTANT]
> Restartování je nyní k dispozici pro všechny cenové úrovně. Další informace a pokyny najdete v tématu [restartování mezipaměti Azure pro správu Redis -](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorování

**Monitorování** oddíl umožňuje konfigurovat Diagnostika a sledování pro mezipaměť Azure Redis. Další informace o mezipaměti Azure Redis monitorování a Diagnostika, naleznete v tématu [monitorování služby Azure Cache pro Redis](cache-how-to-monitor.md).

![Diagnostika](./media/cache-configure/redis-cache-diagnostics.png)

* [Metrika mezipaměti redis](#redis-metrics)
* [Pravidla upozornění](#alert-rules)
* [Diagnostika](#diagnostics)

### <a name="redis-metrics"></a>Metrika mezipaměti Redis
Klikněte na tlačítko **metrika mezipaměti Redis** k [zobrazujte metriky](cache-how-to-monitor.md#view-cache-metrics) ke svojí mezipaměti.

### <a name="alert-rules"></a>Pravidla upozornění

Klikněte na tlačítko **pravidla upozornění** ke konfiguraci upozornění založená na mezipaměti Azure Redis metriky. Další informace najdete v tématu [výstrahy](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostika

Ve výchozím nastavení, metriky mezipaměti ve službě Azure Monitor jsou [uchovávají po dobu 30 dní](../azure-monitor/platform/data-collection.md#metrics) a pak odstranit. Pokud chcete zachovat metriky mezipaměti po dobu delší než 30 dní, klikněte na tlačítko **diagnostiky** k [konfigurace účtu úložiště](cache-how-to-monitor.md#export-cache-metrics) používá k ukládání diagnostiku mezipaměti.

>[!NOTE]
>Kromě archivace metriky mezipaměti do úložiště, můžete také [Streamovat do centra událostí nebo odeslat do služby Log Analytics](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Podpora a řešení potíží nastavení
Nastavení **podpora a řešení potíží** části poskytují možnosti pro řešení problémů s mezipamětí.

![Podpora a řešení potíží](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Služba Resource health](#resource-health)
* [Nová žádost o podporu](#new-support-request)

### <a name="resource-health"></a>Stav prostředků
**Služba Resource health** sleduje váš prostředek a řekne vám, jestli funguje podle očekávání. Další informace o službě Azure Resource health najdete v tématu [přehled Azure Resource health](../resource-health/resource-health-overview.md).

> [!NOTE]
> Resource health je v současné době schopno provádět sestavy stavu mezipaměti Azure pro instance Redis hostované ve službě virtual network. Další informace najdete v tématu [všechny mezipaměti funkce fungují při hostování mezipaměti ve virtuální síti?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nová žádost o podporu
Klikněte na tlačítko **nová žádost o podporu** otevřete žádost o podporu ke svojí mezipaměti.





## <a name="default-redis-server-configuration"></a>Výchozí konfigurace serveru Redis
Novou mezipaměť Azure pro instance Redis jsou nakonfigurovány s následující výchozí Redis konfigurační hodnoty:

> [!NOTE]
> Nelze změnit nastavení v této části pomocí `StackExchange.Redis.IServer.ConfigSet` metody. Pokud tato metoda je volána s příkazy v této části, je vyvolána výjimka podobně jako v následujícím příkladu:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Všechny hodnoty, které se dají konfigurovat jako **maximální paměti zásad**, se dají konfigurovat prostřednictvím webu Azure portal nebo nástrojů příkazového řádku, jako je Azure CLI nebo Powershellu.
> 
> 

| Nastavení | Výchozí hodnota | Popis |
| --- | --- | --- |
| `databases` |16 |Výchozí počet databází, které je 16, ale můžete nakonfigurovat jiné číslo založené na cenové úrovni. <sup>1</sup> DB 0 je výchozí databáze, můžete vybrat jinou krychli na základě připojení pomocí `connection.GetDatabase(dbid)` kde `dbid` je číslo mezi `0` a `databases - 1`. |
| `maxclients` |Závisí na cenové úrovni<sup>2</sup> |Tato hodnota je maximální počet připojených klientů, které jsou povoleny ve stejnou dobu. Po dosažení limitu Redis zavře všechna nová připojení, a vrátí chybu bylo dosaženo maximálního počtu klientů. |
| `maxmemory-policy` |`volatile-lru` |Zásady Maxmemory jak Redis vybere, co se má odebrat, když je nastavení `maxmemory` dosažení (velikost mezipaměti nabídky vybrali při vytváření mezipaměti). S Azure mezipaměti Redis se ve výchozím nastavení je `volatile-lru`, která odebere klíče se vypršení platnosti nastavte pomocí algoritmu LRU. Toto nastavení můžete nakonfigurovat na webu Azure Portal. Další informace najdete v tématu [paměti zásady](#memory-policies). |
| `maxmemory-samples` |3 |Šetří paměť, minimální hodnota TTL algoritmy LRU a jsou přibližně algoritmy místo přesné algoritmy. Ve výchozím nastavení Redis tři klíče kontroly a vyskladnění ten, který byl použit méně nedávno. |
| `lua-time-limit` |5 000 |Maximální doba spuštění skript Lua v milisekundách. Při dosažení maximální doba spuštění Redis zaznamená, že skript je stále v provádění po maximální povolenou dobu a spustí odpovídání na dotazy s chybou. |
| `lua-event-limit` |500 |Maximální velikost fronty událostí skriptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Omezení vyrovnávací paměti výstupního klienta slouží k vynucení odpojení klienti, kteří nejsou čtení dat ze serveru dostatečně rychle z nějakého důvodu (běžným důvodem je, že klient Pub/Sub nemůže využívat zprávy tak rychle, jak vydavatele zapříčinit). Další informace najdete na adrese [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>limit `databases` se liší pro každý mezipaměti Azure Redis cenovou úroveň a můžete nastavit při vytváření mezipaměti. Pokud ne `databases` nastavení zadané během vytváření mezipaměti, výchozí hodnota je 16.

* Základní a standardní mezipamětí
  * C0 (250 MB) mezipaměti – až 16 databází
  * C1 mezipaměti (1 GB) – až 16 databází
  * C2 mezipaměti (2,5 GB) – až 16 databází
  * C3 mezipaměti (6 GB) – až 16 databází
  * C4 mezipaměti (13 GB) – až 32 databází
  * C5 mezipaměti (26 GB) – až 48 databází
  * C6 mezipaměti (53 GB) – až 64 databází
* Mezipaměti úrovně Premium
  * P1 (6 GB – 60 GB) – až 16 databází
  * P2 (13 GB 130 GB) – až 32 databází
  * P3 (26 GB – 260 GB) – až 48 databází
  * P4 (53 GB 530 GB) – až 64 databází
  * Všechny mezipaměti úrovně premium s clusterem Redis povoleny – Redis cluster podporuje pouze používání databáze 0 proto `databases` limit pro všechny úrovně premium mezipaměti s clusterem Redis povoleno je v podstatě 1 a [vyberte](http://redis.io/commands/select) není povolen příkaz. Další informace najdete v tématu [potřeba dělat žádné změny Moje klientská aplikace použít clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Další informace o databázích, naleznete v tématu [co jsou databáze Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases` Nastavení může být nakonfigurovaný jenom během vytváření mezipaměti a pouze pomocí prostředí PowerShell, CLI nebo jiných klientů pro správu. Příklad konfigurace `databases` během vytváření mezipaměti pomocí Powershellu, najdete v článku [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` se liší pro každý mezipaměti Azure Redis cenovou úroveň.

* Základní a standardní mezipamětí
  * C0 (250 MB) mezipaměti – až 256 připojení
  * C1 mezipaměti (1 GB) – až 1 000 připojení
  * C2 mezipaměti (2,5 GB) – až 2 000 připojení
  * C3 mezipaměti (6 GB) – až 5 000 připojení
  * C4 mezipaměti (13 GB) – až 10 000 připojení
  * C5 mezipaměti (26 GB) – až 15 000 připojení
  * C6 mezipaměti (53 GB) – až 20 000 připojení
* Mezipaměti úrovně Premium
  * P1 (6 GB – 60 GB) – až 7500 připojení
  * P2 (13 GB 130 GB) – až 15 000 připojení
  * P3 (26 GB – 260 GB) – až 30 000 připojení
  * P4 (53 GB 530 GB) – až 40 000 připojení

> [!NOTE]
> Zatímco jednotlivé velikosti mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis režie k němu má přidružené. Příkladem takových zatížení může být využití procesoru a paměti v důsledku šifrování TLS/SSL. Limit pro velikost daného mezipaměti maximální připojení předpokládá lehce načíst mezipaměti. Pokud načtení z připojení režii *plus* zatížení z klientské operace přesahuje kapacitu pro systém, mezipaměti může docházet potíže s kapacitou, i pokud nebyl překročen limit připojení pro aktuální velikost mezipaměti.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Příkazy není podporována pro Redis v mezipaměti Azure redis
> [!IMPORTANT]
> Vzhledem k tomu, že konfigurace a správy mezipaměti Azure pro instance Redis spravuje Microsoft, jsou zakázány následující příkazy. Pokud se pokusíte je vyvolat, se zobrazí chybová zpráva podobná `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * KONFIGURACE
> * LADĚNÍ
> * MIGRACE
> * ULOŽIT
> * VYPNUTÍ
> * SLAVEOF
> * CLUSTER - clusteru zápisu příkazy jsou zakázané, ale jen pro čtení clusteru příkazy nejsou povoleny.
> 
> 

Další informace o příkazech Redis najdete v tématu [ http://redis.io/commands ](http://redis.io/commands).

## <a name="redis-console"></a>Konzola redis
Příkazy můžete bezpečně posílat do mezipaměti Azure Redis instance pomocí **konzola Redis**, která je k dispozici na webu Azure Portal pro všechny úrovně mezipaměti.

> [!IMPORTANT]
> - Konzola Redis nebude fungovat s [VNET](cache-how-to-premium-vnet.md). Pokud vaše mezipaměť je součástí virtuální sítě, pouze klienti ve virtuální síti mají přístup k mezipaměti. Protože se spouští konzola Redis v prohlížeči místní, což je mimo virtuální síť, se nemůže připojit k vaší mezipaměti.
> - Ne všechny příkazy Redis podporují v Azure mezipaměti Redis. Seznam Redis příkazy, které jsou pro Azure Cache zakázán pro Redis, najdete v předchozím [Redis nepodporuje v mezipaměti Azure Redis příkazy](#redis-commands-not-supported-in-azure-redis-cache) oddílu. Další informace o příkazech Redis najdete v tématu [ http://redis.io/commands ](http://redis.io/commands).
> 
> 

Konzola Redis, klikněte na tlačítko **konzoly** z **mezipaměti Azure Redis** okno.

![Konzola redis](./media/cache-configure/redis-console-menu.png)

K vydávání příkazů proti vaší instance mezipaměti, zadejte požadovaný příkaz do konzoly.

![Konzola redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Pomocí konzoly Redis cache ve verzi premium v clusteru

Když pomocí konzoly Redis na úrovni premium v clusteru mezipaměti, můžete vydávat příkazy jeden horizontální oddíl mezipaměti. Vydat příkaz konkrétní horizontálních oddílů, nejprve připojí k požadované horizontálních oddílů kliknutím na výběr horizontálních oddílů.

![Konzola redis](./media/cache-configure/redis-console-premium-cluster.png)

Při pokusu o přístup k klíče uložené v rozdílných horizontálních oddílech než připojené horizontální oddíly, zobrazí se chybová zpráva podobná následující zpráva:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

V předchozím příkladu je horizontální oddíl 1 vybrané horizontálního oddílu, ale `myKey` se nachází v horizontálním oddílu 0, je určeno `(shard 0)` část chybové zprávy. V tomto příkladu, pro přístup k `myKey`, vyberte horizontálního oddílu 0 pomocí nástroje pro výběr horizontální oddíl a potom vydat požadovaného příkazu.


## <a name="move-your-cache-to-a-new-subscription"></a>Přesunutí mezipaměti do nového předplatného
Mezipaměti můžete přesunout do nového předplatného kliknutím **přesunout**.

![Přesuňte se mezipaměť Azure Redis](./media/cache-configure/redis-cache-move.png)

Informace o přesunutí prostředků z jedné skupiny prostředků do jiného a z jednoho předplatného do druhého, naleznete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Další postup
* Další informace o práci s příkazy Redis najdete v tématu [jak mohu spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)

