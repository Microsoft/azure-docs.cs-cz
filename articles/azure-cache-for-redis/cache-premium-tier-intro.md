---
title: Úvod do azure cache pro úroveň Redis Premium
description: Zjistěte, jak vytvořit a spravovat trvalost Redis, clustering Redis a podporu virtuální sítě pro vaše instance Azure Cache pro redis úrovně Premium.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74121667"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Úvod do azure cache pro úroveň Redis Premium
Azure Cache for Redis je distribuovaná spravovaná mezipaměť, která vám pomůže vytvářet vysoce škálovatelné a responzivní aplikace tím, že poskytuje super rychlý přístup k vašim datům. 

Nová úroveň Premium je úroveň připravenosti enterprise, která zahrnuje všechny funkce úrovně Standard a další, jako je lepší výkon, větší úlohy, zotavení po havárii, import/export a zvýšené zabezpečení. Pokračujte ve čtení a dozvíte se více o dalších funkcích úrovně mezipaměti Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepší výkon ve srovnání s úrovní Standard nebo Basic
**Lepší výkon než úroveň Standard nebo Basic.** Mezipaměti na úrovni Premium se nasazují na hardware, který má rychlejší procesory a poskytuje lepší výkon ve srovnání s úrovní Basic nebo Standard. Mezipaměť úrovně Premium mají vyšší propustnost a nižší latenci. 

**Propustnost pro stejnou velikost mezipaměti je vyšší v Premium ve srovnání s úrovní Standard.** Například propustnost mezipaměť 53 GB P4 (Premium) je 250 kB požadavků za sekundu ve srovnání s 150 kB pro C6 (standard).

Další informace o velikosti, propustnosti a šířce pásma s prémiovými mezipamětmi najdete v [tématu Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Trvalost dat Redis
Úroveň Premium umožňuje zachovat data mezipaměti v účtu Azure Storage. V mezipaměti Basic/Standard jsou všechna data uložena pouze v paměti. V případě problémů s základní infrastrukturou může dojít ke ztrátě dat. Doporučujeme používat funkci trvalost dat Redis ve vrstvě Premium ke zvýšení odolnosti proti ztrátě dat. Azure Cache for Redis nabízí možnosti RDB a AOF (již brzy) v [trvalosti Redis](https://redis.io/topics/persistence). 

Pokyny ke konfiguraci trvalosti najdete v [tématu Konfigurace trvalosti pro mezipaměť Azure Premium pro redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Pokud chcete vytvořit mezipaměti větší než 53 GB nebo chcete rozkládat data mezi více uzly Redis, můžete použít clustering Redis, který je k dispozici na úrovni Premium. Každý uzel se skládá z dvojice mezipaměti primární a repliky spravované Azure pro vysokou dostupnost. 

**Clustering Redis poskytuje maximální měřítko a propustnost.** Propustnost zvyšuje lineárně, jak zvýšíte počet úlomků (uzlů) v clusteru. Např. Pokud vytvoříte cluster P4 10 štřepů, pak je k dispozici propustnost 250 kB *10 = 2,5 milionu požadavků za sekundu. Další podrobnosti o velikosti, propustnosti a šířce pásma s prémiovými mezipamětmi najdete v nejčastějších dotazech k mezipaměti [Azure Cache for Redis.](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Pokud chcete začít s clusteringem, přečtěte si téma [Jak nakonfigurovat clustering pro prémiovou mezipaměť Azure pro Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Zvýšené zabezpečení a izolace
Mezipaměti vytvořené na úrovni Basic nebo Standard jsou přístupné na veřejném internetu. Přístup ke mezipaměti je omezen na základě přístupového klíče. S úrovní Premium můžete dále zajistit, že ke mezipaměti mají přístup pouze klienti v rámci zadané sítě. Azure Cache for Redis můžete nasadit ve [virtuální síti Azure (VNet).](https://azure.microsoft.com/services/virtual-network/) K dalšímu omezení přístupu k Redisu můžete použít všechny funkce sítě VNet, například podsítě, zásady řízení přístupu a další funkce.

Další informace najdete v tématu [Konfigurace podpory virtuální sítě pro mezipaměť Premium Azure pro redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/export
Import a export je operace správy dat Azure Cache for Redis, která umožňuje importovat data do azure cache pro Redis nebo exportovat data z Azure Cache for Redis importováním a exportováním snímku azure cache pro databázi Redis (RDB) z mezipaměti premium do objektu blob stránky v účtu úložiště Azure. To umožňuje migrovat mezi různými instancemi Azure Cache for Redis nebo naplnit mezipaměť daty před použitím.

Import lze použít k tomu, aby redis kompatibilní RDB soubory z libovolného serveru Redis běží v jakémkoli cloudu nebo prostředí, včetně Redis běží na Linuxu, Windows, nebo jakýkoli poskytovatel cloudu, jako je Amazon Web Services a další. Import dat je snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu Azure Cache for Redis načte soubory RDB z úložiště Azure do paměti a pak vloží klíče do mezipaměti.

Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redisem. Pomocí této funkce můžete přesunout data z jedné mezipaměti Azure pro instanci Redis na jiný nebo na jiný server Redis. Během procesu exportu se na virtuálním počítači, který je hostitelem instance serveru Azure Cache for Redis, vytvoří dočasný soubor a soubor se nahraje do určeného účtu úložiště. Po dokončení operace exportu se stavem úspěchu nebo neúspěchu je dočasný soubor odstraněn.

Další informace najdete v tématu [Jak importovat data do a exportovat data z Azure Cache for Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Restartování
Úroveň premium umožňuje restartovat jeden nebo více uzlů mezipaměti na vyžádání. To umožňuje otestovat odolnost aplikace v případě selhání. Můžete restartovat následující uzly.

* Hlavní uzel mezipaměti
* Sekundární uzel mezipaměti
* Primární i sekundární uzly mezipaměti
* Při použití prémiové mezipaměti s clusteringem můžete restartovat primární, sekundární nebo oba uzly pro jednotlivé oddíly v mezipaměti

Další informace naleznete v [tématu Nejčastější dotazy k restartování](cache-administration.md#reboot) a [restartování](cache-administration.md#reboot-faq).

>[!NOTE]
>Funkce restartování je teď povolená pro všechny úrovně Azure Cache pro Redis.
>
>

## <a name="schedule-updates"></a>Plán aktualizací
Funkce naplánovaných aktualizací umožňuje určit okno údržby mezipaměti. Po zadání okna údržby jsou během tohoto okna provedeny všechny aktualizace serveru Redis. Chcete-li určit okno údržby, vyberte požadované dny a zadejte počáteční hodinu okna údržby pro každý den. Všimněte si, že doba časového intervalu údržby je v utc. 

Další informace naleznete v [tématu Schedule updates](cache-administration.md#schedule-updates) and [Schedule updates FAQ](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Během okna plánované údržby jsou prováděny pouze aktualizace serveru Redis. Okno údržby se nevztahuje na aktualizace Azure nebo aktualizace operačního systému virtuálních aplikací.
> 
> 

## <a name="geo-replication"></a>Geografická replikace

**Geografická replikace** poskytuje mechanismus pro propojení dvou premium tier Azure Cache pro instance Redis. Jedna mezipaměť je označena jako primární propojená mezipaměť a druhá jako sekundární propojená mezipaměť. Sekundární propojená mezipaměť se stane jen pro čtení a data zapsaná do primární mezipaměti jsou replikována do sekundární propojené mezipaměti. Tuto funkci lze použít k replikaci mezipaměti napříč oblastmi Azure.

Další informace najdete v tématu [Jak nakonfigurovat geografickou replikaci pro Azure Cache for Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Škálování na úroveň premium
Chcete-li škálovat na úroveň premium, jednoduše zvolte jednu z úrovní premium v okně **Změnit úroveň ocenění.** Můžete také škálovat mezipaměť na úroveň premium pomocí PowerShell a CLI. Podrobné pokyny najdete v tématu [Jak škálovat azure mezipaměť pro Redis](cache-how-to-scale.md) a [jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Další kroky
Vytvořte mezipaměť a prozkoumejte nové funkce prémiové úrovně.

* [Jak nakonfigurovat trvalost pro premium Azure Cache pro Redis](cache-how-to-premium-persistence.md)
* [Jak nakonfigurovat podporu virtuální sítě pro prémiovou mezipaměť Azure pro Redis](cache-how-to-premium-vnet.md)
* [Jak nakonfigurovat clustering pro premium Azure Cache pro Redis](cache-how-to-premium-clustering.md)
* [Jak importovat data do a exportovat data z Azure Cache pro Redis](cache-how-to-import-export-data.md)
* [Jak spravovat Azure Cache pro Redis](cache-administration.md)

