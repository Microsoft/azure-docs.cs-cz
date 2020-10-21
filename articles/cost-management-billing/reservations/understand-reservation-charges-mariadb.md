---
title: Princip slev za rezervaci – Azure Database for MariaDB
description: Projděte si způsob uplatňování slev za rezervaci na Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 0691ec320b62ed212235a63dacedcf3ec38a23ba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151461"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Způsob uplatňování slev za rezervaci na Azure Database for MariaDB

Když si zakoupíte rezervovanou kapacitu Azure Database for MariaDB, na servery MariaDB, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá pouze náklady na výpočetní prostředky Azure Database for MariaDB. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se ***nenahrazuje***. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Sleva uplatněná na Azure Database for MariaDB

Sleva za rezervovanou kapacitu Azure Database for MariaDB se na provoz serverů MariaDB uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými servery Azure Database for MariaDB. V případě serverů MariaDB, které neběží celou hodinu, se rezervace automaticky využije pro jiný server Azure Database for MariaDB vyhovující atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné servery Azure Database for MariaDB. Pokud nemáte server MariaDB, který běží celou hodinu a vyhovuje atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Database for MariaDB uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

**Příklad 1:** Zakoupíte si rezervovanou kapacitu Azure Database for MariaDB pro 8 virtuálních jader. Pokud používáte server Azure Database for MariaDB s 16 virtuálními jádry, který vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků pro 8 virtuálních jader serveru MariaDB se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků pro server MariaDB s 8 virtuálními jádry získáte slevu za rezervaci.

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Database for MariaDB pro 16 virtuálních jader a ostatní atributy rezervace odpovídají spuštěným izolovaným serverům MariaDB.

* **Příklad 2:** Na hodinu spustíte dva servery Azure Database for MariaDB, z nichž každý má 8 virtuálních jader. Sleva za rezervaci 16 virtuálních jader se uplatní na využití výpočetních prostředků pro oba servery Azure Database for MariaDB s 8 virtuálními jádry.

* **Příklad 3:** Od 13:00 do 13:30 spustíte jeden server Azure Database for MariaDB s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný server Azure Database for MariaDB s 16 virtuálními jádry. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4:** Od 13:00 do 13:45 spustíte jeden server Azure Database for MariaDB s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný server Azure Database for MariaDB s 16 virtuálními jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).