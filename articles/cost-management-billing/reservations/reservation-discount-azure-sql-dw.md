---
title: Principy uplatňování slev za rezervace u služby Azure Synapse Analytics | Microsoft Docs
description: Zjistěte, jak se uplatňují slevy za rezervace u služby Azure Synapse Analytics, a ušetřete.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: f0bd57befc0ec30473065ac69026a77e21e30e5c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682599"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>Principy uplatňování slev za rezervace u služby Azure Synapse Analytics

Po zakoupení rezervované kapacity Azure Synapse Analytics se sleva za rezervaci automaticky uplatní na zřízené instance v rámci dané oblasti. Sleva za rezervaci se uplatňuje na využití naměřené měřičem cDWU služby Azure Synapse Analytics. Úložiště a sítě se účtují podle tarifů průběžných plateb.

## <a name="reservation-discount-application"></a>Uplatnění slevy za rezervaci

Sleva za rezervovanou kapacitu Azure Synapse Analytics se na spuštěné datové sklady uplatňuje po hodinách. Pokud nemáte během některé hodiny nasazený žádný datový sklad, zůstane rezervovaná kapacita pro danou hodinu nevyužitá. Nepřevádí se.

Po nákupu se zakoupená rezervace přiřadí k využití služby Azure Synapse Analytics naměřené u spuštěných datových skladů. Pokud některé datové sklady vypnete, slevy za rezervace se automaticky uplatní na ostatní vyhovující datové sklady.

V případě datových skladů, které nejsou spuštěné celou hodinu, se rezervace automaticky uplatní na ostatní vyhovující instance spuštěné v danou hodinu.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervovanou kapacitu služby Azure Synapse Analytics v závislosti na druhu nasazení.

- **Příklad 1:** Zakoupíte 5 jednotek rezervované kapacity 100 cDWU. Na hodinu spustíte instanci Azure Synapse Analytics DW1500c. V takovém případě naměřené využití odpovídá 15 jednotkám využití 100 cDWU. Sleva za rezervaci se uplatní na 5 jednotek, které jste využili. Zbývajících 10 využitých jednotek využití 100 cDWU se vám bude účtovat podle tarifů průběžných plateb. Jinak řečeno, pro více rezervací je možné částečné krytí.

- **Příklad 2:** Zakoupíte 5 jednotek rezervované kapacity 100 cDWU. Na hodinu spustíte dvě instance Azure Synapse Analytics DW100c. V takovém případě se naměří dvě události využití 1 jednotky využití 100 cDWU. Slevy za rezervovanou kapacitu se uplatní u obou událostí využití. Zbývající 3 jednotky rezervované kapacity 100 cDWU zůstanou nevyužité a nepřevedou se do budoucna. Jinak řečeno, jedna rezervace se může spárovat s více instancemi Azure Synapse Analytics.

- **Příklad 3:** Zakoupíte 1 jednotku rezervované kapacity 100 cDWU. Spustíte dvě instance Azure Synapse Analytics DW100c. Obě jsou spuštěné 30 minut. V takovém případě se slevy za rezervovanou kapacitu uplatní u obou událostí využití. Podle tarifů průběžných plateb se neúčtuje žádné využití.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

- Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- [Zobrazení transakcí rezervací](view-reservations.md)
- [Získání transakcí a využití rezervací prostřednictvím rozhraní API](reservation-apis.md)
- [Správa rezervací](manage-reserved-vm-instance.md)
