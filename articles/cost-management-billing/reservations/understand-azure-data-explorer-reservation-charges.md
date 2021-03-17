---
title: Vysvětlení uplatňování slevy za rezervaci na Azure Data Explorer
description: Zjistěte, jak se uplatňuje sleva za rezervaci na měřič přirážky za Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: b222e4ff519fbe2fbb4fa58b39555ac9fead0917
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352911"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Vysvětlení uplatňování slevy za rezervaci na Azure Data Explorer

Když si zakoupíte rezervovanou kapacitu Azure Data Exploreru, na prostředky Azure Data Exploreru, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace zahrnuje přirážku za Azure Data Explorer. Nezahrnuje výpočetní prostředky, síťové prostředky, prostředky úložiště ani žádné jiné prostředky Azure sloužící k provozu clusteru Azure Data Exploreru. Rezervace těchto prostředků by se měly kupovat zvlášť.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se *nenahrazuje*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Sleva za rezervaci uplatněná na clustery Azure Data Exploreru

Sleva za rezervaci se na přirážku za Azure Data Explorer uplatňuje po hodinách. V případě prostředků Azure Data Exploreru, které neběží celou hodinu, se sleva za rezervaci automaticky uplatní na jiné prostředky Data Exploreru, které vyhovují atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné prostředky Azure Data Exploreru. Pokud nemáte prostředky Azure Data Exploreru, které běží celou hodinu a vyhovují atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

> [!NOTE]
> * Pokud chcete maximalizovat úspory za rezervovanou kapacitu, **důrazně doporučujeme** zakoupit [rezervovanou kapacitu](../../virtual-machines/prepay-reserved-vm-instances.md) pro virtuální počítače, které využívá cluster Azure Data Exploreru.
> * Po zakoupení rezervace se slevy uplatňují ve všech oblastech.

## <a name="examples"></a>Příklady

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Data Exploreru uplatňuje v závislosti na počtu zakoupených jednotek přirážky a na tom, kdy jsou spuštěné.
Například v případě clusteru modulu se **2 virtuálními počítači řady D11_v2** se vám budou účtovat celkové poplatky za vyžádání čtyř jednotek měřiče přirážky za Azure Data Explorer za hodinu.

**Scénář 1**

Zakoupíte si rezervovanou kapacitu Azure Data Exploreru pro 8 jednotek přirážky za Azure Data Explorer. Spustíte cluster modulu se dvěma virtuálními počítači řady D13_v2 s celkovým počtem 16 jader, za který se účtuje 16 jednotek přirážky za Azure Data Explorer za hodinu a který vyhovuje ostatním atributům rezervace. Za využití výpočetních prostředků pro osm jader Azure Data Exploreru se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití jednotek přirážky za Azure Data Explorer s osmi jádry získáte slevu za rezervaci.

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Data Exploreru pro cluster Azure Data Exploreru s 16 jádry a ostatní atributy rezervace odpovídají spuštěnému clusteru Azure Data Exploreru.

**Scénář 2**

Ve dvou různých oblastech na hodinu spustíte dva clustery modulu Azure Data Exploreru, z nichž každý má osm jader. Sleva za rezervaci 16 jader se uplatní na oba clustery a na 16 jednotek přirážky za Azure Data Explorer, které spotřebují.

**Scénář 3**

Od 13:00 do 13:30 spustíte jeden cluster modulu Azure Data Exploreru s 16 jádry. Od 13:30 do 14:00 spustíte jiný cluster modulu Azure Data Exploreru s 16 jádry. Na obě se bude vztahovat sleva za rezervaci.

**Scénář 4**

Od 13:00 do 13:45 spustíte jeden cluster modulu Azure Data Exploreru s 16 jádry. Od 13:30 do 14:00 spustíte jiný cluster modulu Azure Data Exploreru s 16 jádry. Za 15 minut, kdy se doba chodu obou clusterů překrývá, se vám bude účtovat cena při průběžných platbách. Po zbytek doby se na využití přirážky za Azure Data Explorer uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

* [Předplacení výpočetních prostředků Azure Data Exploreru s využitím rezervované kapacity Azure Data Exploreru](/azure/data-explorer/pricing-reserved-capacity)  
* [Co jsou rezervace v Azure](save-compute-costs-reservations.md)  
* [Správa rezervací Azure](manage-reserved-vm-instance.md)  
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
* [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
* [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)