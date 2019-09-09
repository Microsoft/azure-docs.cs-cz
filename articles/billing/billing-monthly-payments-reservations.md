---
title: Nákup rezervací Azure s využitím předem nebo měsíčních splátek
description: Přečtěte si, jak si můžete koupit rezervace Azure s využitím předem nebo měsíčních plateb.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806945"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Nákup rezervací s měsíčními platbami

Až do této chvíle budou rezervace Azure vyžadovat platbu předem. Nyní můžete platit za rezervace s měsíčními platbami. Na rozdíl od průběžného nákupu, kde platíte celou částku, možnost měsíční platby rozdělí celkové náklady na rezervaci rovnoměrně za každý měsíc období. Celkové náklady na front-front-a měsíční rezervace jsou stejné a při výběru platby měsíčně neplatíte žádné další poplatky.

Měsíční částka platby se může lišit v závislosti na směnném kurzu na trhu v aktuálním měsíci pro vaši místní měnu.

K dispozici jsou měsíční platby pro:

- Virtuální počítače
- SQL Database
- SQL Data Warehouse
- Databáze Cosmos
- App Service poplatky za razítko

Nákup rezervací v [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Příklad zobrazení nákupu rezervací](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Při nákupu rezervací si můžete prohlédnout platební plán. Klikněte na **Zobrazit úplný platební plán**.

![Příklad znázorňující platební plán rezervací](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Chcete-li zobrazit plán plateb po nákupu, vyberte rezervaci, klikněte na **ID objednávky rezervace**a pak klikněte na kartu **platby** .

## <a name="view-payments-made"></a>Zobrazit provedené platby

Můžete zobrazit platby vytvořené pomocí rozhraní API, data o využití a analýzy nákladů. U rezervací placených měsíčně se hodnota četnosti zobrazuje jako **opakovaná** v rozhraní API pro údaje o využití a za rezervované náklady. U rezervací placených předem je hodnota zobrazená jako **jednorázová**.

Analýza nákladů zobrazuje měsíční nákupy ve výchozím zobrazení. Použijte filtr **nákupu** pro **typ poplatků** a **Periodický** **pro zobrazení** všech nákupů. Chcete-li zobrazit pouze rezervace, použijte pro **rezervaci**filtr.

![Příklad znázorňující náklady na nákup rezervací v analýze nákladů](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Přepnout na měsíční platby při obnovení

Po obnovení rezervace můžete změnit četnost fakturace na měsíčně.

## <a name="exchange-and-refunds"></a>Výměna a refundace

Podobně jako u jiných rezervací můžete vracet nebo vyměňovat rezervace zakoupené s měsíčním fakturací. V současné době můžete odeslat žádost o podporu a zahájit výměnu nebo refundaci pro rezervaci zakoupenou s měsíčním vyúčtováním.

Když vyměňujete rezervaci, která je placená za měsíc, celkové náklady na nákup nového nákupu by měly být větší než zbyléé platby, které se pro vrácenou rezervaci zruší. Pro výměny nejsou k dispozici žádná další omezení ani poplatky. Můžete si vyměňovat rezervované platby, abyste si koupili novou rezervaci, která se účtuje měsíčně. Hodnota doby života nové rezervace by však měla být větší než poměrná hodnota vracené rezervace.

Pokud zrušíte rezervaci, která je vyplácena měsíčně, může společnost Microsoft použít poplatek za zrušení na budoucí potvrzené platby, které byly zrušeny. Zbývající potvrzené platby se budou započítávat k limitu refundace $50 000 USD.

Další informace o Exchangi a refundacích najdete v tématu [samoobslužné výměny a refundace pro Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>Nejčastější dotazy

Otázka: Nabízí Azure možnost částečných rezervací předem?<br>
A. Ne. Vzhledem k tomu, že náklady na rezervace placené předem a měsíčně jsou stejné, Microsoft nepodporuje částečné platby předem.

Otázka: Jsou k dispozici měsíční platby pro Microsoft Cloud program poskytovatele řešení (CSP)?<br>
A. Ano, partneři můžou zakoupit rezervace pro své zákazníky CSP v Azure Portal. Možnost nákupu rezervací s měsíčním účtováním není k dispozici v partnerském centru.

Otázka: Jsem US Azure Government zákazníkem, můžu si za nákupy rezervací zaplatit měsíčně?<br>
A. Momentálně ne.

Otázka: Kdy je možné místo vytvoření lístku podpory na Azure Portal vyměnit nebo vrátit zpět?<br>
A. Momentálně ne. Žádosti o výměnu a refundace s využitím měsíčních plateb jsou zpracovávány prostřednictvím podpory Azure.

## <a name="next-steps"></a>Další postup

- Další informace o rezervacích najdete v tématu [co jsou Azure reservations?](billing-save-compute-costs-reservations.md)
- Další informace o úlohách, které byste měli provést před nákupem rezervace, najdete v tématu [určení správné velikosti virtuálních počítačů před nákupem](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) .
