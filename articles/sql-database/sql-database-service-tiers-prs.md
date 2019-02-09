---
title: Azure SQL Database úrovně Premium RS služby vyřazení z provozu úrovně | Dokumentace Microsoftu
description: Úroveň Premium RS služby se vyřazuje a podpora se ukončuje – viz možností migrace.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: aa9f3b42bbbe8255be328240d26ed4b8740cc5f7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958486"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Úroveň služby Azure SQL Database úrovně Premium RS (preview) se vyřazuje – možnosti pro migraci

V únoru 2018 společnost Microsoft oznámila, že úroveň Premium RS služby ve službě Azure SQL Database by se dočkala všeobecné dostupnosti a bude po 31. ledna 2019 není podporováno. Za tímto účelem podpory termín se prodloužila do 30. června 2019. Tento článek vysvětluje možnosti migrace z úrovně Premium RS na jinou úroveň služby. Po 30. června 2019 bude Microsoft automaticky migrovat vaše databáze úrovně Premium RS na úroveň všeobecně dostupné služby, který nejlépe odpovídá požadavky na výkon vaší databáze úrovně Premium RS.

Následují migrace cíle a cenové možnosti, které může být vhodný pro úroveň Premium RS zákazníky:

- vCore úrovně služeb

  **Obecné** a **pro důležité obchodní informace** v úrovních služeb [model nákupu založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). Tyto dvě úrovně jsou obecně dostupné. Nákupní model založený na virtuálních jádrech také nabízí **Hyperškálovatelného** úroveň služby (ve verzi public preview), která se přizpůsobí na vyžádání podle potřeb vašich úloh s automatickým Škálováním až 100 TB na databázi. Úroveň služby Hyperškálovatelného poskytuje srovnatelné služby na úrovni Premium ve vstupně-výstupním výkonem [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) za cenu blíže na úroveň Premium RS služby.
- Ceny pro vývoj/testování

  [Ceny pro vývoj/testování](https://azure.microsoft.com/pricing/dev-test/) nabízí úspory až 55 % oproti ceně licenci, jsou míry s vaším předplatným sady Visual Studio.
- Zvýhodněné hybridní využití Azure a ceny za vyhrazené kapacity

  [Zvýhodněné hybridní využití Azure a ceny za vyhrazené kapacity](https://azure.microsoft.com/pricing/details/sql-database/) poskytovat úspory až 80 % oproti ceně licenci, jsou sazby. Další informace o těchto možnostech najdete v tématu [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) a [Azure SQL Database rezervované kapacity](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Jednejte hned a migrovat databáze úrovně Premium RS na alternativní úrovně služby SQL Database

Přečtěte si pokyny v tomto článku spolu s naše ceny a dokumentace k určení správné migrace příjemcům pro úroveň Premium RS úlohy.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrujte úlohy náročné na výpočetní a uložit

Pro úlohy náročné na výpočetní úroveň Premium RS doporučujeme migrovat vrstvu naše všeobecně dostupné služby Obecné založený na virtuálních jádrech a ušetříte víc oproti ceně licenci, jsou sazby za použití zvýhodněné hybridní využití Azure pro SQL Server a nabídky záložní kapacitu. Pokud vám zůstane spíš na způsob nákupu založený na DTU, můžete migrovat databáze náročné na výpočetní úroveň Premium RS na úroveň služeb Standard a přesto uložit a úroveň Premium RS ceny obecné dostupnosti (pokud ho bývali do všeobecné dostupnosti).

> [!WARNING]
> Migrace úloh Premium RS na úrovně služeb na základě jednotek DTU úrovně Premium může zvýšit měsíční náklady na srovnání s aktuálními cenami Premium RS. Doporučujeme zvážit úrovně Hyperškálovatelného nebo pro důležité obchodní informace s programem Azure Hybrid Benefit a záložní kapacitu ceny udržovat podobné nebo nižší náklady než pro úroveň Premium RS.

### <a name="premium-rs-databases"></a>Databáze úrovně Premium RS

|**Pokud jsou v tuto chvíli zapnuté...**|**Migrace na srovnatelné založený na virtuálních jádrech...**|**Migrace na srovnatelné založený na DTU...**|
|---|---|---|
|Premium RS 1|Obecné účely 1 vCore (Gen4)|Standard 3|
|Premium RS 2|Obecné účely, 2 virtuální jádra (Gen4)|Standard 4|
|Premium RS 4|Virtuální jádra pro obecné účely 4 (Gen4)|Standard 6|
|Premium RS 6|Virtuální jádra pro obecné účely 6 (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Fondy úrovně Premium RS

|**Pokud jsou v tuto chvíli zapnuté...**|**Migrace na srovnatelné založený na virtuálních jádrech...**|**Migrace na srovnatelné založený na DTU...**|
|---|---|---|
|Premium RS pool 125 DTU|Obecné účely 1 vCore (Gen4)|Fond Standard 100 Edtu|
|Premium RS pool 250 DTU|Obecné účely, 2 virtuální jádra (Gen4)|Jednotky Edtu fondu úrovně Standard 250|
|Premium RS pool 500 DTU|Virtuální jádra pro obecné účely 4 (Gen4)|Standardní fond 500 Edtu|
|Premium RS pool 1000 DTU|Virtuální jádra pro obecné účely 8 (Gen4)|Fond Standard 1000 Edtu|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimalizace úspory a výkonu pro úlohy náročné na vstupně-výstupních operací

Doporučujeme migrovat vaše izolované databáze náročné na vstupně-výstupních operací na naše založený na virtuálních jádrech Hyperškálovatelného úroveň, aktuálně ve verzi preview a fondech databáze náročné na vstupně-výstupních operací na naše obecně k dispozici pro důležité obchodní informace úroveň pro ideální kombinaci výkonu a nákladů.  Založený na virtuálních jádrech následující možnosti budou udržovat nebo zvýšit výkon vašich aktuální a mohou ušetřit peníze při kombinaci s programem Azure Hybrid Benefit a vyhrazené kapacity ceny.

|**Pokud jsou v tuto chvíli zapnuté...**|**Migrace na srovnatelné založený na virtuálních jádrech...**|**Migrace na srovnatelné založený na DTU...**|
|---|---|---|
|Premium RS 1|(Preview) VCore Hyperškálovatelného 1 (Gen4) nebo – 1 kritické obchodní vCore (Gen4)|Premium 1|
|Premium RS 2|(Preview) Velkokapacitní 2 virtuální jádra (Gen4) nebo kritické obchodní 2 virtuální jádra (Gen4|Premium 2|
|Premium RS 4|(Preview) Velkokapacitní 4 virtuální jádra (Gen4) nebo kritické obchodní 4 virtuální jádra (Gen4)|Premium 4
|Premium RS 6|(Preview) Velkokapacitní 6 virtuální jádra (Gen4) nebo virtuální jádra pro důležité obchodní 6 (Gen4)|Premium 6|

|**Pokud jsou v tuto chvíli zapnuté...**|**Migrace na srovnatelné založený na virtuálních jádrech...**|**Migrace na srovnatelné založený na DTU...**|
|---|---|---|
|Premium RS pool 125 DTU|Obchodní důležité 2 virtuální jádra (Gen4)|Jednotky Edtu fondu 125 Premium|
|Premium RS pool 250 DTU|Obchodní důležité 2 virtuální jádra (Gen4)|Jednotky Edtu fondu 250 Premium|
|Premium RS pool 500 DTU|Obchodní kritické 4 virtuální jádra (Gen4)|Fond 500 Edtu úrovně Premium|
|Premium RS pool 1000 DTU|Obchodní kritické 8 virtuálních jader (Gen4)|Jednotky Edtu fondu 1000 Premium|

## <a name="take-advantage-of-our-new-offers"></a>Využijte naše nové nabídky

Speciální nabídky, které můžete ušetřit až 80 % oproti ceně licenci, jsou ceny nárok naší úrovně služeb v nákupní model založený na virtuálních jádrech. Pomocí své licence SQL serveru Standard nebo Enterprise edition s aktivním programem Software Assurance ušetřit až 55 % oproti ceně licenci, jsou ceny s [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Můžete zkombinovat zvýhodněné hybridní využití s [Azure SQL Database rezervované kapacity](sql-database-reserved-capacity.md) ceny a ušetřit až 80 % při potvrzení předem na jeden nebo tři roky termín.  Aktivujte výhody pro oba z webu Azure portal ještě dnes.

Pokud máte nějaké dotazy nebo připomínky týkající se to změnit nebo pokud potřebujete pomoc s migrací, obraťte se na [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrace z úrovně služeb Premium RS na úroveň služby v jednotek DTU nebo modelu virt. jader

### <a name="migration-of-a-database"></a>Migrace databáze

Migrace databáze z úrovně Premium RS služby vrstvy pro vrstvu služby buď jednotek DTU nebo modelu virt. jader je podobný upgrade a Downgrade mezi úrovní služeb v rámci úrovně služeb Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Pomocí kopie databáze úrovně Premium RS databázi převést do databáze na základě jednotek DTU nebo založený na virtuálních jádrech

Můžete zkopírovat libovolnou databázi s velikostí výpočetní úroveň Premium RS na databázi s velikostí založený na DTU nebo založený na virtuálních jádrech výpočetní bez omezení nebo speciální pořadí úloh, tak dlouho, dokud výpočty velikost cíle podporuje maximální velikost zdrojové databáze. Kopírování databáze vytvoří snímek dat od počáteční čas operace kopírování a neprovede synchronizace dat mezi zdrojem a cílem.

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro izolované databáze, najdete v části [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro elastické fondy, najdete v části [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
