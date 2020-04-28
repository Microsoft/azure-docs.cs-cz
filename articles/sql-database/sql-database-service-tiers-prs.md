---
title: Premium RS vyřazení úrovně služby
description: Úroveň služby Premium RS se vyřadí a podpora se ukončí – viz možnosti migrace.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821047"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Služba Azure SQL Database Premium RS úrovně služeb (Preview) je vyřazená – možnosti migrace

V únoru 2018 společnost Microsoft oznámila, že úroveň služby Premium RS v Azure SQL Database nebude uvolněna pro obecnou dostupnost a již nebude podporována po 31. ledna 2019. Tento konečný termín podpory byl prodloužen na 30. června 2019. Tento článek vysvětluje možnosti migrace z Premium RS úrovně služby na jinou úroveň služby. Od 30. června 2019 Microsoft automaticky migruje vaše databáze Premium RS do všeobecně dostupné úrovně služeb, která nejlépe odpovídá požadavkům na výkon vaší databáze Premium RS.

Níže jsou uvedené cíle migrace a cenové možnosti, které mohou být vhodné pro Premium RS zákazníky:

- vCore úrovně služeb

  Úrovně služeb **pro obecné účely** a **pro důležité obchodní informace** v [modelu nákupu založeném na Vcore](sql-database-service-tiers-vcore.md). Tyto dvě úrovně služeb jsou všeobecně dostupné. Nákupní model založený na vCoreech taky nabízí úroveň služby s technologií **škálování** , která se přizpůsobí požadavkům vašich úloh díky automatickému škálování až na 100 TB na databázi. Úroveň služby pro škálování na úrovni služeb poskytuje vstupně-výstupní výkon srovnatelný s úrovní služeb Premium v [modelu nákupu na základě DTU](sql-database-service-tiers-dtu.md) za cenu, která se blíží úrovni služby Premium RS.
- Ceny pro vývoj a testování

  [Ceny pro vývoj a testování](https://azure.microsoft.com/pricing/dev-test/) poskytují úsporu až 55% oproti sazbám zahrnutým do předplatného sady Visual Studio.
- Ceny Zvýhodněné hybridní využití Azure a rezervované kapacity

  [Ceny zvýhodněné hybridní využití Azure a rezervované kapacity](https://azure.microsoft.com/pricing/details/sql-database/) poskytují úsporu až 80% oproti sazbám zahrnutým v licencích. Další informace o těchto možnostech najdete v tématu [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) a [Azure SQL Database rezervovanou kapacitu](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Využijte možnost pracovat k migraci Premium RSch databází na alternativní SQL Database úrovně služeb.

Přečtěte si pokyny v tomto článku spolu s cenami a dokumentaci, abyste zjistili správné cíle migrace pro vaše Premium RS úlohy.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrace úloh náročných na výpočetní výkon a jejich ukládání

Pro úlohy Premium RS náročné na výpočetní výkon doporučujeme migrovat na naši všeobecně dostupnou úroveň služby Pro obecné účely založenou na vCore a ušetřit více oproti licenčním sazbám, a to pomocí Zvýhodněné hybridní využití Azure pro nabídky SQL Server a vyhrazené kapacity. Pokud byste chtěli zůstat na možnosti nákupu na základě DTU, můžete migrovat databáze Premium RS náročné na výpočetní výkon na úroveň Standard a stále ukládat v Premium RS ceny týkající se obecné dostupnosti (Pokud se nedostaly do všeobecné dostupnosti).

> [!WARNING]
> Migrace vašich Premium RSch úloh do úrovní služeb Premium na bázi DTU může zvýšit měsíční náklady oproti aktuálnímu Premium RS ceny. Doporučujeme, abyste se domnívají, jak vrstvy na úrovni škálování nebo Pro důležité obchodní informace s využitím Zvýhodněné hybridní využití Azure a cen rezervovaných kapacit, aby se zachovaly podobné nebo nižší náklady než Premium RS.

### <a name="premium-rs-databases"></a>Premium RS databáze

|**Pokud aktuálně jste zapnuti...**|**Migrace na srovnatelný vCore...**|**Migrace na srovnatelnou bázi DTU...**|
|---|---|---|
|Premium RS 1|Pro obecné účely 1 vCore (COMPUTE GEN4 –)|Standard 3|
|Premium RS 2|Pro obecné účely 2 virtuální jádra (COMPUTE GEN4 –)|4. úrovně Standard|
|Premium RS 4|Pro obecné účely 4 virtuální jádra (COMPUTE GEN4 –)|Standard 6|
|Premium RS 6|Pro obecné účely 6 virtuální jádra (COMPUTE GEN4 –)|Standard 7|

### <a name="premium-rs-pools"></a>Fondy Premium RS

|**Pokud aktuálně jste zapnuti...**|**Migrace na srovnatelný vCore...**|**Migrace na srovnatelnou bázi DTU...**|
|---|---|---|
|Premium RS ve fondu 125 DTU|Pro obecné účely 1 vCore (COMPUTE GEN4 –)|Standardní fond 100 eDTU|
|Premium RS ve fondu 250 DTU|Pro obecné účely 2 virtuální jádra (COMPUTE GEN4 –)|Standardní fond 250 eDTU|
|Premium RS ve fondu 500 DTU|Pro obecné účely 4 virtuální jádra (COMPUTE GEN4 –)|Standardní fond 500 eDTU|
|Premium RS ve fondu 1000 DTU|Pro obecné účely 8 virtuální jádra (COMPUTE GEN4 –)|Standardní fond 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimalizujte úspory a výkon pro úlohy náročné na v/v.

Pro optimální kombinaci výkonu a nákladů doporučujeme migrovat jednotlivé databáze náročné na v/v na naši škálovatelnou úroveň vCore, která je aktuálně ve verzi Preview, a vaše fondy databází s vysokým počtem v/v na naši všeobecně dostupnou Pro důležité obchodní informace úroveň  Následující možnosti založené na vCore budou udržovat nebo zdokonalovat váš aktuální výkon a při kombinaci s Zvýhodněné hybridní využití Azure a cenami za rezervované kapacity se vám můžou ušetřit peníze.

|**Pokud aktuálně jste zapnuti...**|**Migrace na srovnatelný vCore...**|**Migrace na srovnatelnou bázi DTU...**|
|---|---|---|
|Premium RS 1| VCore 1 (COMPUTE GEN4 –) nebo Pro důležité obchodní informace 1 vCore (COMPUTE GEN4 –)|Premium 1|
|Premium RS 2| Virtuální jádra (COMPUTE GEN4 –) a Pro důležité obchodní informace 2 virtuální jádra (COMPUTE GEN4 –|Premium 2|
|Premium RS 4| Virtuální jádra (COMPUTE GEN4 –) a Pro důležité obchodní informace 4 virtuální jádra (COMPUTE GEN4 –)|Premium 4
|Premium RS 6| Virtuální jádra (COMPUTE GEN4 –) pro škálování na úrovni 6 nebo Pro důležité obchodní informace 6 virtuální jádra (COMPUTE GEN4 –)|Premium 6|

|**Pokud aktuálně jste zapnuti...**|**Migrace na srovnatelný vCore...**|**Migrace na srovnatelnou bázi DTU...**|
|---|---|---|
|Premium RS ve fondu 125 DTU|Pro důležité obchodní informace 2 virtuální jádra (COMPUTE GEN4 –)|Fond Premium 125 eDTU|
|Premium RS ve fondu 250 DTU|Pro důležité obchodní informace 2 virtuální jádra (COMPUTE GEN4 –)|Fond Premium 250 eDTU|
|Premium RS ve fondu 500 DTU|Pro důležité obchodní informace 4 virtuální jádra (COMPUTE GEN4 –)|Fond Premium 500 eDTU|
|Premium RS ve fondu 1000 DTU|Pro důležité obchodní informace 8 virtuální jádra (COMPUTE GEN4 –)|Fond Premium 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Využijte naše nové nabídky

Naše úrovně služeb v rámci nákupního modelu založeného na vCore mají nárok na speciální nabídky, které vám můžou ušetřit až 80% oproti licenční ceně. Využijte licence SQL Server Standard nebo Enterprise Edition s aktivním Software Assurance a Ušetřete až 55% oproti licencím, které jsou v [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/)k dispozici. Zvýhodněné hybridní využití můžete kombinovat s [Azure SQL Databasemi cenami za rezervované kapacity](sql-database-reserved-capacity.md) a ušetřit až 80%, když se potvrdíte dopředu na jeden nebo tři roky.  Aktivujte si obě výhody ještě dnes z Azure Portal.

Pokud máte nějaké dotazy nebo obavy týkající se této změny nebo potřebujete pomoc s migrací, obraťte se na [Společnost Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrace z Premium RS úrovně služeb do úrovně služeb v modelu DTU nebo v modelu vCore

### <a name="migration-of-a-database"></a>Migrace databáze

Migrace databáze z Premium RS úrovně služby na úroveň služby ve službě DTU nebo modelu vCore se podobá upgradu nebo downgrade mezi úrovněmi služeb v Premium RS úrovni služby.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Použití kopírování databáze k převedení databáze Premium RS na databázi založenou na DTU nebo na bázi vCore

Do databáze s výpočetní velikostí založenou na DTU nebo vCore se dá zkopírovat jakákoli Premium RS databáze s výpočetní velikostí založenou na DTU nebo bez omezení nebo speciálního pořadí, pokud cílová výpočetní velikost podporuje maximální velikost databáze zdrojové databáze. Kopie databáze vytvoří snímek dat pro počáteční čas operace kopírování a neprovádí synchronizaci dat mezi zdrojem a cílem.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o specifických velikostech výpočtů a možnostech velikosti úložiště, které jsou dostupné pro izolovanou databázi, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé](sql-database-vcore-resource-limits-single-databases.md) databáze.
- Podrobnosti o specifických velikostech výpočtů a možnostech velikosti úložiště dostupných pro elastické fondy najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
