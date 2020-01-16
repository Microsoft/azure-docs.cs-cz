---
title: Přečtěte si fakturu o vaší smlouvě s partnerem Microsoftu – Azure
description: Přečtěte si, jak zkontrolovat vyúčtování a využití prostředků a ověřit poplatky za vaši fakturaci na základě smlouvy o partnerovi Microsoftu.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: e3823a9eb2e0713f7f42e4e02808ef957efc5944
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990837"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Kurz: Kontrola faktury se smlouvou Microsoft Partner Agreement

 Na fakturačním účtu pro smlouvu s partnerem Microsoftu se každý měsíc pro každý fakturační profil generuje jedna faktura. Tato faktura zahrnuje všechny poplatky zákazníka za předchozí měsíc. Poplatky na faktuře můžete pochopit díky analýze jednotlivých transakcí na webu Azure Portal. Faktury můžete zobrazit také v Azure Portal a porovnat poplatky se souborem podrobností využití.

Další informace najdete v tématu věnovaném [stahování faktur z webu Azure Portal](download-azure-invoice.md).

Tento kurz se týká jenom partnerů Azure se smlouvou Microsoft Partner Agreement.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola fakturovaných transakcí v Azure Portal
> * Kontrola nevyřízených poplatků za účelem odhadnutí další faktury
> * Analýza poplatků za využívání Azure

## <a name="prerequisites"></a>Požadavky

Musíte mít přístup k fakturačnímu účtu pro smlouvu Microsoft Partner Agreement.

Musí být delší než 30 dní ode dne, kdy jste se přihlásili k Azure. Azure se fakturuje na konci fakturačního období.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu

Zkontrolujte typ smlouvy, abyste zjistili, jestli máte přístup k fakturačnímu účtu pro smlouvu s partnerem Microsoftu.

Do pole pro Azure Portal zadejte *cost management + fakturace* a pak vyberte **cost management + fakturace**.

![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Pokud máte přístup jenom k jednomu rozsahu fakturace, vyberte na levé straně **Vlastnosti**. Pokud je typ fakturačního účtu **Smlouva s partnerem Microsoftu**, máte přístup k fakturačnímu účtu pro smlouvu s partnerem Microsoftu.

![Snímek obrazovky, na které se na stránce vlastností zobrazuje smlouva o partnerovi Microsoftu](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Pokud máte přístup k více rozsahům fakturace, ověřte typ ve sloupci fakturačního účtu. Pokud je typ fakturačního účtu pro jakýkoli rozsah **Smlouva s partnerem Microsoftu**, máte přístup k fakturačnímu účtu pro smlouvu s partnerem Microsoftu.

![Snímek obrazovky zobrazující smlouvu o partnerovi Microsoftu na stránce se seznamem fakturačních účtů](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Kontrola fakturovaných transakcí v Azure Portal

V Cost Management + fakturace vyberte **všechny transakce** na levé straně stránky. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet, fakturační profil nebo zákazníka. Pak vyberte **Všechny transakce**.

Na stránce Všechny transakce se zobrazí následující informace:

![Snímek obrazovky se seznamem fakturovaných transakcí](./media/review-partner-agreement-bill/all-transactions.png)

|Column  |Definice  |
|---------|---------|
|Datum     | Datum transakce  |
|ID faktury     | Identifikátor faktury, na které je transakce naúčtovaná. Pokud odešlete žádost o podporu, sdělte podpoře Azure toto ID, abyste žádost o podporu urychlili |
|Typ transakce     |  Typ transakce, třeba nákup, storno nebo poplatky za použití  |
|Produktová řada     | Kategorie produktů, jako jsou výpočetní prostředky pro virtuální počítače nebo databáze pro Azure SQL Database|
|SDKU produktu     | Jedinečný kód identifikující instanci produktu |
|Částka     |  Částka transakce      |
|Fakturační profil     | Transakce se zobrazí na faktuře tohoto fakturačního profilu |

Pokud chcete filtrovat transakce faktury, vyhledejte ID faktury.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Kontrola nevyřízených poplatků za účelem odhadnutí další faktury

Poplatky se odhadují a považují se za nevyřízené, dokud neproběhne jejich fakturace. Pokud chcete odhadnout další fakturu, můžete si prohlédnout nevyřízené poplatky za váš fakturační účet pro smlouvu s partnerem Microsoftu na webu Azure Portal. Nevyřízené poplatky představují odhad a nezahrnují daň. Skutečné poplatky na vaší další faktuře se budou od nevyřízených poplatků lišit.

### <a name="view-pending-transactions"></a>Zobrazení nevyřízených transakcí

Nevyřízené poplatky můžete lépe pochopit díky analýze jednotlivých transakcí, které k těmto poplatkům vedly. V tuto chvíli se na stránce Všechny transakce nezobrazují nevyřízené poplatky za použití. Nevyřízené poplatky za použití najdete na stránce předplatných Azure.

V Cost Management + fakturace vyberte profil fakturace. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

Na levé straně stránky Vyberte **Všechny transakce**.

Vyhledejte *nevyřízené*. Pomocí filtru **Časový interval** zobrazte nevyřízené poplatky za aktuální nebo minulý měsíc.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Zobrazení nevyřízených poplatků podle zákazníka

V Cost Management + fakturace vyberte profil fakturace. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

Na levé straně stránky vyberte **Zákazníci**.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

Na stránce Zákazníci se zobrazí poplatky za každého zákazníka přidruženého k fakturačnímu profilu za aktuální a minulý měsíc. Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc.

### <a name="view-pending-usage-charges"></a>Zobrazení nevyřízených poplatků za použití

V Cost Management + fakturace vyberte profil fakturace. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

Na levé straně stránky vyberte **Předplatná Azure**. Stránka předplatných Azure zobrazí poplatky za každé předplatné ve fakturačním profilu za aktuální a minulý měsíc. Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analýza poplatků za využívání Azure

K analýze poplatků na základě využití slouží soubor CSV s informacemi o využití a nákladech. Soubor s informacemi o využití a poplatcích za Azure můžete filtrovat a pak přiřazovat poplatky za využití k jednotlivým produktům uvedeným v souboru PDF faktury. Pokud chcete zobrazit podrobné poplatky za využití konkrétního produktu, v souboru CSV s informacemi o využití a poplatcích za Azure vyfiltrujte ve sloupci **product** (Produkt) pouze název daného produktu.

V souboru CSV s informacemi o využití a poplatcích za Azure můžete vyfiltrovat také sloupec **customerName** (Název zákazníka) tak, aby se zobrazily denní poplatky za využití pro jednotlivé zákazníky. Pokud chcete zobrazit denní poplatky za využití podle předplatného Azure, filtrujte podle sloupce **subscriptionName** (Název předplatného).

## <a name="pay-your-bill"></a>Úhrada faktury

Pokyny pro úhradu faktury najdete ve spodní části faktury. Do 60 dnů od vystavení faktury můžete zaplatit převodem nebo šekem.

Pokud jste už fakturu zaplatili, na stránce Faktury na webu Azure Portal se můžete podívat na stav platby.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Kontrola fakturovaných transakcí v Azure Portal
> * Kontrola nevyřízených poplatků za účelem odhadnutí další faktury
> * Analýza poplatků za využívání Azure

Seznamte se s používáním Azure Cost Management pro partnery.

> [!div class="nextstepaction"]
> [Začínáme s Azure Cost Management pro partnery](../costs/get-started-partners.md)
