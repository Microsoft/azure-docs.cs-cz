---
title: Předem SQL Data Warehouse poplatky za vyhrazené kapacitě Azure | Dokumentace Microsoftu
description: Zjistěte, jak si můžete Předplatíte SQL Data Warehouse poplatky za vyhrazené kapacitě ještě ušetříte peníze.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371185"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Předem SQL Data Warehouse poplatky za vyhrazené kapacitě

Vám umožní ušetřit s Azure SQL Data Warehouse se rozhodnete cDWU využití po dobu jednoho nebo tří let. K nákupu SQL Data Warehouse vyhrazené kapacity, musíte zvolit Azure oblast a délku smlouvy. Potom přidat do košíku SQL Data Warehouse SKU a zvolit počet jednotek cDWU, které si chcete koupit.

Při nákupu rezervace přejít na SQL Data Warehouse využití, která odpovídá atributy rezervace je už účtovat podle tarifů platit jako budete sazby.

Rezervace nezahrnuje úložiště nebo sítě poplatky spojené s využitím SQL Data Warehouse.

Když vyprší platnost rezervované kapacity, instance SQL Data Warehouse dál běžet, ale se účtují sazbou platit jako můžete přejít. Rezervace není automaticky obnoví.

Informace o cenách najdete v článku [SQL Data Warehouse vyhrazené kapacity nabídky](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Můžete si koupit Azure SQL Data Warehouse vyhrazené kapacitě [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Koupit rezervované kapacity:

- Musíte mít roli vlastníka pro alespoň jeden enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise **přidat Reserved Instances** musí být povolena možnost v [portálu EA](https://ea.azure.com/). Pokud je zakázáno, musíte být správce EA.
- Programu Cloud Solution Provider (CSP) můžou nakupovat jenom správce agentů nebo prodejní agentů SQL Data Warehouse vyhrazené kapacity.

Další informace o jak podnikovým zákazníkům a Zákazníci s průběžnými platbami se vám účtovat nákup rezervace, naleznete v tématu [porozumět používání rezervace Azure u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md) a [pochopit Azure rezervace využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Výběr správné velikosti před nákupem

SQL Data Warehouse, které vyhrazená velikost by měla vycházet z celkové výpočetních jednotek datového skladu (cDWU), které skutečně využijete. Nákupy se provádí v přírůstcích po 100 cDWU.

Předpokládejme například, že vaše celková spotřeba služby SQL Data Warehouse je DW3000c. Chcete zakoupit vyhrazené kapacitu pro všechno. Proto byste si zakoupit 30 jednotek cDWU vyhrazené kapacity.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Nákup SQL Data Warehouse rezervované kapacity

1. Přihlaste se do [webu Azure portal](https://portal.azure.com/).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte předplatné. Pomocí seznamu předplatného vyberte předplatné, který umožňuje platit za rezervované kapacity. Způsob platby předplatného se účtuje pořizovací náklady pro záložní kapacitu. Tento typ předplatného musí být smlouvu enterprise agreement (nabízejí čísla: MS-AZR-0017P nebo MS-AZR - 0148 P) nebo s průběžnými platbami (nabízejí čísla: MS-AZR-0003P nebo MS-AZR-0023P).
  - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
  - V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.
4. Vyberte obor. Pomocí seznamu rozsahů vyberte oboru předplatného.
  - S **jeden** možnost sleva za rezervaci se použije pro SQL Data Warehouse nasazené ve vybraném předplatném.
  - S **Shared** možnost sleva za rezervaci se použije pro instance běžící v rámci kontextu vaší fakturace žádné předplatné.
    - Pro podnikové zákazníky je kontext fakturace registraci smlouvy Enterprise.
    - Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.
5. Vyberte oblast a vyberte oblast Azure, která je předmětem záložní kapacitu.
6. Vyberte množství. Zadejte množství 100 jednotek datového skladu (cDWU), které si chcete koupit.    
  Například množství 30 by vám poskytlo 3000 cDWU záložní kapacitu každou hodinu.
7. Kontrola vyhrazené náklady na rezervované kapacity v SQL Data Warehouse **náklady** oddílu.
8. Vyberte **Koupit**.
9. Vyberte **zobrazit tuto rezervaci** zobrazíte stav vašeho nákupu.

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Potřebujete-li zrušit služby SQL Data Warehouse rezervované kapacity, může být 12 % poplatek za předčasné ukončení. Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší. Náhrady jsou omezené na 50,000.00 za rok. Náhrady, který jste dostali se zbývající poměrný zůstatek minus poplatek za předčasné ukončení 12 %. Pokud chcete požádat o zrušení, přejděte na rezervaci v Azure portal a vyberte **náhrada** vytvořit žádost o podporu.

Pokud potřebujete změnit kapacitu pro vyhrazené SQL Data Warehouse do jiné oblasti nebo termín, můžete ji exchange pro jiné rezervace, který je roven nebo větší hodnotu. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. Jeden nebo tři roky začne, když vytvoříte novou rezervaci. Požádat o výměnu, otevřete rezervace na webu Azure Portal a vyberte **Exchange** vytvořit žádost o podporu.

Další informace o tom, jak exchange nebo náhrady rezervace, naleznete v tématu [výměny rezervaci a vrácení peněz](billing-azure-reservations-self-service-exchange-and-refund.md).

Sleva za rezervaci se automaticky využije na počet instancí SQL Data Warehouse, které odpovídají oboru SQL Data Warehouse vyhrazené kapacity a oblasti. Můžete aktualizovat obor SQL Data Warehouse vyhrazené kapacity pomocí [webu Azure portal](https://portal.azure.com/), prostředí PowerShell, rozhraní příkazového řádku nebo prostřednictvím rozhraní API.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/).

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak použít slevy na rezervaci do služby Azure SQL Data Warehouse najdete v tématu [jak slevy na rezervaci použít do služby Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Další informace o rezervacích Azure, najdete v následujících článcích:
  - [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
  - [Vysvětlení, že slevách na využití platformy Azure rezervace](billing-understand-reservation-charges.md)
  - [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
