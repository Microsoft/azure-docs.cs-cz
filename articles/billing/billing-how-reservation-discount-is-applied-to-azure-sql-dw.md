---
title: Principy uplatňování slev za rezervace u služby Azure SQL Data Warehouse | Microsoft Docs
description: Zjistěte, jak se uplatňují slevy za rezervace u služby Azure SQL Data Warehouse, a ušetřete.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 2520b9142c377bd84ac0d9c09b56ba17b6e42522
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226240"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Principy uplatňování slev za rezervace u služby Azure SQL Data Warehouse

Po zakoupení rezervované kapacity Azure SQL Data Warehouse se sleva za rezervaci automaticky uplatní na datové sklady v rámci dané oblasti. Sleva za rezervaci se uplatňuje na využití naměřené měřičem cDWU služby SQL Data Warehouse. Úložiště a sítě se účtují podle tarifů průběžných plateb.

## <a name="reservation-discount-application"></a>Uplatnění slevy za rezervaci

Sleva za rezervovanou kapacitu SQL Data Warehouse se na spuštěné datové sklady uplatňuje po hodinách. Pokud nemáte během některé hodiny nasazený žádný datový sklad, zůstane rezervovaná kapacita pro danou hodinu nevyužitá. Nepřevádí se.

Po nákupu se zakoupená rezervace přiřadí k využití služby SQL Data Warehouse naměřené u spuštěných datových skladů. Pokud některé datové sklady vypnete, slevy za rezervace se automaticky uplatní na ostatní vyhovující datové sklady.

V případě datových skladů, které nejsou spuštěné celou hodinu, se rezervace automaticky uplatní na ostatní vyhovující instance spuštěné v danou hodinu.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervovanou kapacitu SQL Data Warehouse v závislosti na druhu nasazení.

- **Příklad 1:** Zakoupíte 5 jednotek rezervované kapacity 100 cDWU. Na hodinu spustíte instanci SQL Data Warehouse DW1500c. V takovém případě naměřené využití odpovídá 15 jednotkám využití 100 cDWU. Sleva za rezervaci se uplatní na 5 jednotek, které jste využili. Zbývajících 10 využitých jednotek využití 100 cDWU se vám bude účtovat podle tarifů průběžných plateb.

- **Příklad 2:** Zakoupíte 5 jednotek rezervované kapacity 100 cDWU. Na hodinu spustíte dvě instance SQL Data Warehouse DW100c. V takovém případě se naměří dvě události využití 1 jednotky využití 100 cDWU. Slevy za rezervovanou kapacitu se uplatní u obou událostí využití. Zbývající 3 jednotky rezervované kapacity 100 cDWU zůstanou nevyužité a nepřevedou se do budoucna.

- **Příklad 3:** Zakoupíte 1 jednotku rezervované kapacity 100 cDWU. Spustíte dvě instance SQL Data Warehouse DW100c. Obě jsou spuštěné 30 minut. V takovém případě se slevy za rezervovanou kapacitu uplatní u obou událostí využití. Podle tarifů průběžných plateb se neúčtuje žádné využití.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

- Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](billing-save-compute-costs-reservations.md)
- [Zobrazení transakcí rezervací](billing-view-reservations.md)
- [Získání transakcí a využití rezervací prostřednictvím rozhraní API](billing-reservation-apis.md)
- [Správa rezervací](billing-manage-reserved-vm-instance.md)
