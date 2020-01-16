---
title: Způsob uplatňování slev za rezervaci na Azure Database for MySQL
description: Způsob uplatňování slev za rezervaci na Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 7be3a7913559947be1291db35ac3531f152dfae9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995543"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Způsob uplatňování slev za rezervaci na Azure Database for MySQL

Když si zakoupíte rezervovanou kapacitu Azure Database for MySQL, na servery MySQL, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá pouze náklady na výpočetní prostředky Azure Database for MySQL. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb. 

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se ***nenahrazuje***. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny nelze převést na další období.</br>

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Sleva uplatněná na Azure Database for MySQL

Sleva za rezervovanou kapacitu Azure Database for MySQL se na provoz serverů MySQL uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými servery Azure Database for MySQL. V případě serverů MySQL, které neběží celou hodinu, se rezervace automaticky využije pro jiný server Azure Database for MySQL vyhovující atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné servery Azure Database for MySQL. Pokud nemáte server MySQL, který běží celou hodinu a vyhovuje atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Database for MySQL uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

* **Příklad 1**: koupíte Azure Database for MySQL rezervovanou kapacitu pro 8 Vcore. Pokud používáte server Azure Database for MySQL s 16 virtuálními jádry, který vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků pro 8 virtuálních jader serveru MySQL se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků pro server MySQL s 8 virtuálními jádry získáte slevu za rezervaci.</br>

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Database for MySQL pro 16 virtuálních jader a ostatní atributy rezervace odpovídají spuštěným izolovaným serverům MySQL.

* **Příklad 2**: spuštění dvou Azure Database for MySQL serverů s 8 Vcore každou hodinu. Sleva za rezervaci 16 virtuálních jader se uplatní na využití výpočetních prostředků pro oba servery Azure Database for MySQL s 8 virtuálními jádry.

* **Příklad 3**: spustíte 1 16 Azure Database for MySQL Vcore serveru z 1. odp. na 1:30 ODP. Od 13:30 do 14:00 spustíte jiný server Azure Database for MySQL s 16 virtuálními jádry. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4**: spouštíte 1 16 Vcore Azure Database for MySQL server z 1. odp. až 1:45 odp. Od 13:30 do 14:00 spustíte jiný server Azure Database for MySQL s 16 virtuálními jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
