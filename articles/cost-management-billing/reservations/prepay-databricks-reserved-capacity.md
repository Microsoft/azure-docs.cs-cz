---
title: Optimalizace nákladů na Azure Databricks díky předběžnému nákupu
description: Přečtěte si, jak si předplatit náklady na Azure Databricks pomocí rezervované kapacity a ušetřit.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b7d7380114046fbeaaf4b6eb2660997d64168715
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368562"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optimalizace nákladů na Azure Databricks díky předběžnému nákupu

Na nákladech na jednotku Azure Databricks (DBU) můžete ušetřit předběžným nákupem jednotek potvrzení Azure Databricks (DBCU) na jeden nebo tři roky. Předem zakoupené jednotky DBCU můžete využít kdykoli během zakoupeného období. Na rozdíl od virtuálních počítačů nevyprší platnost předem zakoupených jednotek po každé hodině, takže je můžete využít kdykoli během zakoupeného období.

Od předem zakoupených jednotek DBU se automaticky odečítá veškeré využití služby Azure Databricks. K tomu, abyste získali slevy za předběžný nákup, nemusíte na využití jednotek DBU opakovaně nasazovat předem zakoupený plán ani jej přiřazovat k pracovním prostorům Azure Databricks.

Slevy za předem zakoupené využití se vztahují jenom na využití jednotek DBU. Další poplatky, například za výpočetní prostředky, úložiště a prostředky sítě, se účtují zvlášť.

## <a name="determine-the-right-size-to-buy"></a>Určení správné velikosti, kterou zakoupit

Předběžný nákup Databricks se vztahuje na všechny úlohy a úrovně služby Databricks. Předběžný nákup si můžete představit jako fond předplacených jednotek potvrzení Databricks. Využití se odečítá z tohoto fondu bez ohledu na zvolenou úlohu nebo úroveň. Využití se odečítá s následujícími koeficienty:

| **Úloha** | **Koeficient uplatňování DBU – úroveň Standard** | **Koeficient uplatňování DBU – úroveň Premium** |
| --- | --- | --- |
| Analýza dat | 0,4 | 0,55 |
| Příprava dat | 0,15 | 0,30 |
| Jednoduchá příprava dat | 0,07 | 0,22 |

Například když se spotřebuje určité množství datových analýz na úrovni Standard, od předem zakoupených jednotek potvrzení Databricks se odečte 0,4 jednotky.

Před nákupem si vypočtěte celkové množství DBU spotřebované různými úlohami a úrovněmi. Pomocí uvedených koeficientů je převeďte na DBCU a pak spusťte projekci celkového využití během příštího roku nebo tří let.

## <a name="purchase-databricks-commit-units"></a>Jednotky potvrzení Databricks (DBCU)

Plány Databricks si můžete zakoupit na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Pokud si chcete koupit rezervovanou kapacitu, musíte mít roli vlastníka aspoň u jednoho předplatného se smlouvou Enterprise.

- Musíte mít roli Vlastník aspoň u jedné smlouvy Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo smlouvy se zákazníkem Microsoftu nebo individuální předplatné s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P).
- U předplatných se smlouvou Enterprise musí být na portálu EA povolená možnost Přidat rezervované instance. Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro příslušné předplatné.
- U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro dané předplatné.

**Postup při nákupu:**

1. Přejděte na [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Vyberte předplatné. V seznamu **Předplatné** vyberte předplatné, které chcete použít k úhradě rezervované kapacity. Náklady na rezervovanou instanci se vyfakturují pomocí způsobu platby vybraného pro dané předplatné. Poplatky se strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
1. Vyberte rozsah. Rozsah předplatného vyberte v seznamu **Rozsah**:
    - Rozsah **Jedna skupina prostředků** – sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
    - Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
    - Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace příslušná registrace.
1. Vyberte, kolik jednotek potvrzení Azure Databricks chcete zakoupit, a dokončete nákup.


![Příklad nákupu Azure Databricks na webu Azure Portal](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Změna rozsahu a vlastnictví

V zakoupené rezervaci můžete provádět následující typy změn:

- Aktualizace rozsahu rezervace
- Řízení přístupu na základě role Azure (Azure RBAC)

Předem zakoupené jednotky potvrzení Databricks není možné rozdělit ani sloučit. Další informace o správě rezervací najdete v tématu [Správa rezervací po nákupu](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Plány předběžného nákupu služby Databricks nepodporují rušení ani výměny. Všechny nákupy jsou definitivní.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
  - [Vysvětlení způsobu uplatňování slevy za předběžně zakoupené jednotky DBCU Azure Databricks](reservation-discount-databricks.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
