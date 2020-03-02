---
title: Nákup rezervací Azure s platbami předem nebo měsíčními platbami
description: Přečtěte si, jak můžete kupovat rezervace Azure s platbami předem nebo měsíčními platbami.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.openlocfilehash: 77d663fa01e24acf63acd68d0b8d7cf4cc741055
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587086"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Nákup rezervací s měsíčními platbami

Doposud vyžadovaly rezervace Azure platbu předem. Teď můžete za rezervace platit měsíčními platbami. Na rozdíl od nákupu předem, kdy zaplatíte celou částku najednou, se při měsíčních platbách rozdělí celkové náklady na rezervaci rovnoměrně na jednotlivé měsíce určeného období. Celkové náklady na rezervaci placenou předem a rezervaci s měsíčními platbami jsou stejné. Pokud tedy zvolíte měsíční platby, neplatíte žádné další poplatky.

Částka měsíční platby se může měnit v závislosti na směnném kurzu vaší místní měny v aktuálním měsíci.

Měsíční platby jsou k dispozici pro:

- Virtuální počítače
- Azure Storage
- Databáze SQL
- SQL Data Warehouse
- Databáze Cosmos
- Poplatek za kolek služby App Service
- Spravovaný disk
- Průzkumník dat Azure
- Azure Database for MariaDB, MySQL a PostgreSQL
- Azure VMware Solution by CloudSimple


Rezervace zakoupíte na webu [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Příklad ukazující nákup rezervací](./media/monthly-payments-reservations/purchase-reservation.png)

Při nákupu rezervací můžete zobrazit platební kalendář. Klikněte na **Zobrazit úplný platební kalendář**.

![Příklad ukazující platební kalendář rezervací](./media/monthly-payments-reservations/prepurchase-schedule.png)

Pokud chcete zobrazit kalendář plateb po nákupu, vyberte rezervaci, klikněte na **ID objednávky rezervace** a pak klikněte na kartu **Platby**.

## <a name="view-payments-made"></a>Zobrazení provedených plateb

Provedené platby můžete zobrazit pomocí rozhraní API, prostřednictvím dat o využití a v analýze nákladů. U měsíčně placených rezervací se hodnota četnosti zobrazuje v datech o využití a v rozhraní API poplatků za rezervaci jako **opakovaná**. U rezervací placených předem je tato hodnota zobrazená jako **jednorázová**.

Analýza nákladů zobrazuje měsíční nákupy ve výchozím zobrazení. Pokud chcete zobrazit všechny nákupy, nastavte filtr **Typ poplatku** na **nákup** a **Četnost** na **opakovaná**. Pokud chcete zobrazit jenom rezervace, nastavte filtr na **Rezervace**.

![Příklad ukazující náklady na nákup rezervací v analýze nákladů](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Přechod na měsíční platby při prodloužení

Když rezervaci prodlužujete, můžete změnit četnost fakturace na měsíční.

## <a name="exchange-and-refunds"></a>Výměna a refundace

Podobně jako jiné rezervace můžete i rezervace zakoupené s měsíční fakturací vracet nebo vyměňovat. V současnosti můžete odeslat žádost o podporu a zahájit tak výměnu nebo refundaci pro rezervaci zakoupenou s měsíční fakturací.

Když vyměňujete měsíčně placenou rezervaci, celkové náklady na nový nákup by měly být větší než zbývající platby, které se pro vracenou rezervaci zruší. Pro výměny neexistují žádná další omezení ani poplatky. Předem placenou rezervaci si můžete vyměnit za nákup nové rezervace fakturované měsíčně. Celková hodnota nové rezervace by ale měla být větší než poměrná hodnota vracené rezervace.

Pokud zrušíte měsíčně placenou rezervaci, může Microsoft za zrušené potvrzené platby požadovat poplatek za zrušení ve výši 12 %. Společnost Microsoft ale momentálně tuto pokutu neúčtuje. Zrušené potvrzené platby se načítají do limitu refundace 50 000 USD. Pokud se pokuta za zrušení naúčtuje, nemá vliv na limit refundace.

Další informace o výměně a refundacích najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích najdete v tématu [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- Další informace o úlohách, které byste měli provést před nákupem rezervace, najdete v části o [určení správné velikosti virtuálního počítače před nákupem](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).
