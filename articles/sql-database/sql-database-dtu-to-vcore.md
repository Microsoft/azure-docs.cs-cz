---
title: Migrace z DTU na virtuální jádra
description: Migrujte z modelu DTU do modelu vCore. Migrace na vCore je podobná upgradu nebo downgrade mezi úrovní Standard a Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78945400"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrace z modelu založeného na DTU na model založený na vCore

## <a name="migrate-a-database"></a>Migrace databáze

Migrace databáze z nákupního modelu založeného na DTU do nákupního modelu založeného na vCore se podobá upgradu nebo downgrade mezi úrovněmi služeb Standard a Premium v nákupním modelu založeném na DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrace databází, které používají geografickou replikaci

Migrace z modelu založeného na DTU do nákupního modelu založeného na vCore se podobá upgradu nebo downgrade vztahů geografické replikace mezi databázemi v úrovních služeb Standard a Premium. Během migrace nemusíte zastavit geografickou replikaci, ale musíte dodržovat tato pravidla sekvencování:

- Při upgradu musíte nejdřív upgradovat sekundární databázi a potom upgradovat primární.
- Když se downgrade, obrátí se pořadí: Nejdřív musíte nejprve downgradovat primární databázi a pak snížit její sekundární verzi.

Pokud používáte geografickou replikaci mezi dvěma elastickými fondy, doporučujeme, abyste jeden fond určili jako primární a druhý jako sekundární. V takovém případě byste měli při migraci elastických fondů použít stejné pokyny k sekvencování. Pokud ale máte elastické fondy, které obsahují jak primární, tak sekundární databáze, považovat fond s vyšším využitím jako primární a podle toho pravidla sekvencování řídí.  

Následující tabulka poskytuje pokyny pro konkrétní scénáře migrace:

|Aktuální úroveň služby|Cílová úroveň služby|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Obecné účely|Boku|Může migrovat v libovolném pořadí, ale musí zajistit odpovídající velikost vCore *|
|Premium|Důležité pro podnikání|Boku|Může migrovat v libovolném pořadí, ale musí zajistit odpovídající velikost vCore *|
|Standard|Důležité pro podnikání|Upgrade|Nejprve je třeba migrovat sekundární|
|Důležité pro podnikání|Standard|Downgrade|Nejprve je třeba migrovat primární|
|Premium|Obecné účely|Downgrade|Nejprve je třeba migrovat primární|
|Obecné účely|Premium|Upgrade|Nejprve je třeba migrovat sekundární|
|Důležité pro podnikání|Obecné účely|Downgrade|Nejprve je třeba migrovat primární|
|Obecné účely|Důležité pro podnikání|Upgrade|Nejprve je třeba migrovat sekundární|
||||

\*V rámci pravidla palce každý 100 DTU na úrovni Standard vyžaduje aspoň 1 vCore a každé 125 DTU úrovně Premium vyžaduje aspoň 1 vCore. Další informace najdete v tématu [nákupní model založený na Vcore](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrace skupin převzetí služeb při selhání

Migrace skupin převzetí služeb při selhání s více databázemi vyžaduje samostatnou migraci primárních a sekundárních databází. Během tohoto procesu platí stejné podmínky a pravidla sekvence. Po převedení databází na nákupní model založený na vCore zůstane skupina převzetí služeb při selhání platná se stejnými nastaveními zásad.

### <a name="create-a-geo-replication-secondary-database"></a>Vytvoření sekundární databáze geografické replikace

Sekundární databázi geografické replikace můžete vytvořit pouze pomocí stejné úrovně služby, jakou jste použili pro primární databázi. Pro databáze s vysokou rychlostí generování protokolů doporučujeme vytvořit geografickou sekundární stejnou velikost služby COMPUTE jako primární.

Pokud vytváříte geograficky sekundární v elastickém fondu pro jednu primární databázi, ujistěte se `maxVCore` , že nastavení fondu odpovídá výpočetní velikosti primární databáze. Pokud vytváříte geografickou sekundární hodnotu pro primární objekt v jiném elastickém fondu, doporučujeme, aby fondy měly stejné `maxVCore` nastavení.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Použití kopie databáze k převedení databáze založené na DTU na databázi založenou na vCore

Můžete zkopírovat jakoukoli databázi s výpočetní velikostí založenou na DTU do databáze s výpočetní velikostí založenou na vCore bez omezení nebo speciální sekvencování, pokud cílová výpočetní velikost podporuje maximální velikost databáze zdrojové databáze. Kopie databáze vytvoří snímek dat pro počáteční čas operace kopírování a nesynchronizuje data mezi zdrojem a cílem.

## <a name="next-steps"></a>Další kroky

- Konkrétní velikosti a možnosti velikosti úložiště, které jsou dostupné pro jednotlivé databáze, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Konkrétní velikosti a možnosti velikosti úložiště dostupné pro elastické fondy najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
