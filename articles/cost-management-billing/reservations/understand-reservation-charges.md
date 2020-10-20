---
title: Vysvětlení slev za rezervaci ve službě Azure SQL Database | Microsoft Docs
description: Zjistěte, jak se na spuštěné služby Azure SQL Database uplatňuje sleva za rezervaci. Sleva se na tyto databáze uplatňuje po hodinách.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: banders
ms.openlocfilehash: 054641d8136d121e611182c8d8b104aefcbc6481
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057871"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>Způsob uplatňování slev za rezervaci na služby Azure SQL Database

Když si zakoupíte rezervovanou kapacitu služby Azure SQL Database, na databáze SQL, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace se vztahuje na náklady na výpočetní prostředky SQL Database, včetně primární repliky a veškerých fakturovatelných sekundárních replik. Poplatky za software, úložiště a sítě se vám budou účtovat za použití normálních sazeb. Licenční náklady na SQL Database můžete pokrýt pomocí [Zvýhodněného hybridního využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Upozorňujeme, že slevy za rezervace se nevztahují na bezserverovou variantu Azure SQL Database.

Informace o rezervovaných instancích virtuálních počítačů najdete v tématu [Vysvětlení slevy za rezervované instance virtuálních počítačů Azure](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

*Nevyužité slevy se nenahrazují*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="discount-applied-to-running-sql-databases"></a>Sleva uplatněná na spuštěné databáze SQL

Sleva za rezervovanou kapacitu SQL Database se na spuštěné databáze SQL uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými databázemi SQL. V případě databází SQL, které neběží celou hodinu, se rezervace automaticky využije pro ostatní databáze SQL odpovídající atributům rezervace. Tato sleva se použije také pro databáze SQL, které běží současně. Pokud nemáte databáze SQL, které běží celou hodinu a odpovídají atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu služby SQL Database uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

- Scénář 1: Zakoupíte si rezervovanou kapacitu služby SQL Database pro databázi SQL s 8 jádry. Spustíte službu SQL Database se 16 jádry, která vyhovuje všem zbývajícím atributům rezervace. Naúčtuje se vám průběžná platba za 8 jader využití výpočetních prostředků služby SQL Database. Na jednu hodinu získáte slevu za rezervaci využití výpočetních prostředků pro databázi SQL s 8 jádry.

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu SQL Database pro databázi SQL se 16 jádry a spuštěné databáze SQL vyhovují všem zbývajícím atributům rezervace.

- Scénář 2: Spustíte dvě databáze SQL, které mají každá 8 jader a běží jednu hodinu. Na využití výpočetních prostředků pro obě databáze SQL s 8 jádry se uplatní sleva za rezervaci 16 jader.
- Scénář 3: Od 13:00 do 13:30 máte spuštěnou jednu databázi SQL se 16 jádry. Od 13:30 do 14:00 spustíte jinou databázi SQL se 16 jádry. Na obě se bude vztahovat sleva za rezervaci.
- Scénář 4: Od 13:00 do 13:45 máte spuštěnou jednu databázi SQL se 16 jádry. Od 13:30 do 14:00 spustíte jinou databázi SQL se 16 jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.
- Scénář 5: Spustíte jednu 4jádrovou databázi SQL Hyperscale se třemi sekundárními replikami, z nichž každá má 4 jádra. Rezervace se vztahuje na využití výpočetních prostředků primární repliky a všech sekundárních replik.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)
