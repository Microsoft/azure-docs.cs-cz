---
title: Vysvětlení slev za rezervaci ve službě Azure SQL Databases | Microsoft Docs
description: Zjistěte, jak se na spuštěné databáze služby Azure SQL Database uplatňuje sleva za rezervaci.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: f7382fc81bbb7e0f3ac61b19c1efa1e7de3e9ed7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995465"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Způsob uplatňování slev za rezervaci na databáze služby Azure SQL Database

Když si zakoupíte rezervovanou kapacitu služby Azure SQL Database, na prostředky SQL Database, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá náklady na výpočetní prostředky služby SQL Database. Poplatky za software, úložiště a sítě se vám budou účtovat za použití normálních sazeb. Licenční náklady na databáze SQL Database můžete pokrýt pomocí [zvýhodněného hybridního využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Informace o rezervovaných instancích virtuálních počítačů najdete v tématu [Vysvětlení slevy za rezervované instance virtuálních počítačů Azure](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

*Nevyužité slevy se nenahrazují*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny nelze převést na další období.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="discount-applied-to-sql-databases"></a>Sleva uplatněná na databáze SQL

 Sleva za rezervovanou kapacitu databází SQL se na spuštěné databáze SQL uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými databázemi SQL. V případě služeb SQL Database, které neběží celou hodinu, se rezervace automaticky využije pro ostatní služby SQL Database odpovídající atributům rezervace. Vaše sleva se použije také pro databáze SQL, které běží současně. Pokud nemáte databáze SQL, které běží celou hodinu a odpovídají atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu služby SQL Database uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

- Scénář 1: koupíte SQL Database rezervovanou kapacitu pro 8 jader SQL Database. Spustíte databázi SQL se 16 jádry, která vyhovuje všem zbývajícím atributům rezervace. Naúčtuje se vám průběžná platba za 8 jader využití výpočetních prostředků služby SQL Database. Na jednu hodinu získáte slevu za rezervaci využití výpočetních prostředků pro databázi SQL s 8 jádry.

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu SQL Database pro databázi SQL se 16 jádry a spuštěné databáze SQL vyhovují všem zbývajícím atributům rezervace.

- Scénář 2: za hodinu spustíte dvě databáze SQL s 8 jádry. Na využití výpočetních prostředků pro obě databáze SQL s 8 jádry se uplatní sleva za rezervaci 16 jader.
- Scénář 3: spuštění 1 16 Core SQL Database od 1 ODP do 1:30 ODP. Od 13:30 do 14:00 spustíte jinou databázi SQL se 16 jádry. Na obě se bude vztahovat sleva za rezervaci.
- Scénář 4: spustíte 1 16 Core SQL Database od 1 ODP do 1:45 odp. Od 13:30 do 14:00 spustíte jinou databázi SQL se 16 jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)
