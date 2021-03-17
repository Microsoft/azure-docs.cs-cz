---
title: Princip slev za rezervaci – Azure Database for MySQL
description: Projděte si způsob uplatňování slev za rezervaci na servery Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 85a8c59cce74787a7b1723850915ec133b34b3b0
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240371"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Způsob uplatňování slev za rezervaci na Azure Database for MySQL

Když si zakoupíte rezervovanou kapacitu Azure Database for MySQL, na servery MySQL, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá pouze náklady na výpočetní prostředky Azure Database for MySQL. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se * **nenahrazuje** _. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.</br>

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Sleva uplatněná na Azure Database for MySQL

Sleva za rezervovanou kapacitu Azure Database for MySQL se na provoz serverů MySQL uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými servery Azure Database for MySQL. V případě serverů MySQL, které neběží celou hodinu, se rezervace automaticky využije pro jiný server Azure Database for MySQL vyhovující atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné servery Azure Database for MySQL. Pokud nemáte server MySQL, který běží celou hodinu a vyhovuje atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Database for MySQL uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

_ **Příklad 1** : Zakoupíte si rezervovanou kapacitu Azure Database for MySQL pro 8 virtuálních jader. Pokud používáte server Azure Database for MySQL s 16 virtuálními jádry, který vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků pro 8 virtuálních jader serveru MySQL se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků pro server MySQL s 8 virtuálními jádry získáte slevu za rezervaci.</br>

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Database for MySQL pro 16 virtuálních jader a ostatní atributy rezervace odpovídají spuštěným izolovaným serverům MySQL.

* **Příklad 2:** Na hodinu spustíte dva servery Azure Database for MySQL, z nichž každý má 8 virtuálních jader. Sleva za rezervaci 16 virtuálních jader se uplatní na využití výpočetních prostředků pro oba servery Azure Database for MySQL s 8 virtuálními jádry.

* **Příklad 3:** Od 13:00 do 13:30 spustíte jeden server Azure Database for MySQL s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný server Azure Database for MySQL s 16 virtuálními jádry. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4:** Od 13:00 do 13:45 spustíte jeden server Azure Database for MySQL s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný server Azure Database for MySQL s 16 virtuálními jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).