---
title: Seznámení s mezipamětí Azure pro Redis úrovně Premium
description: Naučte se vytvářet a spravovat trvalost Redis, Redis Clustering a podporu virtuálních sítí pro Azure úrovně Premium pro instance Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74121667"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Seznámení s mezipamětí Azure pro Redis úrovně Premium
Azure cache pro Redis je distribuovaná spravovaná mezipaměť, která pomáhá vytvářet vysoce škálovatelné a reagující aplikace tím, že poskytuje velmi rychlý přístup k vašim datům. 

Nová úroveň Premium je úroveň pro podnik, která zahrnuje všechny funkce úrovně Standard a další, jako je lepší výkon, větší zatížení, zotavení po havárii, import/export a rozšířené zabezpečení. Pokud chcete získat další informace o dalších funkcích úrovně mezipaměti Premium, pokračujte ve čtení.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepší výkon v porovnání s úrovní Standard nebo Basic
**Lepší výkon než úroveň Standard nebo Basic.** Mezipaměti v úrovni Premium se nasazují na hardware, který má rychlejší procesory a poskytuje vyšší výkon v porovnání s úrovní Basic nebo Standard. Mezipaměti úrovně Premium mají vyšší propustnost a nižší latenci. 

**Propustnost pro stejnou velikost mezipaměti je vyšší v úrovni Premium, a to ve srovnání s úrovní Standard.** Například propustnost mezipaměti 53 GB P4 (Premium) je 250 tisíc požadavky za sekundu v porovnání s 150K pro C6 (Standard).

Další informace o velikosti, propustnosti a šířce pásma pomocí prémiových mezipamětí najdete v tématu [Nejčastější dotazy k Azure cache pro Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) .

## <a name="redis-data-persistence"></a>Trvalost dat Redis
Úroveň Premium vám umožňuje uchovávat data z mezipaměti v účtu Azure Storage. V mezipaměti Basic a Standard jsou všechna data uložena pouze v paměti. V případě základních problémů infrastruktury může dojít ke ztrátě dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci trvalosti dat Redis na úrovni Premium. Azure cache for Redis nabízí v [Redis Persistence](https://redis.io/topics/persistence)možnosti RDB a AOF (už brzy). 

Pokyny ke konfiguraci trvalosti najdete v tématu [Konfigurace trvalosti pro službu Azure cache Premium pro Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Pokud chcete vytvořit mezipaměti větší než 53 GB nebo chcete data horizontálních oddílů napříč více uzly Redis, můžete použít clustering Redis, který je k dispozici na úrovni Premium. Každý uzel se skládá z dvojice mezipaměti primárního/repliky spravované službou Azure pro zajištění vysoké dostupnosti. 

**Clustering Redis poskytuje maximální škálování a propustnost.** Propustnost se zvyšuje lineárně při zvýšení počtu horizontálních oddílů (uzlů) v clusteru. Např. Pokud vytvoříte cluster P4 s 10 horizontálních oddílů, je dostupná propustnost 250 tisíc * 10 = 2 500 000 požadavků za sekundu. Další podrobnosti o velikosti, propustnosti a šířce pásma pomocí prémiových mezipamětí najdete v tématu [Nejčastější dotazy k Azure cache pro Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) .

Pokud chcete začít s clustering, přečtěte si téma [Postup konfigurace clusteringu pro službu Azure cache Premium pro Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Rozšířené zabezpečení a izolace
Mezipaměti vytvořené v úrovni Basic nebo Standard jsou přístupné na veřejném Internetu. Přístup k mezipaměti je omezený na základě přístupového klíče. Úroveň Premium vám umožní zajistit, aby k této mezipaměti měli přístup jenom klienti v rámci zadané sítě. Mezipaměť Azure pro Redis můžete nasadit v [azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/). K dalšímu omezení přístupu k Redisu můžete použít všechny funkce sítě VNet, například podsítě, zásady řízení přístupu a další funkce.

Další informace najdete v tématu [jak nakonfigurovat Virtual Network podporu pro Azure cache Premium pro Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/export
Import/Export je mezipaměť Azure pro Redis správu dat, která umožňuje importovat data do mezipaměti Azure pro Redis nebo exportovat data z mezipaměti Azure pro Redis tím, že importuje a exportuje snímek Azure cache for Redis Database (RDB) z mezipaměti Premium do objektu blob stránky v účtu Azure Storage. To vám umožní migrovat mezi různými mezipamětí Azure pro instance Redis nebo naplnit mezipaměť daty před použitím.

Pomocí importu můžete přenést soubory RDB kompatibilní s Redis z libovolného serveru Redis běžícího v jakémkoli cloudu nebo prostředí, včetně Redis spuštěného v systému Linux, Windows nebo libovolného poskytovatele cloudu, jako je například Amazon Web Services a další. Import dat představuje snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu načte služba Azure cache pro Redis soubory RDB z Azure Storage do paměti a pak tyto klíče vloží do mezipaměti.

Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redis. Tuto funkci můžete použít k přesunu dat z jedné instance Azure cache pro instanci Redis do jiného nebo na jiný server Redis. Během procesu exportu se na virtuálním počítači, který hostuje instanci serveru Azure cache pro Redis, vytvoří dočasný soubor a soubor se nahraje do určeného účtu úložiště. Když se operace exportu dokončí buď se stavem úspěch, nebo neúspěchem, dočasný soubor se odstraní.

Další informace najdete v tématu [Jak importovat data do a exportovat data z Azure cache pro Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Restartování
Úroveň Premium vám umožní restartovat jeden nebo více uzlů vaší mezipaměti na vyžádání. To vám umožní testovat v aplikaci odolnost proti chybám v případě selhání. Můžete restartovat následující uzly.

* Hlavní uzel vaší mezipaměti
* Sekundární uzel vaší mezipaměti
* Primární i sekundární uzel vaší mezipaměti
* Pokud používáte mezipaměť Premium s clusteringem, můžete restartovat primární, sekundární nebo oba uzly pro jednotlivé horizontálních oddílůy v mezipaměti.

Další informace najdete v tématu Nejčastější dotazy k [restartování](cache-administration.md#reboot) a [restartu](cache-administration.md#reboot-faq).

>[!NOTE]
>Pro veškerou mezipaměť Azure pro vrstvy Redis je teď povolená funkce restartování.
>
>

## <a name="schedule-updates"></a>Plán aktualizací
Funkce naplánované aktualizace umožňuje určit časové období údržby pro mezipaměť. Po zadání časového období údržby se v průběhu tohoto okna provedou všechny aktualizace Redis serveru. Chcete-li určit časový interval pro správu a údržbu, vyberte požadované dny a zadejte časový interval pro správu a údržbu pro každý den. Všimněte si, že čas časového období údržby je UTC. 

Další informace najdete v tématu [Plánování aktualizací](cache-administration.md#schedule-updates) a [Plánování aktualizací v nejčastějších dotazech](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Během naplánovaných časových období údržby se provádí jenom aktualizace serveru Redis. Časové období údržby se nevztahuje na aktualizace nebo aktualizace Azure v operačním systému virtuálního počítače.
> 
> 

## <a name="geo-replication"></a>Geografická replikace

**Geografická replikace** poskytuje mechanismus pro propojení dvě mezipaměti Azure úrovně Premium pro instance Redis. Jedna mezipaměť je určena jako primární propojená mezipaměť a druhá jako sekundární propojená mezipaměť. Sekundární propojená mezipaměť se bude jen pro čtení a data zapsaná do primární mezipaměti se replikují do sekundární propojené mezipaměti. Tato funkce se dá použít k replikaci mezipaměti napříč oblastmi Azure.

Další informace najdete v tématu [Konfigurace geografické replikace pro Azure cache pro Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Postup škálování na úroveň Premium
Pokud chcete škálovat na úroveň Premium, jednoduše v okně **změnit cenovou úroveň** vyberte jednu z úrovní Premium. Mezipaměť můžete také škálovat na úroveň Premium pomocí PowerShellu a rozhraní příkazového řádku. Podrobné pokyny najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md) a [Jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Další kroky
Vytvořte mezipaměť a prozkoumejte nové funkce úrovně Premium.

* [Jak nakonfigurovat trvalost pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-persistence.md)
* [Jak nakonfigurovat Virtual Network podporu pro Azure cache Premium pro Redis](cache-how-to-premium-vnet.md)
* [Postup konfigurace clusteringu pro službu Azure cache v úrovni Premium pro Redis](cache-how-to-premium-clustering.md)
* [Import a export dat z Azure cache pro Redis](cache-how-to-import-export-data.md)
* [Jak spravovat službu Azure cache pro Redis](cache-administration.md)

