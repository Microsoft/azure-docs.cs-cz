---
title: Migrace z DTU na virtuální jádra
description: Migrujte z modelu DTU do modelu virtuálních jader. Migrace na virtuální jádro je podobná upgradování nebo downgradingu mezi standardní a prémiovou úrovní.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945400"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrace z modelu založeného na DTU do modelu založeného na virtuálních jádrech

## <a name="migrate-a-database"></a>Migrace databáze

Migrace databáze z nákupního modelu založeného na DTU na nákupní model založený na virtuálních jádrech je podobná upgradu nebo downgradu mezi úrovněmi standardních a prémiových služeb v nákupním modelu založeném na DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrace databází, které používají geografickou replikaci

Migrace z modelu založeného na DTU na nákupní model založený na virtuálních jádrech je podobná upgradování nebo downgradu vztahů geografické replikace mezi databázemi ve standardních a prémiových úrovních služeb. Během migrace není nutné zastavit geografickou replikaci, ale musíte dodržovat tato pravidla řazení:

- Při inovaci je nutné nejprve inovovat sekundární databázi a potom inovovat primární databázi.
- Při downgradu pořadí, obrácení pořadí: musíte nejprve downgrade primární databáze a potom downgrade sekundární.

Pokud používáte geografickou replikaci mezi dvěma elastické fondy, doporučujeme určit jeden fond jako primární a druhý jako sekundární. V takovém případě při migraci elastických fondů byste měli použít stejné pokyny pro řazení. Pokud však máte elastické fondy, které obsahují primární i sekundární databáze, zacházejte s fondem s vyšším využitím jako primární a postupujte podle pravidel řazení odpovídajícím způsobem.  

Následující tabulka obsahuje pokyny pro konkrétní scénáře migrace:

|Aktuální úroveň služby|Cílová úroveň služby|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Obecné účely|Boční|Můžete migrovat v libovolném pořadí, ale je třeba zajistit odpovídající velikost icore*|
|Premium|Důležité obchodní|Boční|Můžete migrovat v libovolném pořadí, ale je třeba zajistit odpovídající velikost icore*|
|Standard|Důležité obchodní|Upgrade|Nejprve je nutné migrovat sekundární|
|Důležité obchodní|Standard|Downgrade|Nejprve je nutné migrovat primární|
|Premium|Obecné účely|Downgrade|Nejprve je nutné migrovat primární|
|Obecné účely|Premium|Upgrade|Nejprve je nutné migrovat sekundární|
|Důležité obchodní|Obecné účely|Downgrade|Nejprve je nutné migrovat primární|
|Obecné účely|Důležité obchodní|Upgrade|Nejprve je nutné migrovat sekundární|
||||

\*Jako pravidlo, každých 100 DTU ve standardní vrstvě vyžadují alespoň 1 virtuální jádro a každých 125 DTU v prémiové úrovni vyžadují alespoň 1 virtuální jádro. Další informace naleznete v [nákupním modelu založeném na virtuálních jádrech](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrace skupin převzetí služeb při selhání

Migrace skupin s podporou převzetí služeb při selhání s více databázemi vyžaduje individuální migraci primární a sekundární databáze. Během tohoto procesu platí stejné úvahy a pravidla řazení. Po převodu databází na nákupní model založený na virtuálních jádrech zůstane skupina s podporou převzetí služeb při selhání v platnosti se stejným nastavením zásad.

### <a name="create-a-geo-replication-secondary-database"></a>Vytvoření sekundární databáze geografické replikace

Sekundární databázi geografické replikace (geograficky sekundární) můžete vytvořit pouze pomocí stejné úrovně služby, jakou jste použili pro primární databázi. Pro databáze s vysokou rychlostí generování protokolu doporučujeme vytvořit geograficky sekundární se stejnou velikostí výpočetních prostředků jako primární.

Pokud vytváříte geograficky sekundární v elastickém fondu pro jednu `maxVCore` primární databázi, ujistěte se, že nastavení pro fond odpovídá velikosti výpočetních prostředků primární databáze. Pokud vytváříte geograficky sekundární pro primární v jiném elastickém fondu, doporučujeme, aby fondy mají stejné `maxVCore` nastavení.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Převod databáze založené na DTU na databázi založenou na virtuálních jádrech pomocí kopie databáze

Libovolnou databázi s výpočetní velikostí založenou na DTU můžete zkopírovat do databáze s velikostí výpočetních prostředků založených na virtuálních jádrech bez omezení nebo speciálního řazení, pokud cílová velikost výpočetních prostředků podporuje maximální velikost databáze zdrojové databáze. Kopie databáze vytvoří snímek dat od počátečního času operace kopírování a nesynchronizuje data mezi zdrojem a cílem.

## <a name="next-steps"></a>Další kroky

- Konkrétní velikosti výpočetních prostředků a možnosti velikosti úložiště, které jsou k dispozici pro jednotlivé databáze, naleznete [v tématu limity prostředků založených na virtuálních jádrech SQL Database pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Konkrétní výpočetní velikosti a možnosti velikosti úložiště, které jsou k dispozici pro elastické fondy, najdete v [tématu limity prostředků založených na virtuálních jádrech SQL Database pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
