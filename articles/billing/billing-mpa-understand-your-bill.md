---
title: Vysvětlení poplatků na faktuře za smlouvu s partnerem Microsoftu – Azure
description: Zjistěte, jak číst a chápat poplatky uvedené na faktuře.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223698"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Vysvětlení poplatků na faktuře za smlouvu s partnerem Microsoftu

 Na fakturačním účtu pro smlouvu s partnerem Microsoftu se každý měsíc pro každý fakturační profil generuje jedna faktura. Tato faktura zahrnuje všechny poplatky zákazníka za předchozí měsíc. Poplatky na faktuře můžete pochopit díky analýze jednotlivých transakcí na webu Azure Portal. Na webu Azure Portal si můžete také prohlédnout své faktury. Další informace najdete v tématu věnovaném [stahování faktur z webu Azure Portal](billing-download-azure-invoice.md).

Tento článek se týká fakturačního účtu pro smlouvu s partnerem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě s partnerem Microsoftu](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Zobrazení transakcí na faktuře na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://www.azure.com).

2. Vyhledejte *Cost Management a fakturace*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Na levé straně stránky Vyberte **Všechny transakce**. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet, fakturační profil nebo zákazníka. Pak vyberte **Všechny transakce**.

4. Na stránce Všechny transakce se zobrazí následující informace:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Sloupec  |Definice  |
    |---------|---------|
    |Datum     | Datum transakce  |
    |ID faktury     | Identifikátor faktury, na které je transakce naúčtovaná. Pokud odešlete žádost o podporu, sdělte podpoře Azure toto ID, abyste žádost o podporu urychlili |
    |Typ transakce     |  Typ transakce, třeba nákup, storno nebo poplatky za použití  |
    |Produktová řada     | Kategorie produktů, jako jsou výpočetní prostředky pro virtuální počítače nebo databáze pro Azure SQL Database|
    |SKU produktu     | Jedinečný kód identifikující instanci produktu |
    |Částka     |  Částka transakce      |
    |Fakturační profil     | Transakce se zobrazí na faktuře tohoto fakturačního profilu |

5. Vyhledáním ID faktury můžete transakce na faktuře filtrovat.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Kontrola nevyřízených poplatků za účelem odhadnutí další faktury

Poplatky se odhadují a považují se za nevyřízené, dokud neproběhne jejich fakturace. Pokud chcete odhadnout další fakturu, můžete si prohlédnout nevyřízené poplatky za váš fakturační účet pro smlouvu s partnerem Microsoftu na webu Azure Portal. Nevyřízené poplatky představují odhad a nezahrnují daň. Skutečné poplatky na vaší další faktuře se budou od nevyřízených poplatků lišit.

### <a name="view-pending-transactions"></a>Zobrazení nevyřízených transakcí

Nevyřízené poplatky můžete lépe pochopit díky analýze jednotlivých transakcí, které k těmto poplatkům vedly. V tuto chvíli se na stránce Všechny transakce nezobrazují nevyřízené poplatky za použití. Nevyřízené poplatky za použití najdete na stránce předplatných Azure. Další informace najdete v části [Zobrazení nevyřízených poplatků za použití](#view-pending-usage-charges).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte *Cost Management a fakturace*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

4. Na levé straně stránky Vyberte **Všechny transakce**.

5. Vyhledejte *nevyřízené*. Pomocí filtru **Časový interval** zobrazte nevyřízené poplatky za aktuální nebo minulý měsíc.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Zobrazení nevyřízených poplatků podle zákazníka

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte *Cost Management a fakturace*.

3. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

4. Na levé straně stránky vyberte **Zákazníci**.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. Na stránce Zákazníci se zobrazí poplatky za každého zákazníka přidruženého k fakturačnímu profilu za aktuální a minulý měsíc. Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc.

### <a name="view-pending-usage-charges"></a>Zobrazení nevyřízených poplatků za použití

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte *Cost Management a fakturace*.

3. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

4. Na levé straně stránky vyberte **Předplatná Azure**.

5. Stránka předplatných Azure zobrazí poplatky za každé předplatné ve fakturačním profilu za aktuální a minulý měsíc. Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analýza poplatků za využívání Azure

K analýze poplatků na základě využití slouží soubor CSV s informacemi o využití a nákladech. [Soubor CSV s informacemi o využití a poplatcích za Azure si můžete stáhnout z webu Azure Portal](billing-download-azure-daily-usage.md).

Soubor s informacemi o využití a poplatcích za Azure můžete filtrovat a pak přiřazovat poplatky za využití k jednotlivým produktům uvedeným v souboru PDF faktury. Pokud chcete zobrazit podrobné poplatky za využití konkrétního produktu, v souboru CSV s informacemi o využití a poplatcích za Azure vyfiltrujte ve sloupci **product** (Produkt) pouze název daného produktu.

V souboru CSV s informacemi o využití a poplatcích za Azure můžete vyfiltrovat také sloupec **customerName** (Název zákazníka) tak, aby se zobrazily denní poplatky za využití pro jednotlivé zákazníky. Pokud chcete zobrazit denní poplatky za využití podle předplatného Azure, filtrujte podle sloupce **subscriptionName** (Název předplatného).


## <a name="pay-your-bill"></a>Úhrada faktury

Pokyny pro úhradu faktury najdete ve spodní části faktury. Do 60 dnů od vystavení faktury můžete zaplatit převodem nebo šekem.

Pokud jste už fakturu zaplatili, na stránce Faktury na webu Azure Portal se můžete podívat na stav platby.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrola přístupu ke smlouvě s partnerem Microsoftu
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a podrobném využití najdete tady:
