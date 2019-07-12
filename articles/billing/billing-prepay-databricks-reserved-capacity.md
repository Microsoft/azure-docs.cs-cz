---
title: Optimalizace nákladů na Azure Databricks pomocí předběžného nákupu
description: Zjistěte, jak si můžete Předplatíte poplatky za Azure Databricks vyhrazené kapacitě ještě ušetříte peníze.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811255"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optimalizace nákladů na Azure Databricks pomocí předběžného nákupu

Můžete uložit v Azure Databricks jednotek (DBU) ušetřit předběžného nákupu Azure Databricks potvrzení jednotky (DBCU) pro jeden nebo tři roky. Můžete použít předem zakoupené DBCUs kdykoli během období nákupu. Na rozdíl od virtuálních počítačů, předem zakoupené jednotky nevyprší po hodinách a použít kdykoli během období nákupu.

Jakékoli použití Azure Databricks odečte snížení z předem zakoupené Dbu automaticky. Nemusíte znovu provádět nasazení ani přiřadit pracovní prostory Azure Databricks za DBU využití k získání slevy předběžného nákupu předem zakoupené plán.

Sleva předběžného nákupu se vztahuje pouze na DBU využití. Další poplatky, jako jsou výpočty, úložiště a sítě se účtují samostatně.

## <a name="determine-the-right-size-to-buy"></a>Určení správné velikosti koupit

Databricks předběžného nákupu se vztahuje na všechny úrovně a úloh Databricks. Před nákupem jako fond předplacené jednotek Databricks potvrzení změn si můžete představit. Využití se odečte od fondu, bez ohledu na to, úlohy nebo úroveň. Využití je odečtena poměr následující:

| **Úloha** | **Poměr aplikace DBU – úroveň Standard** | **Poměr aplikace DBU – úroveň Premium** |
| --- | --- | --- |
| Analýza dat | 0.4 | 0.55 |
| Příprava dat | 0.15 | 0,30 |
| Jednoduchá příprava dat | 0,07 | 0.22 |

Když množství Data Analytics – úroveň Standard je zpracován, předem zakoupené jednotky Databricks potvrzení se odečte 0.4 jednotky.

Před nákupem, vypočítejte celkový počet DBU množství využité pro různé úlohy a úrovně. Pomocí předchozího poměry normalizovat DBCU a pak spusťte projekce celkového využití nad další jeden nebo tři roky.

## <a name="purchase-databricks-commit-units"></a>Jednotek Databricks potvrzení nákupu

Databricks plány si můžete koupit [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Koupit rezervované kapacity, musíte mít roli vlastníka pro předplatné aspoň jeden enterprise.

- V současné době předběžného nákupu je dostupná jenom pro zákazníky se smlouvou Enterprise.
- Musí být v roli vlastník pro alespoň jedno předplatné Enterprise.
- Předplatné Enterprise **přidat Reserved Instances** musí být povolená v [portál EA](https://ea.azure.com/). Nebo, pokud je toto nastavení zakázané, musíte být správcem předplatného EA.

**K nákupu:**

1. Přejděte na [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Vyberte předplatné. Použití **předplatné** seznam a vyberte předplatné, které umožňuje platit za rezervované kapacity. Způsob platby předplatného se účtuje pořizovací náklady pro záložní kapacitu. Poplatky se odečteno od zůstatku peněžním závazkem registrace nebo účtovat jako překročení částky.
1. Vyberte obor. Použití **oboru** seznam a vyberte oboru předplatného:
    - **Jednotného oboru skupiny prostředků** – platí pro odpovídající prostředky ve vybrané skupině prostředků pouze sleva za rezervaci.
    - **Jednotného oboru předplatného** – sleva za rezervaci se vztahuje na odpovídající prostředkům ve vybraném předplatném.
    - **Sdílený obor** – sleva za rezervaci se vztahuje na odpovídající zdroje v oprávněné předplatné, které jsou v kontextu fakturace. Pro zákazníky se smlouvou Enterprise je kontext fakturace registraci.
1. Vyberte počet jednotek Azure Databricks potvrzení chcete zakoupit a dokončete nákup.


![Příklad zobrazující nákupu Azure Databricks na portálu Azure portal](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Změnit obor a vlastnictví

Rezervace po nákupu můžete provádět následující typy změn:

- Aktualizovat obor rezervace
- Přístup na základě rolí

Nelze rozdělit nebo sloučit předběžného nákupu jednotek Databricks potvrzení. Další informace o správě rezervace, naleznete v tématu [Správa po nákupu rezervace](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Zrušit a exchange není podporována pro plány předběžného nákupu Databricks. Všechny nákupy se nedá vrátit zpět.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- Další informace o rezervacích Azure, najdete v následujících článcích:
  - [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
  - [Vysvětlení, jak se použije slevu DBCU předběžného nákupu Azure Databricks](billing-reservation-discount-databricks.md)
  - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
