---
title: Vyřazení úrovně služby Premium RS
description: Úroveň služby Premium RS je vyřazena a podpora pro ni končí – viz možnosti migrace.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821047"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Úroveň služby Azure SQL Database Premium RS (preview) se vyřazuje – možnosti migrace

V únoru 2018 Společnost Microsoft oznámila, že úroveň služeb Premium RS v Azure SQL Database nebude uvolněna pro obecnou dostupnost a nebude podporována po 31. Tato lhůta pro ukončení podpory byla prodloužena do 30. Tento článek vysvětluje možnosti migrace z úrovně služby Premium RS na jinou úroveň služeb. června 2019 microsoft automaticky migruje vaše databáze Premium RS na obecně dostupnou úroveň služeb, která nejvíce odpovídá požadavkům na výkon vaší databáze Premium RS.

Níže jsou uvedeny cíle migrace a cenové možnosti, které mohou být vhodné pro zákazníky Premium RS:

- úrovně služeb virtuálních jader

  Úrovně služeb **obecnéúčely** a **důležité pro podnikání** v modelu [nákupu založeném na virtuálních jádrech](sql-database-service-tiers-vcore.md). Tyto dvě úrovně služeb jsou obecně k dispozici. Nákupní model založený na virtuálních jádrech také nabízí úroveň služeb **Hyperscale,** která se na vyžádání přizpůsobuje potřebám vaší úlohy s automatickým škálováním až na 100 TB na databázi. Úroveň služeb Hyperscale poskytuje výkon vstupně-up srovnatelný s úrovní služeb Premium v [nákupním modelu založeném na DTU](sql-database-service-tiers-dtu.md) za cenu blíže k úrovni služeb Premium RS.
- Ceny pro vývoj a testování

  [Ceny za vývoj a testování](https://azure.microsoft.com/pricing/dev-test/) poskytují úspory až o 55 % oproti sazbám zahrnutým v licenci s vaším předplatným sady Visual Studio.
- Hybridní výhody Azure a ceny za rezervovanou kapacitu

  [Azure Hybrid Benefit a ceny za rezervovanou kapacitu](https://azure.microsoft.com/pricing/details/sql-database/) poskytují úspory až 80 % oproti sazbám zahrnutým z licence. Další informace o těchto možnostech najdete v [tématu Azure Hybrid Benefit for SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) a Azure SQL Database [rezervované kapacity](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Jednejte nyní a migrujte své databáze Premium RS na alternativní úrovně služeb SQL Database

Přečtěte si pokyny v tomto článku spolu s našimi cenami a dokumentací a zjistěte, kde se správná cílová místa migrace pro vaše úlohy Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrace úloh náročných na výpočetní výkon a uložení

Pro vaše výpočetní náročné úlohy Premium RS doporučujeme migrovat na naši obecně dostupnou úroveň služeb pro obecné účely založené na virtuálních jádrech a ušetřit více oproti sazbám zahrnutým z licence pomocí hybridní výhody Azure pro SQL Server a nabídky rezervované kapacity. Pokud byste raději zůstali na možnosti nákupu na základě DTU, můžete migrovat databáze Premium RS náročné na výpočetní výkon na úroveň služby Standard a stále ušetřit oproti cenám obecné dostupnosti Premium RS (pokud přešly do obecné dostupnosti).

> [!WARNING]
> Migrace úloh Premium RS na úrovně služeb Premium založené na DTU může zvýšit měsíční náklady oproti aktuálním cenám Premium RS. Doporučujeme zvážit úrovně Hyperscale nebo Business Critical s hybridními výhodami Azure a cenami za rezervovanou kapacitu, aby chomáč podobných nebo nižších nákladů než Premium RS.

### <a name="premium-rs-databases"></a>Databáze Premium RS

|**Pokud jste v současné době na...**|**Migrovat na srovnatelné virtuální jádro...**|**Migrovat na srovnatelné DTU-založené ...**|
|---|---|---|
|Premium RS 1|Virtuální jádro pro všeobecné použití 1 (Gen4)|Standardní 3|
|Prémiové RS 2|Virtuální jádra pro všeobecné použití 2 (Gen4)|Standardní 4|
|Prémiové RS 4|Virtuální jádra pro všeobecné použití 4 (Gen4)|Norma 6|
|Prémiové RS 6|Virtuální jádra pro všeobecné použití 6 (Gen4)|Norma 7|

### <a name="premium-rs-pools"></a>Fondy Premium RS

|**Pokud jste v současné době na...**|**Migrovat na srovnatelné virtuální jádro...**|**Migrovat na srovnatelné DTU-založené ...**|
|---|---|---|
|Prémiový RS bazén 125 DTU|Virtuální jádro pro všeobecné použití 1 (Gen4)|Standardní fond 100 eDTU|
|Prémiový RS bazén 250 DTU|Virtuální jádra pro všeobecné použití 2 (Gen4)|Standardní fond 250 eDTU|
|Prémiový RS bazén 500 DTU|Virtuální jádra pro všeobecné použití 4 (Gen4)|Standardní bazén 500 eDTU|
|Prémiový RS bazén 1000 DTU|Virtuální jádra pro všeobecné použití 8 (Gen4)|Standardní bazén 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimalizujte úspory a výkon pro vaše úlohy náročné na IO

Doporučujeme migrovat jednotlivé databáze náročné na IO na naši úroveň Hyperscale založenou na virtuálních jádrech, která je momentálně ve verzi Preview, a do našich obecně dostupných úrovní Business Critical, abyste měli optimální kombinaci výkonu a nákladů.  Následující možnosti založené na virtuálních jádrech budou udržovat nebo zlepšovat váš aktuální výkon a mohou vám ušetřit peníze v kombinaci s hybridní výhodou Azure a cenami za rezervovanou kapacitu.

|**Pokud jste v současné době na...**|**Migrovat na srovnatelné virtuální jádro...**|**Migrovat na srovnatelné DTU-založené ...**|
|---|---|---|
|Premium RS 1| Virtuální jádro Hyperscale 1 (Gen4) nebo Business Critical 1 virtuální jádro (Gen4)|Prémiový 1|
|Prémiové RS 2| Virtuální jádra Hyperscale 2 (Gen4) nebo Business Critical 2 virtuální jádra (Gen4|Prémiový 2|
|Prémiové RS 4| Virtuální jádra Hyperscale 4 (Gen4) nebo business critical 4 virtuální jádra (Gen4)|Prémiový 4
|Prémiové RS 6| Virtuální jádra Hyperscale 6 (Gen4) nebo Business Critical 6 virtuálních jader (Gen4)|Prémiový 6|

|**Pokud jste v současné době na...**|**Migrovat na srovnatelné virtuální jádro...**|**Migrovat na srovnatelné DTU-založené ...**|
|---|---|---|
|Prémiový RS bazén 125 DTU|Kritická 2 virtuální jádra pro podnikání (Gen4)|Prémiový fond 125 eDTU|
|Prémiový RS bazén 250 DTU|Kritická 2 virtuální jádra pro podnikání (Gen4)|Prémiový fond 250 eDTU|
|Prémiový RS bazén 500 DTU|Kritická virtuální jádra 4 pro firmy (Gen4)|Prémiový bazén 500 eDTU|
|Prémiový RS bazén 1000 DTU|Kritická virtuální jádra pro kritické pro podnikání (Gen4)|Prémiový bazén 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Využijte našich nových nabídek

Naše úrovně služeb v nákupním modelu založeném na virtuálních jádrech jsou způsobilé pro speciální nabídky, které vám mohou ušetřit až 80 % oproti cenám zahrnutým v licenci. Pomocí licencí SQL Server Standard nebo Enterprise edition s aktivním programem Software Assurance ušetříte až 55 % oproti cenám zahrnutým z licencí pomocí [hybridnívýhody Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Hybridní výhodu můžete kombinovat s cenami [rezervované kapacity Azure SQL Database](sql-database-reserved-capacity.md) a ušetřit až 80 % při potvrzení předem na dobu jednoho nebo tří let.  Aktivujte obě výhody ještě dnes z portálu Azure.

Máte-li jakékoli dotazy nebo obavy týkající se této změny nebo potřebujete-li pomoc s migrací, obraťte se na společnost [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrace z úrovně služby Premium RS na úroveň služby v modelu DTU nebo v modelu virtuálních jader

### <a name="migration-of-a-database"></a>Migrace databáze

Migrace databáze z úrovně služby Premium RS na úroveň služeb v modelu DTU nebo v modelu virtuálního jádra je podobná upgradu nebo downgradu mezi úrovněmi služeb na úrovni služby Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Použití kopie databáze k převodu databáze Premium RS do databáze založené na DTU nebo na virtuálních jádrech

Můžete zkopírovat libovolnou databázi s velikostí výpočetního výkonu Premium RS do databáze s velikostí výpočetních prostředků založených na DTU nebo na virtuálních jádrech bez omezení nebo speciálního řazení, pokud cílová výpočetní velikost podporuje maximální velikost databáze zdrojové databáze. Kopie databáze vytvoří snímek dat od počátečního času operace kopírování a neprovádí synchronizaci dat mezi zdrojem a cílem.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o konkrétních velikostech výpočetních prostředků a možnostech velikosti úložiště, které jsou k dispozici pro jednu databázi, naleznete [v tématu limity prostředků založených na virtuálních jádrech SQL Database pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)
- Podrobnosti o konkrétních výpočetních velikostech a možnostech velikosti úložiště, které jsou k dispozici pro elastické fondy, najdete v [tématu limity prostředků založených na virtuálních jádrech SQL Database pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
