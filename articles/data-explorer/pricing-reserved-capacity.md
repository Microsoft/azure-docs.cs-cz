---
title: Platba za službu Azure Průzkumník dat Markup za účelem úspory peněz
description: Naučte se, jak koupit rezervovanou kapacitu Azure Průzkumník dat, abyste ušetřili náklady na Azure Průzkumník dat.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681625"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Platba za služby Azure Průzkumník dat označení jednotek pomocí Azure Průzkumník dat rezervované kapacity

V porovnání s cenami za průběžné platby vám ušetříme peníze pomocí Azure Průzkumník dat za jednotky značek. S využitím služby Azure Průzkumník dat rezervované kapacity provedete závazek Azure Průzkumník dat použít po dobu jednoho nebo tří let, abyste získali značnou slevu za náklady na kód v Azure Průzkumník dat. Pokud si chcete koupit službu Azure Průzkumník dat rezervovanou kapacitu, stačí zadat termín, který se použije pro všechna nasazení Azure Průzkumník dat ve všech oblastech.

Zakoupením rezervované rezervace platíte za náklady na označení po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za službu Azure Průzkumník dat s označením, které odpovídají atributům rezervace, už se nebudou účtovat podle tarifů průběžných plateb. Výhody služby Azure Průzkumník dat, které jsou již spuštěny nebo jsou nově nasazeny, budou automaticky získány. Tato rezervace nepokrývá výpočetní prostředky, sítě ani poplatky za úložiště spojené s clustery. Rezervovanou kapacitu těchto prostředků je potřeba koupit samostatně. Na konci rezervovaného období vyprší platnost fakturačního přínosu a jednotky služby Azure Průzkumník dat Markup se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete na [stránce s cenami za Azure Průzkumník dat](https://azure.microsoft.com/pricing/details/data-explorer/).

V [Azure Portal](https://portal.azure.com)si můžete koupit rezervovanou kapacitu Azure Průzkumník dat. Zakoupení rezervované kapacity Azure Průzkumník dat:

* Musíte být vlastníkem aspoň jednoho předplatného organizace nebo předplatného s průběžnými platbami.
* U předplatných se smlouvou Enterprise musí být na webu **EA Portal** povolená možnost [Přidat rezervované instance](https://ea.azure.com). Případně, pokud je toto nastavení zakázáno, musíte být správce EA v předplatném.
* V případě programu Cloud Solution Provider (CSP) si můžou koupit službu Azure Průzkumník dat rezervovanou kapacitu jenom agenti pro správu nebo prodejní agenty.

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete tady:
* [Vysvětlení využití rezervace Azure pro podnikovou registraci](../billing/billing-understand-reserved-instance-usage-ea.md) 
* [Pochopte využívání rezervací Azure pro předplatné](../billing/billing-understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="determine-the-right-markup-usage-before-purchase"></a>Určíte správné použití značek před nákupem.

Velikost rezervace by měla být založená na celkovém počtu jednotek služby Azure Průzkumník dat značek používaných existujícími a již nasazenými clustery Azure Průzkumník dat. Počet jednotek značek se rovná počtu jader clusteru modulu Azure Průzkumník dat v produkčním prostředí (včetně SKU pro vývoj/testování). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Koupit rezervovanou kapacitu Azure Průzkumník dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby** > **rezervacích** > **koupit nyní**. Vyberte **Přidat**.
1. V podokně **Vybrat typ produktu** vyberte **Azure Průzkumník dat** , abyste si koupili novou rezervaci pro jednotky značek Azure Průzkumník dat. 
1. Vybrat **Nákup**
1. Vyplňte požadovaná pole. 

    ![Stránka nákupu](media/pricing-reserved-capacity/purchase-page.png)

1. Přečtěte si náklady na rezervaci rezervované kapacity služby Azure Průzkumník dat v části **náklady** .
1. Vyberte **Koupit**.
1. Pokud chcete zobrazit stav nákupu, vyberte **Zobrazit tuto rezervaci** .

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Pokud potřebujete zrušit rezervaci rezervované kapacity Azure Průzkumník dat, může existovat 12% poplatek za předčasné ukončení. Náhrady vycházejí z nejnižší ceny nákupní ceny nebo aktuální ceny za rezervaci. Výše vrácených peněz je omezená na 50 000 USD za rok. Výše vrácených peněz, které obdržíte, je zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Chcete-li požádat o zrušení, pokračujte na rezervaci v Azure Portal a výběrem možnosti **refundace** vytvořte žádost o podporu.

Pokud potřebujete změnit rezervaci rezervované kapacity Azure Průzkumník dat na jiný termín, můžete ji vyměnit pro jinou rezervaci, která má stejnou nebo větší hodnotu. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. Termín 1 nebo 3 rok začíná při vytváření nové rezervace. Pokud chcete požádat o výměnu, v Azure Portal klikněte na rezervace a vytvořte žádost o podporu výběrem možnosti **Exchange** .

Další informace o výměně nebo refundaci rezervacích najdete v tématu věnovaném [výměnám a refundacím rezervací](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Správa rezervace rezervované kapacity

Sleva za rezervované jednotky služby Azure Průzkumník dat se automaticky použije na počet jednotek značek, které se shodují s rozsahem rezervace rezervovaných kapacit a atributů Azure Průzkumník dat. 


> [!NOTE]
> * Rozsah rezervované rezervace kapacity Azure Průzkumník dat můžete aktualizovat prostřednictvím [Azure Portal](https://portal.azure.com), PowerShellu, CLI nebo přes rozhraní API.
> * Informace o tom, jak spravovat rezervaci rezervované kapacity Azure Průzkumník dat, najdete v tématu [Správa rezervované kapacity azure Průzkumník dat](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou rezervace Azure?](../billing/billing-save-compute-costs-reservations.md)
* [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)
* [Vysvětlení slev za rezervace Azure](../billing/billing-understand-reservation-charges.md)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
* [Vysvětlení využití rezervací u smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
