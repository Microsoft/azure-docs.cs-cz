---
title: Úroveň služeb Kritický pro podnikání
description: Informace o důležité podnikové kritické úrovni databáze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268675"
---
# <a name="business-critical-tier---azure-sql-database"></a>Kritická podniková úroveň – Azure SQL Database

> [!NOTE]
> Úroveň Business Critical se nazývá Premium v nákupním modelu DTU. Porovnání nákupního modelu založeného na virtuálních jádrech s nákupním modelem založeným na DTU najdete v [tématu Nákupní modely a prostředky azure SQL Database](sql-database-purchase-models.md).

Azure SQL Database je založen na architektuře SQL Server Database Engine, která je upravena pro cloudové prostředí, aby byla zajištěna 99,99% dostupnost i v případě selhání infrastruktury. Existují tři architektury modely, které se používají v Azure SQL Database:
- Obecný účel/standard 
- Kritické pro podnikání/prémiové
- Hyperškálování

Model úrovně služby Premium/Business Critical je založen na clusteru procesů databázového stroje. Tento architektonický model spoléhá na skutečnost, že vždy existuje kvorum uzly databázového stroje a má minimální vliv na výkon na vaše pracovní vytížení i během činností údržby.

Azure upgraduje a opravuje základní operační systém, ovladače a SQL Server Database Engine transparentně s minimálními prostoje pro koncové uživatele. 

Dostupnost premium je povolená v úrovních služeb Premium a Business Critical v Azure SQL Database a je navržena pro intenzivní úlohy, které nemohou tolerovat žádný dopad na výkon z důvodu probíhajících operací údržby.

V modelu premium Azure SQL databáze integruje výpočetní prostředky a úložiště na jednom uzlu. Vysoké dostupnosti v tomto architektonickém modelu je dosaženo replikací výpočetních prostředků (proces databázového stroje SQL Server) a úložiště (místně připojeného ssd) nasazeného ve čtyřech uzlových clusterech pomocí technologie podobné sql serveru [always on availability groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster uzlů databázového stroje](media/sql-database-managed-instance/business-critical-service-tier.png)

Proces databázového stroje SQL i podkladové soubory mdf/ldf jsou umístěny na stejném uzlu s místně připojeným úložištěm SSD, které poskytuje nízkou latenci úlohy. Vysoká dostupnost je implementována pomocí technologie podobné SQL Server [always on Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Každá databáze je cluster databázových uzlů s jednou primární databází, která je přístupná pro zatížení zákazníka, a třemi sekundárními procesy obsahujícími kopie dat. Primární uzel neustále tlačí změny do sekundárních uzlů, aby bylo zajištěno, že data jsou k dispozici na sekundární repliky, pokud primární uzel dojde k chybě z jakéhokoli důvodu. Převzetí služeb při selhání je zpracována SQL Server Database Engine – jedna sekundární replika se stane primární uzel a nová sekundární replika je vytvořen a zajistit dostatek uzlů v clusteru. Úloha je automaticky přesměrována na nový primární uzel.

Kromě toho business critical cluster má integrované funkce [škálování pro čtení,](sql-database-read-scale-out.md) která poskytuje zdarma integrovaný integrovaný uzel jen pro čtení, který lze použít ke spuštění dotazů jen pro čtení (například sestavy), které by neměly mít vliv na výkon primární úlohy.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služby?

Úroveň služeb Business Critical je určena pro aplikace, které vyžadují odpovědi s nízkou latencí ze základního úložiště SSD (v průměru 1 až 2 ms), rychlé obnovení v případě selhání základní infrastruktury nebo potřeba vyřadit sestavy, analýzy a jen pro čtení. dotazy na bezplatně čitelnou sekundární repliku primární databáze.

Hlavní důvody, proč byste měli zvolit úroveň služby Business Critical namísto úrovně obecného použití, jsou:
-   Nízké požadavky na latenci vstupně-výstupních řízení – úloha, která vyžaduje rychlou odezvu z vrstvy úložiště (průměrně 1 až 2 milisekundy), by měla používat úroveň Business Critical. 
-   Častá komunikace mezi aplikací a databází. Aplikace, která nemůže využít ukládání do mezipaměti aplikační vrstvy nebo [požadovat dávkování](sql-database-use-batching-to-improve-performance.md) a je třeba odeslat mnoho dotazů SQL, které musí být rychle zpracovány, jsou dobrými kandidáty pro úroveň Business Critical.
-   Velký počet aktualizací – operace vkládání, aktualizace a odstraňování upravují datové stránky v paměti `CHECKPOINT` (nečisté stránky), které je nutné uložit do datových souborů s operací. Potenciální selhání procesu databázového stroje nebo převzetí služeb při selhání databáze s velkým počtem nečistých stránek může zvýšit dobu obnovení ve vrstvě obecného použití. Použijte business critical vrstvu, pokud máte zatížení, které způsobuje mnoho změn v paměti. 
-   Dlouho běžící transakce, které upravují data. Transakce, které jsou otevřeny po delší dobu zabránit zkrácení souboru protokolu, které mohou zvýšit velikost protokolu a počet [souborů protokolu virtuální (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Vysoký počet VLF může zpomalit obnovení databáze po převzetí služeb při selhání.
-   Zatížení s vytvářením sestav a analytickými dotazy, které lze přesměrovat na bezplatnou sekundární repliku jen pro čtení.
- Vyšší odolnost proti chybám a rychlejší zotavení z poruch. V případě selhání systému bude databáze primární instance zakázána a jedna ze sekundárních replik se okamžitě stane novou primární databází pro čtení a zápis, která je připravena ke zpracování dotazů. Databázový stroj nemusí analyzovat a znovu provádět transakce ze souboru protokolu a načíst všechna data do vyrovnávací paměti.
- Pokročilá ochrana proti poškození dat – úroveň Business Critical využívá repliky databází na pozadí pro účely kontinuity podniku, a proto služba také využívá automatickou opravu stránky, což je stejná technologie používaná pro zrcadlení databáze sql serveru [a skupiny dostupnosti](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). V případě, že replika nemůže číst stránku z důvodu problému s integritou dat, bude z jiné repliky načtena nová kopie stránky, která nahradí nečitelnou stránku bez ztráty dat nebo výpadku zákazníka. Tato funkce je použitelná ve vrstvě obecného použití, pokud databáze obsahuje geograficky sekundární repliku.
- Vyšší dostupnost – úroveň Business Critical v konfiguraci Multi-AZ zaručuje 99,995% dostupnost ve srovnání s 99,99 % úrovně pro všeobecné účely.
- Rychlé geografické obnovení – úroveň Business Critical nakonfigurovaná s geografickou replikací má zaručený cíl bodu obnovení (RPO) 5 sekund a cíl doby obnovení (RTO) 30 sekund pro 100 % nasazených hodin.

## <a name="next-steps"></a>Další kroky

- Najděte charakteristiky prostředků (počet jader, vstupně-to, paměť) důležité obchodní vrstvy ve [spravované instanci](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), jedna databáze v [modelu virtuálních jader](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) nebo [model DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)nebo elastický fond v [modelu virtuálních jader](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) a [model DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Přečtěte si o úrovních [Obecné účely](sql-database-service-tier-general-purpose.md) a [Hyperškálování.](sql-database-service-tier-hyperscale.md)
- Další informace o [service fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii naleznete [v tématu Kontinuita provozu](sql-database-business-continuity.md).
