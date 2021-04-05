---
title: Úroveň služby Pro důležité obchodní informace
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Přečtěte si o důležitých podnikových úrovních služeb pro Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780219"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Pro důležité obchodní informace úroveň – Azure SQL Database a spravovaná instance Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Úroveň Pro důležité obchodní informace se v modelu nákupu DTU označuje jako Premium. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](purchasing-models.md).

Azure SQL Database a Azure SQL Managed instance jsou založené na architektuře SQL Server databázového stroje, která je upravena pro cloudové prostředí, aby se zajistila 99,99% dostupnost, a to i v případě selhání infrastruktury. Používají se tři modely architektury:
- Pro obecné účely/Standard 
- Pro důležité obchodní informace/Premium
- Hyperškálování

Model úrovně služeb Premium/Pro důležité obchodní informace je založený na clusteru procesů databázového stroje. Tento model architektury spoléhá na skutečnost, že je vždy kvorum dostupných uzlů databázového stroje a má minimální dopad na výkon na vaše úlohy, a to i během aktivit údržby. Úroveň služby technologie webscale je aktuálně dostupná jenom pro Azure SQL Database (ne pro spravovanou instanci SQL) a je vysoce škálovatelná úroveň výkonu úložiště a výpočetní technologie, která využívá architekturu Azure k horizontálnímu navýšení kapacity úložiště a výpočetních prostředků pro databázi v Azure SQL Database, a to nad rámec limitů, které jsou dostupné pro Pro obecné účely a Pro důležité obchodní informace úrovně služeb.

Upgrady a opravy Azure odchází z operačního systému, ovladačů a SQL Server databázového stroje s minimálním časovým intervalem pro koncové uživatele. 

Dostupnost Premium je povolená v úrovních služby Premium a Pro důležité obchodní informace a je navržená pro náročné úlohy, které nemůžou tolerovat dopad na výkon kvůli probíhajícím operacím údržby.

Výpočetní prostředky a úložiště jsou integrované na jednom uzlu v modelu Premium. Vysokou dostupnost v tomto modelu architektury se dosahuje replikací výpočetních prostředků (SQL Server procesu databázového stroje) a úložiště (místně připojená jednotka SSD) nasazená do clusteru se čtyřmi uzly pomocí technologie podobně jako SQL Server [skupiny dostupnosti Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster uzlů databázového stroje](./media/service-tier-business-critical/business-critical-service-tier.png)

Proces databázového stroje SQL Server a zdrojové soubory. mdf/. ldf jsou umístěné na stejném uzlu s místně připojeným úložištěm SSD, které poskytuje nízkou latenci pro vaše zatížení. Vysoká dostupnost se implementuje pomocí technologie podobné SQL Server [skupinám dostupnosti Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Každá databáze je cluster databázových uzlů s jednou primární databází, která je přístupná pro úlohy zákazníka, a třemi sekundárními procesy, které obsahují kopie dat. Primární uzel průběžně přenáší změny do sekundárních uzlů, aby bylo zajištěno, že data jsou k dispozici na sekundárních replikách, pokud z jakéhokoli důvodu dojde k chybě primárního uzlu. Převzetí služeb při selhání je zpracováváno modulem SQL Server Database Engine – jedna sekundární replika se stal primárním uzlem a vytvoří se nová sekundární replika, která zajistí, že cluster obsahuje dostatek uzlů. Zatížení se automaticky přesměruje na nový primární uzel.

Kromě toho Pro důležité obchodní informace cluster obsahuje integrovanou možnost [škálování pro čtení](read-scale-out.md) , která poskytuje bezplatný integrovaný uzel jen pro čtení, který se dá použít ke spouštění dotazů jen pro čtení (například sestav), které by neměly mít vliv na výkon vašeho primárního zatížení.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služby

Úroveň služby Pro důležité obchodní informace je navržená pro aplikace, které vyžadují odezvy s nízkou latencí z podkladového úložiště SSD (průměrně 1-2 MS), rychlé obnovení v případě, že se podkladová infrastruktura nezdařila nebo pokud je potřeba mimo jiné načíst sestavy, analýzy a dotazy jen pro čtení, a to zdarma pro čitelné sekundární repliku primární databáze.

Hlavní důvody, proč byste měli zvolit Pro důležité obchodní informace úroveň služby místo Pro obecné účely úrovně:
-   **Nízké požadavky na latenci v/v** – úlohy, které potřebují rychlou odezvu z vrstvy úložiště (1-2 milisekund v průměru), by měly používat pro důležité obchodní informace úrovně. 
-   **Častá komunikace mezi aplikací a databází** Aplikace, které nemůžou využívat ukládání do mezipaměti aplikační vrstvy nebo vyžadují [dávkování](../performance-improve-use-batching.md) a potřebují poslat mnoho dotazů SQL, které se musí rychle zpracovat, jsou dobrými kandidáty na pro důležité obchodní informaceou úroveň.
-   **Velký počet aktualizací** – operace vložení, aktualizace a odstranění upraví datové stránky v paměti (nezměněná stránka), které je třeba uložit do datových souborů s `CHECKPOINT` operací. Možná dojde k selhání procesu databázového stroje nebo převzetí služeb při selhání databáze s velkým počtem nezměněných stránek, což může prodloužit dobu obnovení Pro obecné účely úrovně. Pro důležité obchodní informace úroveň použijte v případě, že máte zatížení, které způsobuje mnoho změn v paměti. 
-   **Dlouho běžící transakce, které upravují data**. Transakce, které jsou otevřeny po delší dobu, brání v zkracování souboru protokolu, což může zvýšit velikost protokolu a počet [virtuálních souborů protokolu (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Vysoký počet VLFs může zpomalit obnovení databáze po převzetí služeb při selhání.
-   **Úlohy s vytvářením sestav a analytickými dotazy** , které se dají přesměrovat na bezplatnou sekundární repliku jen pro čtení.
- **Vyšší odolnost a rychlejší obnovení při selhání**. V případě selhání systému bude databáze na primární instanci zakázaná a jedna ze sekundárních replik se hned stala novou primární databází pro čtení a zápis, která je připravená na zpracování dotazů. Databázový stroj nemusí analyzovat a opakovat transakce ze souboru protokolu a načíst všechna data do vyrovnávací paměti.
- **Rozšířená ochrana před poškozením dat**. Pro důležité obchodní informace vrstva využívá repliky databáze za účelem zajištění kontinuity podnikových aplikací, a proto služba také využívá automatickou opravu stránky, což je stejná technologie, která se používá pro SQL Server [zrcadlení databáze a skupiny dostupnosti](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). V případě, že replika nemůže přečíst stránku z důvodu problému s integritou dat, bude nová kopie stránky načtena z jiné repliky, která nahrazuje nečitelný stránku, aniž by došlo ke ztrátě dat nebo výpadkům zákazníků. Tato funkce se vztahuje na vrstvu Pro obecné účely, pokud má databáze geograficky sekundární repliku.
- **Vyšší dostupnost** – pro důležité obchodní informace úroveň v nástroji multi-AZ configuration garantuje 99,995% dostupnost, ve srovnání s 99,99% pro obecné účely úrovně.
- **Rychlá úroveň geografického obnovení** – pro důležité obchodní informace nakonfigurovaná s geografickou replikací, má zaručený cíl bodu obnovení (RPO) 5 sec a doba obnovení (RTO) 30 sekund po dobu 100% nasazených hodin.

## <a name="next-steps"></a>Další kroky

- Vyhledá charakteristiky prostředků (počet jader, vstupně-výstupní operace, paměti) Pro důležité obchodní informace úrovně v [SQL Managed instance](../managed-instance/resource-limits.md#service-tier-characteristics), izolovaná databáze v [modelu Vcore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) nebo [modelu DTU](resource-limits-dtu-single-databases.md#premium-service-tier)nebo elastický fond v modelu [Vcore](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) a modelu [DTU](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Přečtěte si o [pro obecné účely](service-tier-general-purpose.md) a úrovních [škálování](service-tier-hyperscale.md) .
- Přečtěte si o [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [Kontinuita podnikových aplikací](business-continuity-high-availability-disaster-recover-hadr-overview.md).