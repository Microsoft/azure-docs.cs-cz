---
title: Způsob uplatňování slev za rezervaci na Azure Database for MariaDB
description: Způsob uplatňování slev za rezervaci na Azure Database for MariaDB
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 340d395278eb924570e07d106308e63be3c2f9af
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995517"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Způsob uplatňování slev za rezervaci na Azure Database for MariaDB

Když si zakoupíte rezervovanou kapacitu Azure Database for MariaDB, na servery MariaDB, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá pouze náklady na výpočetní prostředky Azure Database for MariaDB. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb. 

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se ***nenahrazuje***. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny nelze převést na další období.</br>

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Sleva uplatněná na Azure Database for MariaDB

Sleva za rezervovanou kapacitu Azure Database for MariaDB se na provoz serverů MariaDB uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými servery Azure Database for MariaDB. V případě serverů MariaDB, které neběží celou hodinu, se rezervace automaticky využije pro jiný server Azure Database for MariaDB vyhovující atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné servery Azure Database for MariaDB. Pokud nemáte server MariaDB, který běží celou hodinu a vyhovuje atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Database for MariaDB uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

* **Příklad 1**: koupíte Azure Database for MariaDB rezervovanou kapacitu pro 8 Vcore. Pokud používáte server Azure Database for MariaDB s 16 virtuálními jádry, který vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků pro 8 virtuálních jader serveru MariaDB se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků pro server MariaDB s 8 virtuálními jádry získáte slevu za rezervaci.</br>

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Database for MariaDB pro 16 virtuálních jader a ostatní atributy rezervace odpovídají spuštěným izolovaným serverům MariaDB.

* **Příklad 2**: spuštění dvou Azure Database for MariaDB serverů s 8 Vcore každou hodinu. Sleva za rezervaci 16 virtuálních jader se uplatní na využití výpočetních prostředků pro oba servery Azure Database for MariaDB s 8 virtuálními jádry.

* **Příklad 3**: spustíte 1 16 Azure Database for MariaDB Vcore serveru z 1. odp. na 1:30 ODP. Od 13:30 do 14:00 spustíte jiný server Azure Database for MariaDB s 16 virtuálními jádry. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4**: spouštíte 1 16 Vcore Azure Database for MariaDB Server z 1. odp. až 1:45 odp. Od 13:30 do 14:00 spustíte jiný server Azure Database for MariaDB s 16 virtuálními jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
