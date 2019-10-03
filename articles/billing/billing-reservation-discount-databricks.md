---
title: Způsob uplatňování slevy za předběžný nákup služby Azure Databricks
description: Zjistěte, jak se na využití uplatňuje sleva za předběžný nákup služby Azure Databricks.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9651a61cb7b5c995a46571b7628d5416c08f4161
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719671"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Způsob uplatňování slevy za předběžný nákup služby Azure Databricks

Předem zakoupené jednotky potvrzení Azure Databricks (DBCU) můžete využít kdykoli během zakoupeného období. Od předem zakoupených jednotek DBCU se automaticky odečítá veškeré využití služby Azure Databricks.

Na rozdíl od virtuálních počítačů nevyprší platnost předem zakoupených jednotek po každé hodině. Můžete je využít kdykoli během zakoupeného období. K tomu, abyste získali slevy za předběžný nákup, nemusíte na využití opakovaně nasazovat předem zakoupený plán ani jej přiřazovat k pracovním prostorům Azure Databricks.

Slevy za předem zakoupené využití se vztahují jenom na využití jednotek Azure Databricks (DBU). Další poplatky, například za výpočetní prostředky, úložiště a prostředky sítě, se účtují zvlášť.

## <a name="pre-purchase-discount-application"></a>Uplatnění slevy za předběžný nákup

Předběžný nákup Databricks se vztahuje na všechny úlohy a úrovně služby Databricks. Předběžný nákup si můžete představit jako fond předplacených jednotek potvrzení Databricks. Využití se odečítá z tohoto fondu bez ohledu na zvolenou úlohu nebo úroveň. Využití se odečítá s následujícími koeficienty:

| **Úloha** | **Koeficient uplatňování DBU – úroveň Standard** | **Koeficient uplatňování DBU – úroveň Premium** |
| --- | --- | --- |
| Analýza dat | 0,4 | 0,55 |
| Příprava dat | 0,15 | 0,30 |
| Jednoduchá příprava dat | 0,07 | 0,22 |

Například když se spotřebuje určité množství datových analýz na úrovni Standard, od předem zakoupených jednotek potvrzení Databricks se odečte 0,4 jednotky. Když se využije v určitém objemu jednoduchá příprava dat na úrovni Standard, od předem zakoupených jednotek potvrzení Databricks se odečte 0,07 jednotky.

## <a name="determine-plan-use"></a>Určení využití plánu

Pokud chcete určit využití plánu DBCU, na webu Azure Portal přejděte do části **Rezervace** a klikněte na zakoupený plán služby Databricks. Zobrazí se vaše využití k aktuálnímu datu a všechny zbývající jednotky. Další informace o určení využití rezervací najdete v článku [Zobrazení využití rezervací](billing-reservation-apis.md#see-reservation-usage).

## <a name="how-discount-application-shows-in-usage-data"></a>Jak se uplatnění slevy projeví v datech o využití

Když na využití služby Databricks uplatníte slevu za předběžný nákup, v datech o využití se zobrazí nulové poplatky za vyžádání. Další informace o nákladech na rezervace a jejich využití najdete v tématu [Získání nákladů na rezervace a jejich využití v rámci smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervace, najdete v tématu [Správa rezervací Azure](billing-manage-reserved-vm-instance.md).
- Další informace o úspoře peněz předběžným nákupem služby Azure Databricks najdete v tématu [Optimalizace nákladů na Azure Databricks díky předběžnému nákupu](billing-prepay-databricks-reserved-capacity.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací v Azure](billing-manage-reserved-vm-instance.md)
  - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
