---
title: Úspora nákladů za SQL Data Warehouse poplatky pomocí rezervované kapacity Azure
description: Přečtěte si, jak ušetřit náklady za SQL Data Warehouse poplatky s rezervovanou kapacitou pro úspory peněz.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806275"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Úspora nákladů za SQL Data Warehouse poplatky s rezervovanou kapacitou

Peníze můžete ušetřit Azure SQL Data Warehouse tím, že zadáte rezervaci za využití cDWU po dobu jednoho nebo tří let. Pokud chcete koupit SQL Data Warehouse rezervovanou kapacitu, musíte zvolit oblast Azure a termín. Pak si můžete do košíku vložit skladovou položku služby SQL Data Warehouse a zvolit množství jednotek cDWU, které si chcete zakoupit.

Když si zakoupíte rezervaci, využití služby SQL Data Warehouse odpovídající atributům rezervace se už nebude účtovat podle sazeb pro průběžné platby.

Rezervace se nevztahuje na poplatky za úložiště ani sítě související s využitím služby SQL Data Warehouse.

Když rezervovaná kapacita vyprší, instance služby SQL Data Warehouse poběží dál, ale budou se fakturovat podle sazeb pro průběžné platby. Rezervace se automaticky neprodlužují.

Informace o cenách najdete v [nabídce SQL Data Warehouse rezervované kapacity](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

V [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)si můžete koupit Azure SQL Data Warehouse rezervovanou kapacitu. Platíte za rezervaci [předem nebo s měsíčními platbami](billing-monthly-payments-reservations.md). Zakoupení rezervované kapacity:

- Musíte mít roli vlastníka alespoň pro jedno předplatné podniku nebo průběžné platby.
- V případě předplatných Enterprise musí být na [portálu EA](https://ea.azure.com/)povolená možnost **Přidat rezervované instance** . Pokud je nastavení zakázané, musíte být správce EA.
- V případě programu Cloud Solution Provider (CSP) mohou SQL Data Warehouse rezervované kapacity koupit pouze agenti správce nebo prodejní agenti.

Další informace o tom, jak se zákazníci s průběžnými platbami účtují za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro registraci v podniku](billing-understand-reserved-instance-usage-ea.md) a [pochopení využití rezervace Azure pro vaše Předplatné](billing-understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="choose-the-right-size-before-purchase"></a>Zvolit správnou velikost před nákupem

Velikost rezervace SQL Data Warehouse by měla být založená na celkovém počtu jednotek datového skladu (cDWU), které využíváte. Nákupy se provádějí v 100 cDWU přírůstcích.

Předpokládejme například, že vaše celková spotřeba SQL Data Warehouse je DW3000c. Chcete zakoupit vyhrazenou kapacitu pro vše. Měli byste si koupit 30 jednotek rezervované kapacity cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Koupit SQL Data Warehouse rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **všechny** > **rezervace**služeb.
3. Vyberte předplatné. Pomocí seznamu předplatných vyberte předplatné, které se používá k placení rezervované kapacity. Platební metodou předplatného se účtují náklady na rezervovanou kapacitu. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo průběžné platby (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P).
   - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
   - V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.
4. Vyberte obor. Pro výběr oboru předplatného použijte seznam rozsah.
   - **Obor jedné skupiny prostředků** – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.
   - **Rozsah jednoho předplatného** – aplikuje slevu na rezervované prostředky ve vybraném předplatném.
   - **Sdílený rozsah** – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky smlouva Enterprise je fakturačním kontextem registrace. U jednotlivých předplatných s tarify průběžných plateb jsou oborem fakturace všechna oprávněná předplatná vytvořená správcem účtu.
   - Pro podnikové zákazníky je fakturačním kontextem registrace EA.
   - Pro zákazníky s průběžnými platbami se sdíleným oborem jsou všechna předplatná s průběžnými platbami vytvořená správcem účtu.
5. Vyberte oblast a zvolte oblast Azure, která je pokrytá rezervovanou kapacitou.
6. Vyberte množství. Zadejte množství 100 jednotek datového skladu (cDWU), které chcete koupit.    
   Například množství 30 vám poskytne 3 000 cDWU rezervované kapacity každou hodinu.
7. Přečtěte si část náklady na rezervované vyhrazené kapacity SQL Data Warehouse v části **náklady** .
8. Vyberte **Koupit**.
9. Pokud chcete zobrazit stav nákupu, vyberte **Zobrazit tuto rezervaci** .

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervace zrušení, výměna nebo refundace

Rezervace můžete zrušit, vyměnit nebo vrátit refundaci s určitými omezeními. Další informace najdete v tématu věnovaném [výměnám samoobslužných služeb a refundacím pro Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

Sleva rezervace se automaticky aplikuje na počet instancí SQL Data Warehouse, které odpovídají SQL Data Warehouse rezervovanému rozsahu kapacity a oblasti. Rozsah SQL Data Warehouse rezervované kapacity můžete aktualizovat pomocí [Azure Portal](https://portal.azure.com/), PowerShellu, CLI nebo přes rozhraní API.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/).

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak se slevy za rezervace vztahují na Azure SQL Data Warehouse, najdete v tématu [jak se vztahují slevy na rezervace na Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Další informace o Azure Reservations najdete v následujících článcích:
  - [Co jsou Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
  - [Pochopení Azure Reservations slevy](billing-understand-reservation-charges.md)
  - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
