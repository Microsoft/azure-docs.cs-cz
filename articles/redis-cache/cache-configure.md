---
title: Postup konfigurace Azure Redis Cache | Microsoft Docs
description: Rady pro pochopení výchozí konfiguraci Redis pro Azure Redis Cache a informace o konfiguraci vaší instance služby Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: 0cd21c0367a95d3e866137797ac32fc5bdd196c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31420718"
---
# <a name="how-to-configure-azure-redis-cache"></a>Postup konfigurace Azure Redis Cache
Toto téma popisuje konfigurace, které jsou k dispozici pro vaše instance služby Azure Redis Cache. Toto téma obsahuje také výchozí konfigurace serveru Redis pro instance služby Azure Redis Cache.

> [!NOTE]
> Další informace o konfiguraci a použití prémiových funkcí mezipaměti najdete v tématu [postup konfigurace trvalosti](cache-how-to-premium-persistence.md), [postup konfigurace clusterů](cache-how-to-premium-clustering.md), a [postup konfigurace podpory služby Virtual Network ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Konfigurace nastavení mezipaměti Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Nastavení Azure Redis Cache jsou zobrazit a konfigurovat na **Redis Cache** okno pomocí **prostředků nabídky**.

![Nastavení mezipaměti redis](./media/cache-configure/redis-cache-settings.png)

Můžete zobrazit a nakonfigurovat následující nastavení pomocí **prostředků nabídky**.

* [Přehled](#overview)
* [Protokol aktivit](#activity-log)
* [Řízení přístupu (IAM)](#access-control-iam)
* [Značky](#tags)
* [Diagnóza a řešení problémů](#diagnose-and-solve-problems)
* [Nastavení](#settings)
    * [Přístupové klávesy](#access-keys)
    * [Upřesnit nastavení](#advanced-settings)
    * [Advisor mezipaměti redis](#redis-cache-advisor)
    * [Škálování](#scale)
    * [Velikost clusteru redis](#cluster-size)
    * [Trvalosti dat redis](#redis-data-persistence)
    * [Plán aktualizací](#schedule-updates)
    * [Geografická replikace](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brána firewall](#firewall)
    * [Vlastnosti](#properties)
    * [Zámky.](#locks)
    * [Skriptu pro automatizaci](#automation-script)
* [Správa](#administration)
    * [Import dat](#importexport)
    * [Export dat](#importexport)
    * [Restartování](#reboot)
* [Monitorování](#monitoring)
    * [Metriky pro redis](#redis-metrics)
    * [Pravidla výstrah](#alert-rules)
    * [Diagnostika](#diagnostics)
* [Podporovat & řešení potíží s nastavení](#support-amp-troubleshooting-settings)
    * [Stav prostředků](#resource-health)
    * [Nová žádost o podporu](#new-support-request)


## <a name="overview"></a>Přehled

**Přehled** poskytuje k základní informace o mezipaměti, například název, porty, cenová úroveň a metriky vybrané mezipaměti.

### <a name="activity-log"></a>Protokol aktivit

Klikněte na tlačítko **protokol aktivit** zobrazíte akcích provedených v mezipaměti. Můžete také pomocí filtrování rozbalte toto zobrazení zahrnout další prostředky. Další informace o práci s protokoly auditu najdete v tématu [auditovat operace s Resource Managerem](../azure-resource-manager/resource-group-audit.md). Další informace o sledování událostí Azure Redis Cache najdete v tématu [operace a výstrahy](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Řízení přístupu (IAM)

**Přístup k ovládacímu prvku (IAM)** části poskytuje podporu pro řízení přístupu na základě role (RBAC) na portálu Azure. Tato konfigurace pomáhá organizacím, které splňují požadavky na správu jejich přístup, jednoduše a přesně. Další informace najdete v tématu [řízení přístupu na základě rolí na portálu Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Značky

**Značky** část umožňuje uspořádání prostředků. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostikovat a řešit problémy

Klikněte na tlačítko **Diagnostikujte a řešení problémů** poskytované s běžné problémy a strategie pro jejich řešení.



## <a name="settings"></a>Nastavení
**Nastavení** část vám pomůže přístup a nakonfigurujte následující nastavení pro mezipaměť.

* [Přístupové klávesy](#access-keys)
* [Upřesnit nastavení](#advanced-settings)
* [Advisor mezipaměti redis](#redis-cache-advisor)
* [Škálování](#scale)
* [Velikost clusteru redis](#cluster-size)
* [Trvalosti dat redis](#redis-data-persistence)
* [Plán aktualizací](#schedule-updates)
* [Geografická replikace](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brána firewall](#firewall)
* [Vlastnosti](#properties)
* [Zámky.](#locks)
* [Skriptu pro automatizaci](#automation-script)



### <a name="access-keys"></a>Přístupové klíče
Klikněte na tlačítko **přístupové klíče** k zobrazení nebo opětovné vygenerování přístupových klíčů pro mezipaměť. Tyto klíče používají připojení klientů k mezipaměti.

![Přístupové klíče mezipaměti redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Upřesnit nastavení
Následující nastavení se konfigurují na **upřesňující nastavení** okno.

* [Přístupové porty](#access-ports)
* [Zásady paměti](#memory-policies)
* [Oznámení keyspace (rozšířené nastavení)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Přístupové porty
Přístup bez SSL je ve výchozím nastavení pro nové mezipaměti zakázaný. Chcete-li povolit port bez SSL, klikněte na tlačítko **ne** pro **povolit přístup jen přes SSL** na **upřesňující nastavení** okna a potom klikněte na **Uložit**.

![Přístupové porty mezipaměti redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zásady paměti
**Maxmemory zásad**, **vyhrazené maxmemory**, a **vyhrazené maxfragmentationmemory** nastavení na **upřesňující nastavení** okno nakonfigurovat zásady paměť pro mezipaměť.

![Zásady Maxmemory mezipaměti redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zásady Maxmemory** nakonfiguruje zásady vyřazení mezipaměti a umožňuje zvolit z následujících zásad vyřazení:

* `volatile-lru` -Toto je výchozí zásady vyřazení.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Další informace o `maxmemory` zásady, najdete v části [zásady vyřazení](http://redis.io/topics/lru-cache#eviction-policies).

**Maxmemory vyhrazené** nastavení konfiguruje množství paměti, v MB, která je vyhrazena pro operace bez mezipaměti, jako je například replikace během převzetí služeb při selhání. Nastavení této hodnoty můžete mít více konzistentního prostředí serveru Redis, pokud se liší podle zatížení. Tato hodnota je třeba nastavit vyšší pro úlohy, které jsou zapsat náročné. Když paměti je vyhrazena pro takové operace, není k dispozici pro úložiště dat uložených v mezipaměti.

**Maxfragmentationmemory vyhrazené** nastavení konfiguruje množství paměti v MB, která je vyhrazena pro přizpůsobení pro fragmentace paměti. Nastavení této hodnoty můžete mít více konzistentního prostředí serveru Redis, pokud je mezipaměť plná nebo blízko úplné a fragmentaci poměr je vysoká. Když paměti je vyhrazena pro takové operace, není k dispozici pro úložiště dat uložených v mezipaměti.

Jednou z věcí vzít v úvahu při výběru novou hodnotu rezervace paměti (**vyhrazené maxmemory** nebo **vyhrazené maxfragmentationmemory**) je, jak tato změna může ovlivnit mezipaměti, která je již spuštěn s velké objemy dat v něm. Například pokud jste 53 GB mezipaměti s 49 GB dat, pak změňte hodnotu rezervace na 8 GB, tato změna bude vyřadit maximální dostupná paměť systému dolů 45 GB. Pokud buď vaše aktuální `used_memory` , vaše `used_memory_rss` hodnoty jsou vyšší, než nový limit 45 GB a pak systému bude mít vyřazení dat dokud `used_memory` a `used_memory_rss` jsou pod 45 GB. Vyřazení může zvýšit fragmentace zatížení a paměti serveru. Další informace o metrikách mezipaměti jako `used_memory` a `used_memory_rss`, najdete v části [k dostupným metrikám a vytváření sestav intervaly](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> **Vyhrazené maxmemory** a **maxfragmentationmemory vyhrazené** nastavení jsou dostupná jenom pro Standard a Premium ukládá do mezipaměti.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Oznámení keyspace (rozšířené nastavení)
Redis keyspace oznámení se konfigurují na **upřesňující nastavení** okno. Oznámení keyspace umožňují klientům přijímat upozornění, když dojde k určitým událostem.

![Upřesňující nastavení mezipaměti redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Oznámení keyspace a **oznámení události keyspace** nastavení jsou dostupná jenom pro Cache úrovní Standard a Premium.
> 
> 

Další informace najdete v tématu [Redis oznámení Keyspace](http://redis.io/topics/notifications). Ukázkový kód, najdete v článku [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) v soubor [Hello, world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) ukázka.


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor
**Redis Cache Advisor** zobrazuje doporučení pro mezipaměť. Během normálních operací zobrazí se žádná doporučení. 

![Doporučení](./media/cache-configure/redis-cache-no-recommendations.png)

Pokud jakékoliv podmínky, ke kterým došlo během operace svojí mezipaměti, jako je například velké množství paměti, šířky pásma sítě nebo zatížení serveru, upozornění se zobrazí na **Redis Cache** okno.

![Doporučení](./media/cache-configure/redis-cache-recommendations-alert.png)

Další informace naleznete na **doporučení** okno.

![Doporučení](./media/cache-configure/redis-cache-recommendations.png)

Tyto metriky můžete sledovat na [tabulek sledování](cache-how-to-monitor.md#monitoring-charts) a [využití grafy](cache-how-to-monitor.md#usage-charts) části **Redis Cache** okno.

Každá cenová úroveň má jiné limity pro připojení klientů, paměti a šířky pásma. Pokud vaše mezipaměť blíží maximální kapacity pro tyto metriky po dobu delší dobu, vytvoří se doporučení. Další informace o metriky a omezení zkontrolovány uživatelem **doporučení** nástroj, najdete v následující tabulce:

| Metrika mezipaměti redis | Další informace |
| --- | --- |
| Využití šířky pásma sítě |[Výkon mezipaměti - dostupnou šířku pásma](cache-faq.md#cache-performance) |
| Připojení klienti |[Výchozí konfigurace serveru Redis - maxclients](#maxclients) |
| Zatížení serveru |[Použití grafů – zatížení serveru Redis](cache-how-to-monitor.md#usage-charts) |
| Využití paměti |[Výkon mezipaměti - velikost](cache-faq.md#cache-performance) |

Chcete-li upgradovat mezipaměti, klikněte na tlačítko **upgradovat nyní** změnit cenovou úroveň a [škálování](#scale) svojí mezipaměti. Další informace o výběru cenovou úroveň, najdete v části [jaké mezipaměť Redis nabídky a velikosti použít?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Měřítko
Klikněte na tlačítko **škálování** chcete zobrazit nebo změnit jeho cenovou úroveň mezipaměti. Další informace o škálování najdete v tématu [postup škálování Azure Redis Cache](cache-how-to-scale.md).

![Cenová úroveň mezipaměti redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Velikost clusteru redis
Klikněte na tlačítko **velikost clusteru Redis (PREVIEW)** Chcete-li změnit velikost clusteru pro spuštěný premium mezipaměti s povoleným clusteringem.

> [!NOTE]
> Všimněte si, že při obecné dostupnosti bylo vydáno Azure Redis Cache na úrovni Premium, velikost clusteru Redis funkce je aktuálně ve verzi preview.
> 
> 

![Velikost clusteru redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Chcete-li změnit velikost clusteru, posuvníkem nebo zadejte číslo mezi 1 a 10 v **počet horizontálních** textového pole a klikněte na tlačítko **OK** uložit.

> [!IMPORTANT]
> Redis clustering je dostupná jenom pro prémiových mezipamětí. Další informace najdete v článku [Postup konfigurace clusterů pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Trvalost dat Redis
Klikněte na tlačítko **trvalosti dat Redis** povolit, zakázat nebo konfigurace trvalosti dat pro mezipaměť premium. Azure Redis Cache nabízí buď pomocí trvalosti Redis [RDB trvalost](cache-how-to-premium-persistence.md#configure-rdb-persistence) nebo [AOF trvalost](cache-how-to-premium-persistence.md#configure-aof-persistence).

Další informace najdete v tématu [postup konfigurace trvalosti pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trvalosti dat redis je dostupná jenom pro prémiových mezipamětí. 
> 
> 

### <a name="schedule-updates"></a>Plán aktualizací
**Naplánovat aktualizace** okno umožňuje určit časové období údržby pro aktualizace serveru Redis ke svojí mezipaměti. 

> [!IMPORTANT]
> Pro správu a údržbu platí jenom pro Redis serveru aktualizací a tak, aby všechny Azure aktualizace nebo aktualizace operačního systému virtuálních počítačů, které jsou hostiteli mezipaměti.
> 
> 

![Plán aktualizací](./media/cache-configure/redis-schedule-updates.png)

Zadejte časové období údržby, zkontrolujte požadované dny a zadejte hodina spouštění údržby okna pro každý den a klikněte na tlačítko **OK**. Všimněte si, že časového období údržby se ve standardu UTC. 

> [!IMPORTANT]
> **Naplánovat aktualizace** funkce je dostupná jenom pro prémiových mezipamětí vrstvy. Další informace a pokyny najdete v tématu [správy Azure Redis Cache - naplánovat aktualizace](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Geografická replikace

**Geografická replikace** okno poskytuje mechanismus pro propojení dvě instance vrstvy Azure Redis Cache Premium. Jeden mezipaměti je určený jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Sekundární propojené mezipaměti se stane, jen pro čtení a data zapsat do mezipaměti. primární se replikují do sekundární propojené mezipaměti. Tato funkce slouží k replikaci mezipaměti mezi oblastmi Azure.

> [!IMPORTANT]
> **Geografická replikace** je dostupná jenom u prémiových mezipamětí vrstvy. Další informace a pokyny najdete v tématu [konfiguraci geografická replikace pro Azure Redis Cache](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
**Virtuální sítě** část vám pomůže nakonfigurovat nastavení virtuální sítě pro mezipaměť. Informace týkající se vytváření cache ve verzi premium se virtuální síť podporovat a aktualizuje jeho nastavení, najdete v tématu [konfiguraci virtuální sítě podporu pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Nastavení virtuální sítě jsou dostupné pouze pro prémiových mezipamětí, které byly nakonfigurovány s podporou virtuální sítě během vytváření mezipaměti. 
> 
> 

### <a name="firewall"></a>Brána firewall

Konfigurace pravidel brány firewall je k dispozici pro všechny úrovně Azure Redis Cache.

Klikněte na tlačítko **brány Firewall** k zobrazení a konfigurace pravidel brány firewall pro mezipaměť.

![Brána firewall](./media/cache-configure/redis-firewall-rules.png)

Pravidla brány firewall můžete zadat s počáteční a koncové rozsah IP adres. Pokud jsou nakonfigurovaná pravidla brány firewall, připojení klienta pouze z zadaných rozsazích IP adres může připojit k mezipaměti. Při uložení pravidlo brány firewall není chvíli trvat, než pravidlo je platná. Toto opoždění je obvykle méně než jedna minuta.

> [!IMPORTANT]
> Připojení z Azure Redis Cache monitorování systémů jsou vždy povoleny, i když jsou nakonfigurovaná pravidla brány firewall.
> 
> 

### <a name="properties"></a>Vlastnosti
Klikněte na tlačítko **vlastnosti** zobrazíte informace o mezipaměti, včetně koncový bod mezipaměti a portů.

![Vlastnosti mezipaměti redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zámky
**Zamkne** části umožňuje zamknout předplatné, skupinu prostředků nebo prostředek zabránit ostatním uživatelům ve vaší organizaci neúmyslnému odstranění nebo úprava důležitých prostředků. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automatizační skript

Klikněte na tlačítko **skriptu pro automatizaci** sestavení a exportovat šablonu vaše nasazené prostředky pro budoucí nasazení. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablony Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Nastavení správy
Nastavení v **správy** části umožňují provádět následující úlohy správy pro mezipaměť. 

![Správa](./media/cache-configure/redis-cache-administration.png)

* [Import dat](#importexport)
* [Export dat](#importexport)
* [Restartování](#reboot)


### <a name="importexport"></a>Import/export
Import a Export je operace Azure Redis Cache dat správy, který umožňuje importovat a exportovat data v mezipaměti import a Export snímku databáze Redis Cache (RDB) z mezipaměti premium do objektů blob stránky v účtu úložiště Azure. Import a Export umožňuje migraci mezi různými instancemi Azure Redis Cache nebo naplnění mezipaměti s daty před použitím.

Import umožňuje přinést si Redis kompatibilní RDB soubory z jakéhokoli Redis serveru se systémem v libovolném cloudu nebo prostředí, včetně Redis systémem Linux, Windows nebo kteréhokoli poskytovatele cloudových služeb jako Amazon Web Services a dalších. Import dat je snadný způsob, jak vytvořit mezipaměti s předem vyplněná data. Během procesu importu Azure Redis Cache načte RDB soubory z úložiště Azure do paměti a vloží klíčů do mezipaměti.

Export umožňuje exportovat data uložená ve službě Azure Redis Cache k Redis kompatibilní soubory RDB. Tato funkce slouží pro přesun dat z jedné instance Azure Redis Cache do jiné nebo jinému serveru Redis. Během procesu exportu ve virtuálním počítači, který je hostitelem instance serveru Azure Redis Cache je vytvořit dočasný soubor a soubor je odeslán k účtu úložiště určený. Po dokončení operace exportu se stavem úspěch nebo selhání dočasný soubor bude odstraněn.

> [!IMPORTANT]
> Import a Export je dostupná jenom pro prémiových mezipamětí vrstvy. Další informace a pokyny najdete v tématu [importu a exportu dat ve službě Azure Redis Cache](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Restartování
**Restartovat** okno umožňuje restartovat uzly mezipaměti. Tato možnost restartování umožňuje otestovat aplikaci pro odolnost proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-configure/redis-cache-reboot.png)

Pokud máte s povoleným clusteringem cache ve verzi premium, můžete vybrat které horizontálních oddílů mezipaměti restartovat.

![Restartování](./media/cache-configure/redis-cache-reboot-cluster.png)

Restartovat jeden nebo více uzlů svojí mezipaměti, vyberte požadovaný uzel a klikněte na tlačítko **restartovat**. Pokud máte cache ve verzi premium s povoleným clusteringem, vyberte shard(s) restartovat a pak klikněte na tlačítko **restartovat**. Po několika minutách restartování vybrané uzly a jsou zpět do režimu online několik minut později.

> [!IMPORTANT]
> Restart je nyní k dispozici pro všechny cenové úrovně. Další informace a pokyny najdete v tématu [správy Azure Redis Cache - restartování](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorování

**Monitorování** část vám pomůže nakonfigurovat monitorování pro mezipaměť Redis Cache a diagnostiky. Další informace o Azure Redis Cache monitorování a Diagnostika, najdete v části [jak monitorovat Azure Redis Cache](cache-how-to-monitor.md).

![Diagnostika](./media/cache-configure/redis-cache-diagnostics.png)

* [Metriky pro redis](#redis-metrics)
* [Pravidla výstrah](#alert-rules)
* [Diagnostika](#diagnostics)

### <a name="redis-metrics"></a>Metrika mezipaměti Redis
Klikněte na tlačítko **Redis metriky** k [metriky zobrazit](cache-how-to-monitor.md#view-cache-metrics) ke svojí mezipaměti.

### <a name="alert-rules"></a>Pravidla upozornění

Klikněte na tlačítko **výstrah pravidla** můžete konfigurovat upozornění na základě metriky pro Redis Cache. Další informace najdete v tématu [výstrahy](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostika

Ve výchozím nastavení, mezipaměti metriky v Azure monitorování jsou [uchovávají po dobu 30 dnů](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) a odstraní. Chcete-li zachovat metriky vaší mezipaměti po dobu delší než 30 dní, klikněte na tlačítko **diagnostiky** k [konfigurace účtu úložiště](cache-how-to-monitor.md#export-cache-metrics) používá k ukládání diagnostiku mezipaměti.

>[!NOTE]
>Kromě archivace vaše mezipaměť metriky pro úložiště, můžete také [stream je do centra událostí nebo poslat k analýze protokolů](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

## <a name="support--troubleshooting-settings"></a>Podporovat & řešení potíží s nastavení
Nastavení v **podpory a řešení potíží s** části poskytují možnosti pro řešení problémů s mezipamětí.

![Podpora a řešení potíží](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Stav prostředků](#resource-health)
* [Nová žádost o podporu](#new-support-request)

### <a name="resource-health"></a>Stav prostředků
**Stav prostředku** sleduje prostředku a zjistíte, zda je spuštěn podle očekávání. Další informace o službě stavu prostředků Azure najdete v tématu [přehled stavu prostředků Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Stav prostředku je momentálně nelze vytvořit sestavu stavu instance služby Azure Redis Cache hostované ve virtuální síti. Další informace najdete v tématu [všechny funkce mezipaměti fungují při hostování mezipaměti ve virtuální síti?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nová žádost o podporu
Klikněte na tlačítko **nová žádost o podporu** otevřete žádost o podporu pro mezipaměť.





## <a name="default-redis-server-configuration"></a>Výchozí konfigurace serveru Redis
Nové instance služby Azure Redis Cache jsou nakonfigurované s následující výchozí hodnoty Redis konfigurace:

> [!NOTE]
> Nastavení v této části nelze změnit pomocí `StackExchange.Redis.IServer.ConfigSet` metoda. Pokud tato metoda je volána s příkazy v této části, je vyvolána výjimka podobně jako v následujícím příkladu:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Všechny hodnoty, které se dají konfigurovat jako **max paměti policy**, se dají konfigurovat prostřednictvím portálu Azure nebo nástroje příkazového řádku pro správu například Azure CLI nebo prostředí PowerShell.
> 
> 

| Nastavení | Výchozí hodnota | Popis |
| --- | --- | --- |
| `databases` |16 |Výchozí počet databází je 16, ale můžete nakonfigurovat různé počty podle cenové úrovně. <sup>1</sup> výchozí databáze je databáze 0, můžete vybrat jinou na základě jednotlivých připojení pomocí `connection.GetDatabase(dbid)` kde `dbid` je číslo v rozsahu od `0` a `databases - 1`. |
| `maxclients` |Závisí na cenovou úroveň<sup>2</sup> |Tato hodnota je maximální počet připojených klientů, které jsou povoleny ve stejnou dobu. Po dosažení limitu Redis zavře všechna nová připojení, a vrátí chybu bylo dosaženo maximální počet klientů. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory zásady je nastavení pro jak Redis vybere co při odebrání `maxmemory` je dosaženo (velikost mezipaměti nabídky, jste vybrali při vytvoření mezipaměti). S Azure Redis Cache ve výchozím nastavení je `volatile-lru`, které odebere klíče s vypršení platnosti nastavit pomocí algoritmu hodnoty nejdelšího Nepoužití. Toto nastavení můžete nakonfigurovat na portálu Azure. Další informace najdete v tématu [paměti zásady](#memory-policies). |
| `maxmemory-samples` |3 |Uložit paměti, jsou přibližně algoritmy místo přesné algoritmy LRU a minimální hodnota TTL algoritmy. Ve výchozím nastavení Redis tři klíče kontroly a vyskladnění ten, který byl použit méně nedávno. |
| `lua-time-limit` |5 000 |Maximální doba provádění skriptu Lua v milisekundách. Pokud je dosaženo maximální dobu spuštění, zaprotokoluje Redis, je stále v provádění po maximální povolenou dobu skript a spustí odpoví na dotazy s chybou. |
| `lua-event-limit` |500 |Maximální velikost fronty událostí skriptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Omezení vyrovnávací paměti výstupní klienta lze vynutit odpojení klienti, kteří nejsou čtení dat ze serveru dostatečně rychle z nějakého důvodu (obvyklým důvodem je, že klient Pub nebo Sub nemůže využívat zprávy rychle, jak můžete vytvořit vydavatele, je). Další informace najdete na adrese [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>tento limit pro `databases` se liší pro každý Azure Redis Cache cenová úroveň a můžete nastavit při vytváření mezipaměti. Pokud žádné `databases` nastavení zadat během vytváření mezipaměti, výchozí hodnota je 16.

* Základní a standardní mezipaměti
  * C0 (250 MB) mezipaměti - až 16 databáze
  * C1 mezipaměti (1 GB) - až 16 databáze
  * C2 mezipaměti (2,5 GB) - až 16 databáze
  * C3 (6 GB) mezipaměti - až 16 databáze
  * C4 (13 GB) mezipaměti - až 32 databáze
  * C5 (26 GB) mezipaměti - až 48 databáze
  * C6 (53 GB) mezipaměti - až 64 databáze
* Ukládá do mezipaměti Premium
  * P1 (6 GB - 60 GB) - až 16 databáze
  * P2 (13 GB - 130 GB) – až 32 databáze
  * P3 (26 GB - 260 GB) – až 48 databáze
  * P4 (53 GB - 530 GB) – až 64 databáze
  * Všechny prémiových mezipamětí s clusterem Redis povoleny – Redis cluster podporuje jenom používání databáze 0 proto `databases` omezení pro všechny premium mezipaměť Redis clusteru povolena efektivně 1 a [vyberte](http://redis.io/commands/select) není povolen příkaz. Další informace najdete v tématu [muset provést jakékoli změny Moje aplikace klienta použít clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Další informace o databáze najdete v tématu [co jsou databáze Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases` Nastavení může být nakonfigurovaný jenom během vytváření mezipaměti a pouze pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiných klientů pro správu. Příklad konfigurace `databases` během vytváření mezipaměti pomocí prostředí PowerShell, najdete v části [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` se liší pro každý Azure Redis Cache cenová úroveň.

* Základní a standardní mezipaměti
  * C0 (250 MB) mezipaměti - až 256 připojení
  * C1 mezipaměti (1 GB) – až 1 000 připojení
  * C2 mezipaměti (2,5 GB) – až 2 000 připojení
  * C3 (6 GB) mezipaměti - až 5 000 připojení
  * C4 (13 GB) mezipaměti - až 10 000 připojení
  * C5 (26 GB) mezipaměti - až 15 000 připojení
  * C6 (53 GB) mezipaměti - až 20 000 připojení
* Ukládá do mezipaměti Premium
  * P1 (6 GB - 60 GB) – až 7500 připojení
  * P2 (13 GB - 130 GB) – až 15 000 připojení
  * P3 (26 GB - 260 GB) – až 30 000 připojení
  * P4 (53 GB - 530 GB) – až 40 000 připojení

> [!NOTE]
> Při každém velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má režie spojená s ním spojená. Příkladem takových režie může být využití procesoru a paměti v důsledku šifrování pomocí protokolu TLS/SSL. Připojení maximální limit pro velikost daného mezipaměti předpokládá lehkým načíst mezipaměti. Pokud načíst z režijní náklady na připojení *plus* zatížení z klientské operace překročí kapacitu pro systém, mezipaměti můžete mít problémy kapacitu, i pokud nebyl překročen limit připojení pro aktuální velikost mezipaměti.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis příkazy nejsou podporované ve službě Azure Redis Cache
> [!IMPORTANT]
> Vzhledem k tomu, že konfigurace a Správa instance služby Azure Redis Cache spravuje Microsoft, jsou zakázány následující příkazy. Pokud se pokusíte je vyvolat, zobrazí chybová zpráva podobná `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * KONFIGURACE
> * LADĚNÍ
> * MIGRACE
> * ULOŽIT
> * VYPNUTÍ
> * SLAVEOF
> * CLUSTER - clusteru zápisu, které jsou příkazy, ale jen pro čtení clusteru příkazy jsou povoleny.
> 
> 

Další informace o příkazech Redis najdete v tématu [ http://redis.io/commands ](http://redis.io/commands).

## <a name="redis-console"></a>Konzola redis
Bezpečně vydávat příkazy vaší instancí Azure Redis Cache pomocí **konzola Redis**, která je k dispozici na webu Azure portal pro všechny úrovně mezipaměti.

> [!IMPORTANT]
> - Konzola Redis nefunguje s [VNET](cache-how-to-premium-vnet.md). Pokud vaše mezipaměť je součástí virtuální sítě, jenom pro klienty ve virtuální síti přístup do mezipaměti. Protože se spouští konzola Redis v prohlížeči místní, což je mimo síť VNET, se nemůže připojit ke své mezipaměti.
> - Ne všechny příkazy Redis jsou podporovány ve službě Azure Redis Cache. Seznam Redis příkazy, které jsou pro Azure Redis Cache zakázán, najdete v předchozí [Redis příkazy nejsou podporované ve službě Azure Redis Cache](#redis-commands-not-supported-in-azure-redis-cache) části. Další informace o příkazech Redis najdete v tématu [ http://redis.io/commands ](http://redis.io/commands).
> 
> 

Přístup ke konzole Redis, klikněte na tlačítko **konzoly** z **Redis Cache** okno.

![Konzola redis](./media/cache-configure/redis-console-menu.png)

K vydání příkazů proti instance mezipaměti, jednoduše zadejte požadovaný příkaz do konzoly.

![Konzola redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Pomocí konzole Redis cache ve verzi premium clusteru

Když mezipaměť Redis konzoly pomocí prémiový clusteru, můžete vydávat příkazy jeden horizontálního oddílu mezipaměti. Příkaz pro konkrétní horizontálního oddílu, nejprve připojí k požadované horizontálního oddílu kliknutím na výběr horizontálního oddílu.

![Konzola redis](./media/cache-configure/redis-console-premium-cluster.png)

Pokud budete chtít získat přístup ke klíči, který je uložen v různých horizontálního oddílu než připojené horizontálního oddílu, zobrazí se chybová zpráva podobná následující zpráva:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

V předchozím příkladu je ID horizontálního oddílu 1 vybrané horizontálního oddílu, ale `myKey` se nachází v horizontálního oddílu 0, jak `(shard 0)` část chybové zprávy. V tomto příkladu se pro přístup k `myKey`, vyberte horizontálního oddílu 0 pomocí nástroje pro výběr horizontálního oddílu a potom vydat příkaz požadované.


## <a name="move-your-cache-to-a-new-subscription"></a>Vaše mezipaměť přesunout do nového předplatného
Mezipaměti můžete přesunout do nového předplatného kliknutím **přesunout**.

![Přesunout mezipaměti Redis](./media/cache-configure/redis-cache-move.png)

Informace o přesun prostředků z jedné skupiny prostředků do jiné a z jedno předplatné do druhého, najdete v části [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Další postup
* Další informace o práci s příkazy Redis najdete v tématu [jak můžete spouštět příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)

