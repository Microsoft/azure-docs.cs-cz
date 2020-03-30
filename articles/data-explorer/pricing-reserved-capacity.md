---
title: Předplatit značky Azure Data Explorer ušetřit peníze
description: Přečtěte si, jak koupit rezervovanou kapacitu Azure Data Exploreru, abyste ušetřili náklady na Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969264"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Platba předem pro značkovací jednotky Azure Data Explorer u rezervované kapacity Azure Data Exploreru

Ušetřete peníze s Azure Data Explorer ušetřete předplacením za jednotky značek ve srovnání s průběžnými platbami. S rezervovanou kapacitou Aplikace Azure Data Explorer se zavazujete, že Azure Data Explorer bude používat po dobu jednoho nebo tří let, abyste získali výraznou slevu na přirážky k Azure Data Exploreru. Chcete-li zakoupit rezervovanou kapacitu Aplikace Azure Data Explorer, stačí zadat termín, bude se vztahovat na všechna nasazení Průzkumníka dat Azure ve všech oblastech.

Zakoupením rezervace platíte předem náklady na přirážku po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za značky Azure Data Explorer, které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. Azure Data Explorer clustery, které jsou již spuštěné nebo ty, které jsou nově nasazené automaticky získáte výhodu. Tato rezervace se nevztahuje na výpočetní prostředky, síťové nebo tarify úložiště spojené s clustery. Rezervovanou kapacitu pro tyto prostředky je třeba zakoupit samostatně. Na konci období rezervace vyprší platnost fakturační výhody a značkovací jednotky Průzkumníka dat Azure se účtují za průběžnou platbu. Rezervace se neobnovují automaticky. Informace o cenách najdete na [stránce s cenami v Průzkumníku dat Azure](https://azure.microsoft.com/pricing/details/data-explorer/).

Rezervovanou kapacitu Azure Data Exploreru si můžete koupit na [webu Azure Portal](https://portal.azure.com). Zakoupení rezervované kapacity Aplikace Azure Data Explorer:

* Musíte být vlastníkem alespoň jednoho předplatného Enterprise nebo Pay-You-Go.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com) povolená možnost **Přidat rezervované instance**. Případně pokud je toto nastavení zakázáno, musíte být správcem EA v předplatném.
* V případě programu Zprostředkovatel cloudových řešení (CSP) mohou rezervovanou kapacitu aplikace Azure Data Explorer zakoupit jenom agenti správce nebo prodejci.

Podrobnosti o tom, jak se zákazníkům podnikových a zákazníkům s průběžným inami účtují poplatky za nákupy rezervací, najdete v tématu:
* [Principy využití rezervací Azure pro registraci enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Seznamte se s využitím rezervací Azure pro předplatné s průběžným platbaem](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Určení správného využití přirážky před nákupem

Velikost rezervace by měla být založená na celkovém počtu značkovacích jednotek Průzkumníka dat Azure používaných existujícími clustery Azure Data Explorer nebo brzy nasazené. Počet značkovacích jednotek se rovná počtu jader clusteru Clusteru Azure Data Explorer v produkčním prostředí (bez dat a test skladové položky). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Zakoupení rezervované kapacity Azure Data Exploreru

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **Možnost Všechny služby** > **Reservations** > **Rezervace nákup nyní**. Vyberte **Přidat**.
1. V podokně **Vybrat typ produktu** vyberte Azure Data **Explorer** a zakupte novou rezervaci pro značkovací jednotky Průzkumníka dat Azure. 
1. Vybrat **koupit**
1. Vyplňte požadovaná pole. 

    ![Stránka nákupu](media/pricing-reserved-capacity/purchase-page.png)

1. Zkontrolujte náklady na rezervaci rezervované kapacity služby Azure Data Explorer v části **Náklady.**
1. Vyberte **Koupit**.
1. Chcete-li zobrazit stav nákupu, vyberte možnost **Zobrazit tuto rezervaci.**

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Pokud potřebujete zrušit rezervaci rezervované kapacity aplikace Azure Data Explorer, může být 12 % poplatek za předčasné ukončení. Refundace jsou založeny na nejnižší ceně vaší kupní ceny nebo aktuální ceně rezervace. Výše vrácených peněz je omezená na 50 000 USD za rok. Výše vrácených peněz, které obdržíte, je zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Pokud chcete požádat o zrušení, přejděte na rezervaci na webu Azure portal a kliknutím na **refundaci** vytvořte žádost o podporu.

Pokud potřebujete změnit rezervaci rezervované kapacity Azure Data Explorer na jiný termín, můžete ji vyměnit za jinou rezervaci, která má stejnou nebo vyšší hodnotu. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. Období 1 nebo 3 roky začíná vytvořením nové rezervace. Pokud chcete požádat o výměnu, přejděte na rezervaci na webu Azure Portal a kliknutím na **Exchange** vytvořte žádost o podporu.

Další informace o výměně nebo refundaci rezervací naleznete v [tématu Rezervace a refundace](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Správa rezervace rezervované kapacity

Sleva na rezervaci značkovacích jednotek Průzkumníka dat Azure se automaticky použije na počet jednotek značek, které odpovídají oboru rezervace rezervované kapacity a atributům Průzkumníka dat Azure. 


> [!NOTE]
> * Rozsah rezervace rezervované kapacity Aplikace Azure Data Explorer můžete aktualizovat prostřednictvím [portálu Azure](https://portal.azure.com)Portal , PowerShellu, rozhraní PŘÍKAZOVÉHO PŘÍKAZOVÉHO PŘÍKAZU nebo prostřednictvím rozhraní API.
> * Informace o tom, jak spravovat rezervaci rezervované kapacity aplikace Azure Data Explorer, najdete v [tématu správa rezervované kapacity aplikace Azure Data Explorer](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Správa rezervací Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Vysvětlení slev za rezervace Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
