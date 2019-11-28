---
title: Úspora nákladů na poplatky za SQL Data Warehouse pomocí rezervované kapacity Azure
description: Přečtěte si, jak pomocí rezervované kapacity ušetřit náklady na poplatky za SQL Data Warehouse a dosáhnout tak úspory peněz.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 75e008a6a19baacec40a809530e3a1f624a33f2b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223623"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Úspora nákladů na poplatky za SQL Data Warehouse pomocí rezervované kapacity

U služby Azure SQL Data Warehouse můžete ušetřit, když se rozhodnete rezervovat využití jednotek cDWU na dobu jednoho roku nebo tří let. Pokud chcete zakoupit rezervovanou kapacitu služby SQL Data Warehouse, musíte zvolit oblast Azure a období. Pak do košíku vložíte skladovou položku (SKU) služby SQL Data Warehouse a zvolíte množství jednotek cDWU, které chcete zakoupit.

Když zakoupíte rezervaci, využití služby SQL Data Warehouse odpovídající atributům rezervace se už nebude účtovat podle sazeb pro průběžné platby.

Rezervace se nevztahuje na poplatky za úložiště ani sítě související s využitím služby SQL Data Warehouse.

Když rezervovaná kapacita vyprší, instance služby SQL Data Warehouse poběží dál, ale budou se fakturovat podle sazeb pro průběžné platby. Rezervace se neprodlužují automaticky.

Informace o cenách najdete v [nabídce rezervované kapacity služby SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Rezervovanou kapacitu služby Azure SQL Data Warehouse můžete zakoupit na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](billing-monthly-payments-reservations.md). Pro nákup rezervované kapacity platí:

- Musíte mít roli vlastníka alespoň pro jedno předplatné se smlouvou Enterprise nebo s průběžnými platbami.
- U předplatných se smlouvou Enterprise musí být na portálu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Pokud je toto nastavení zakázané, musíte být správcem EA.
- U programu CSP (Cloud Solution Provider) můžou rezervovanou kapacitu služby SQL Data Warehouse zakoupit jenom agenti správy nebo agenti prodeje.

Další informace o tom, jak se zákazníkům se smlouvou Enterprise a zákazníkům s průběžnými platbami účtují poplatky za nákupy rezervací, najdete v tématech s [vysvětlením využití rezervace Azure u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md) a s [vysvětlením využití rezervace Azure u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Volba správné velikosti před nákupem

Velikost rezervace služby SQL Data Warehouse by měla být založená na celkovém množství výpočetních jednotek datového skladu (cDWU), které využíváte. Nákupy se provádějí v přírůstcích 100 jednotek cDWU.

Předpokládejme například, že vaše celková spotřeba kapacity služby SQL Data Warehouse je DW3000c. Chcete zakoupit rezervovanou kapacitu pro celou tuto spotřebu. Měli byste proto zakoupit 30 jednotek rezervované kapacity cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Zakoupení rezervované kapacity služby SQL Data Warehouse

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte předplatné. K výběru předplatného, které se použije k placení rezervované kapacity, použijte seznam předplatných. Náklady na rezervovanou kapacitu se účtují pomocí způsobu platby zvoleného pro předplatné. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo předplatné s průběžnými platbami (číslo nabídky: MS-AZR-0003P nebo MS-AZR-0023P).
   - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
   - V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.
4. Vyberte rozsah. K výběru rozsahu předplatného použijte seznam Rozsah.
   - Rozsah **Jedna skupina prostředků** – sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
   - Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
   - Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace prováděcí smlouva. U jednotlivých předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.
   - U zákazníků se smlouvou Enterprise je kontextem fakturace prováděcí smlouva EA.
   - U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.
5. Vyberte oblast Azure, která je pokrytá rezervovanou kapacitou.
6. Zvolte množství. Množství zadejte ve stovkách výpočetních jednotek datového skladu (cDWU), které chcete koupit.    
   Například množství 30 vám poskytne 3 000 jednotek cDWU rezervované kapacity každou hodinu.
7. V části **Poplatky** se podívejte, jaké budou náklady na rezervaci kapacity služby SQL Data Warehouse.
8. Vyberte **Koupit**.
9. Stav nákupu zobrazíte tak, že vyberete **Zobrazit tuto rezervaci**.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

Sleva za rezervaci se automaticky uplatní na počet instancí SQL Data Warehouse, které odpovídají rozsahu a oblasti rezervované kapacity SQL Data Warehouse. Rozsah rezervované kapacity SQL Data Warehouse můžete aktualizovat prostřednictvím webu [Azure Portal](https://portal.azure.com/), PowerShellu, rozhraní příkazového řádku nebo rozhraní API.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/).

## <a name="next-steps"></a>Další kroky

- Další informace o uplatňování slev za rezervaci u Azure SQL Data Warehouse najdete v tématu [o způsobu uplatňování slev za rezervaci u služby Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
  - [Vysvětlení slev za rezervace Azure](billing-understand-reservation-charges.md)
  - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
