---
title: Nákup rezervací Azure s platbami předem nebo měsíčními platbami
description: Přečtěte si, jak můžete kupovat rezervace Azure s platbami předem nebo měsíčními platbami.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235748"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Nákup rezervací s měsíčními platbami

Za rezervace můžete platit měsíčními platbami. Na rozdíl od nákupu předem, kdy zaplatíte celou částku najednou, se při měsíčních platbách rozdělí celkové náklady na rezervaci rovnoměrně na jednotlivé měsíce určeného období. Celkové náklady na rezervaci placenou předem a rezervaci s měsíčními platbami jsou stejné. Pokud tedy zvolíte měsíční platby, neplatíte žádné další poplatky.

Pokud se rezervace nakupuje prostřednictvím smlouvy se zákazníkem Microsoftu (MCA), částka měsíční platby se může měnit v závislosti na směnném kurzu vaší místní měny v aktuálním měsíci.

Měsíční platby nejsou k dispozici pro: Databricks, rezervace pro SUSE Linux, plány Red Hatu a Azure Red Hat OpenShift ve verzi Compute.

Rezervace zakoupíte na webu [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

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

Podobně jako jiné rezervace můžete i rezervace zakoupené s měsíční fakturací vracet nebo vyměňovat. 

Když vyměňujete měsíčně placenou rezervaci, celkové náklady na nový nákup by měly být větší než zbývající platby, které se pro vracenou rezervaci zruší. Pro výměny neexistují žádná další omezení ani poplatky. Předem placenou rezervaci si můžete vyměnit za nákup nové rezervace fakturované měsíčně. Celková hodnota nové rezervace by ale měla být větší než poměrná hodnota vracené rezervace.

Pokud zrušíte rezervaci, která se platí měsíčně, zrušené budoucí platby se načítají do limitu refundace 50 000 USD.

Další informace o výměně a refundacích najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích najdete v tématu [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- Další informace o úlohách, které byste měli provést před nákupem rezervace, najdete v části o [určení správné velikosti virtuálního počítače před nákupem](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).
