---
title: Kontrola vyúčtování Smlouvy se zákazníkem Microsoftu – Azure
description: Naučte se kontrolovat náklady a využití prostředků a ověřovat poplatky na faktuře za Smlouvu se zákazníkem Microsoftu.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 619dec1b2246b7e74b058e570df4021888196b0c
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447535"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Kurz: Kontrola faktury za Smlouvu se zákazníkem Microsoftu

Poplatky na faktuře můžete zkontrolovat analýzou jednotlivých transakcí. Na fakturačním účtu za smlouvu se zákazníkem Microsoftu se každý měsíc pro každý fakturační profil generuje jedna faktura. Tato faktura zahrnuje všechny poplatky za předchozí měsíc. Na faktury se můžete podívat na webu Azure Portal a porovnat poplatky se souborem podrobností o využití.

Tento kurz se týká zákazníků Azure, kteří mají Smlouvu se zákazníkem Microsoftu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola fakturovaných transakcí na webu Azure Portal
> * Kontrola nevyřízených poplatků za účelem odhadnutí další faktury
> * Analýza poplatků za využívání Azure

## <a name="prerequisites"></a>Požadavky

Musíte mít fakturační účet pro Smlouvu se zákazníkem Microsoftu.

Musíte mít přístup ke Smlouvě se zákazníkem Microsoftu. Pokud chcete zobrazit informace o fakturaci a využití, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Další informace o fakturačních rolích u smluv se zákazníkem Microsoftu najdete v tématu [Role a úlohy související s fakturačním profilem](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Od vytvoření vašeho předplatného Azure musí uplynout více než 30 dnů. Azure se fakturuje na konci fakturačního období.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu

Zkontrolujte typ smlouvy, abyste zjistili, jestli máte přístup k fakturačnímu účtu pro smlouvu se zákazníkem Microsoftu.

Na webu Azure Portal do vyhledávacího pole zadejte *správa nákladů a fakturace* a potom vyberte **Správa nákladů a fakturace**.

![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Pokud máte přístup jenom k jednomu rozsahu fakturace, vyberte na levé straně **Vlastnosti**. Pokud je typ fakturačního účtu **Smlouva se zákazníkem Microsoftu**, máte přístup k fakturačnímu účtu smlouvy se zákazníkem Microsoftu.

![Snímek obrazovky zobrazující Smlouvu se zákazníkem Microsoftu na stránce vlastností](./media/review-customer-agreement-bill/billing-mca-property.png)

Pokud máte přístup k více rozsahům fakturace, ověřte typ ve sloupci fakturačního účtu. Pokud je typ fakturačního účtu pro jakýkoli rozsah **Smlouva se zákazníkem Microsoftu**, máte přístup k fakturačnímu účtu smlouvy se zákazníkem Microsoftu.

![Snímek obrazovky zobrazující Smlouvu se zákazníkem Microsoftu na stránce seznamu fakturačních účtů](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Kontrola fakturovaných transakcí na webu Azure Portal

Vlevo na stránce na webu Azure Portal vyberte **Všechny transakce**. V závislosti na druhu přístupu vyberte fakturační účet, fakturační profil nebo oddíl faktury a pak vyberte **Všechny transakce**.

Na stránce Všechny transakce se zobrazí následující informace:

![Snímek obrazovky se seznamem fakturovaných transakcí](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Sloupec  |Definice  |
|---------|---------|
|Datum     | Datum transakce  |
|ID faktury     | Identifikátor faktury, na které je transakce naúčtovaná. Pokud odešlete žádost o podporu, sdělte podpoře Azure toto ID, abyste žádost o podporu urychlili |
|Typ transakce     |  Typ transakce, třeba nákup, storno nebo poplatky za použití  |
|Produktová řada     | Kategorie produktů, jako jsou výpočetní prostředky pro virtuální počítače nebo databáze pro Azure SQL Database|
|SKU produktu     | Jedinečný kód identifikující instanci produktu |
|Částka     |  Částka transakce      |
|Oddíl faktury     | Transakce se na faktuře daného fakturačního profilu zobrazí v této části faktury |
|Fakturační profil     | Transakce se zobrazí na faktuře tohoto fakturačního profilu |

Vyhledáním ID faktury můžete transakce na faktuře filtrovat.

### <a name="view-transactions-by-invoice-sections"></a>Zobrazení transakcí podle oddílů faktury

Oddíly faktury pomáhají s uspořádáním nákladů na faktuře pro určitý fakturační profil. Další informace. Po vygenerování faktury se na faktuře zobrazí poplatky za všechny oddíly fakturačního profilu.

Následující obrázek uvádí poplatky v oddílu faktury Účetní oddělení na ukázkové faktuře.

![Příklad znázorňující informace v oddílu faktury](./media/review-customer-agreement-bill/invoicesection-details.png)

Až najdete poplatky v určitém oddílu faktury, můžete si na webu Azure Portal zobrazit transakce, abyste těmto poplatkům porozuměli.

Na webu Azure Portal přejděte na stránku Všechny transakce, která obsahuje transakce na určité faktuře.

Příslušné transakce zobrazíte, když nastavíte filtrování podle názvu oddílu faktury.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Kontrola nevyřízených poplatků za účelem odhadnutí další faktury

Na fakturačním účtu souvisejícím s určitou Smlouvou se zákazníkem Microsoftu se poplatky odhadují a považují se za nevyřízené, dokud neproběhne jejich fakturace. Na webu Azure Portal můžete nevyřízené poplatky zobrazit, abyste mohli odhadnout výši další faktury. Nevyřízené poplatky představují odhad a nezahrnují daň. Skutečné poplatky na vaší další faktuře se budou od nevyřízených poplatků lišit.

### <a name="view-summary-of-pending-charges"></a>Zobrazení shrnutí nevyřízených poplatků

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

V horní části obrazovky vyberte kartu **Shrnutí**.

Část s poplatky obsahuje poplatky od začátku aktuálního měsíce a za minulý měsíc.

![Snímek obrazovky se souhrnem fakturačního profilu](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc a objeví se na další faktuře.

### <a name="view-pending-transactions"></a>Zobrazení nevyřízených transakcí

Nevyřízené poplatky můžete lépe pochopit díky analýze jednotlivých transakcí, které k těmto poplatkům vedly. V tuto chvíli se na stránce Všechny transakce nezobrazují nevyřízené poplatky za použití. Nevyřízené poplatky za použití najdete na stránce předplatných Azure.

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

Na webu Azure Portal do vyhledávacího pole zadejte *správa nákladů a fakturace* a potom vyberte **Správa nákladů a fakturace**.

Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

Na levé straně stránky Vyberte **Všechny transakce**.

Vyhledejte *nevyřízené*. Pomocí filtru **Časový interval** zobrazte nevyřízené poplatky za aktuální nebo minulý měsíc.

![Snímek obrazovky se seznamem nevyřízených transakcí](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Zobrazení nevyřízených poplatků za použití

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

Na webu Azure Portal do vyhledávacího pole zadejte *správa nákladů a fakturace* a potom vyberte **Správa nákladů a fakturace**.

Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

V levé části stránky vyberte **Všechna předplatná**.

Stránka předplatných Azure zobrazí poplatky za každé předplatné ve fakturačním profilu za aktuální a minulý měsíc. Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc.

![Snímek obrazovky s předplatnými a poplatky od začátku měsíce a poplatky za poslední měsíc](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analýza poplatků za využívání Azure

K analýze poplatků na základě využití slouží soubor CSV s informacemi o využití a nákladech. Můžete si stáhnout soubor týkající se určité faktury nebo nevyřízených poplatků.

### <a name="download-your-invoice-and-usage-details"></a>Stažení faktury a podrobností o využití

V závislosti na úrovni vašeho přístupu možná budete muset v části Správa nákladů a fakturace vybrat fakturační účet nebo fakturační profil. V nabídce vlevo v části **Fakturace** vyberte **Faktury**. V tabulce faktur najděte řádek s fakturou, kterou chcete stáhnout. Klikněte na symbol stahování nebo tři tečky (...) na konci řádku. V poli **Stáhnout** si stáhněte soubor s podrobnostmi o využití a fakturu.

### <a name="view-detailed-usage-by-invoice-section"></a>Zobrazení podrobného využití podle oddílu faktury

Soubor s informacemi o využití a nákladech na Azure můžete filtrovat a pak přiřazovat poplatky za využití k různým oddílům faktury.

Následující informace vás provedou odsouhlasením poplatků za výpočetní prostředky pro oddíl faktury Účetní oddělení:

![Příklad znázorňující informace v oddílu faktury](./media/review-customer-agreement-bill/invoicesection-details.png)

| Faktura v PDF | Soubor CSV s informacemi o využití a poplatcích za Azure |
| --- | --- |
|Účetní oddělení |invoiceSectionName |
|Poplatky za využití – plán Microsoft Azure |productOrderName |
|Compute |serviceFamily |

Vyfiltrujte sloupec **invoiceSectionName** v souboru CSV podle položky **Účetní oddělení**. Potom vyfiltrujte sloupec **productOrderName** v souboru CSV podle položky **Plán Microsoft Azure**. Dále vyfiltrujte sloupec **serviceFamily** v souboru CSV podle položky **Microsoft.Compute**.

![Snímek obrazovky znázorňující soubor s informacemi o využití a poplatcích filtrovaný podle oddílů faktury](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Zobrazení podrobného využití podle předplatného

Soubor CSV s informacemi o využití a nákladech na Azure můžete filtrovat a pak přiřazovat poplatky za využití k různým předplatným. Když si zobrazíte poplatky za určité předplatné, pomocí souboru CSV s informacemi o využití a poplatcích za Azure můžete tyto poplatky analyzovat.

Následující obrázek ukazuje seznam předplatných na webu Azure Portal.

![Snímek obrazovky se seznamem předplatných na webu Azure Portal a jedním vybraným předplatným](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Sloupec **subscriptionName** v souboru CSV s informacemi o využití a poplatcích za Azure vyfiltrujte podle položky **WA_Subscription**, aby se zobrazily podrobné poplatky za využití pro předplatné WA_Subscription.

![Snímek obrazovky znázorňující soubor s informacemi o využití a poplatcích filtrovaný podle předplatného](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Úhrada faktury

Pokyny pro úhradu faktury najdete ve spodní části faktury. [Zjistěte, jak provést platbu](mca-understand-your-invoice.md#how-to-pay).

Pokud jste už fakturu zaplatili, na stránce Faktury na webu Azure Portal se můžete podívat na stav platby.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Kontrola fakturovaných transakcí na webu Azure Portal
> * Kontrola nevyřízených poplatků za účelem odhadnutí další faktury
> * Analýza poplatků za využívání Azure

Dokončete rychlý start, abyste mohli začít používat analýzu nákladů.

> [!div class="nextstepaction"]
> [Začínáme s analýzou nákladů](../costs/quick-acm-cost-analysis.md)
