---
title: Vysvětlení poplatků na faktuře za smlouvu se zákazníkem Microsoftu – Azure
description: Zjistěte, jak číst a chápat poplatky uvedené na faktuře.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e9c74fd18bf80dfb8fa406912795fb53e655bd88
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226157"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Vysvětlení poplatků na faktuře za smlouvu se zákazníkem Microsoftu

Poplatky na faktuře můžete pochopit díky analýze jednotlivých transakcí. Na fakturačním účtu za smlouvu se zákazníkem Microsoftu se každý měsíc pro každý fakturační profil generuje jedna faktura. Tato faktura zahrnuje všechny poplatky za předchozí měsíc. Faktury si můžete zobrazit na webu Azure Portal. Další informace najdete v článku [Stahování faktur pro smlouvu se zákazníkem Microsoftu](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Tento článek se týká fakturačního účtu za smlouvu se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Zobrazení transakcí na faktuře na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://www.azure.com).

2. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Na levé straně stránky Vyberte **Všechny transakce**. V závislosti na druhu přístupu vyberte fakturační účet, fakturační profil nebo oddíl faktury a pak vyberte **Všechny transakce**.

4. Na stránce Všechny transakce se zobrazí následující informace:

    ![Snímek obrazovky se seznamem fakturovaných transakcí](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

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

5. Vyhledáním ID faktury můžete transakce na faktuře filtrovat.

### <a name="view-transactions-by-invoice-sections"></a>Zobrazení transakcí podle oddílů faktury

Oddíly faktury pomáhají s uspořádáním nákladů na faktuře pro určitý fakturační profil. Další informace najdete v článku, který [vysvětluje oddíly faktury](billing-mca-overview.md#invoice-sections). Po vygenerování faktury se na faktuře zobrazí poplatky za všechny oddíly fakturačního profilu.

Následující obrázek uvádí poplatky v oddílu faktury Účetní oddělení na ukázkové faktuře.

![Příklad znázorňující informace v oddílu faktury](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Až najdete poplatky v určitém oddílu faktury, můžete si na webu Azure Portal zobrazit transakce, abyste těmto poplatkům porozuměli.

1. Na webu Azure Portal přejděte na stránku Všechny transakce, která obsahuje transakce na určité faktuře. Další informace najdete v části [Zobrazení transakcí na faktuře na webu Azure Portal](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Příslušné transakce zobrazíte, když nastavíte filtrování podle názvu oddílu faktury.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Kontrola nevyřízených poplatků za účelem odhadnutí další faktury

Na fakturačním účtu souvisejícím s určitou smlouvou se zákazníkem Microsoftu se odhadují poplatky a považují se za nevyřízené, dokud neproběhne jejich fakturace. Na webu Azure Portal můžete nevyřízené poplatky zobrazit, abyste mohli odhadnout výši další faktury. Nevyřízené poplatky představují odhad a nezahrnují daň. Skutečné poplatky na vaší další faktuře se budou od nevyřízených poplatků lišit.

### <a name="view-summary-of-pending-charges"></a>Zobrazení shrnutí nevyřízených poplatků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte položku **Správa nákladů a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Zvolte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

4. V horní části obrazovky vyberte kartu **Shrnutí**.

5. Část s poplatky obsahuje poplatky od začátku aktuálního měsíce a za minulý měsíc.

   ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc a objeví se na další faktuře.

### <a name="view-pending-transactions"></a>Zobrazení nevyřízených transakcí

Nevyřízené poplatky můžete lépe pochopit díky analýze jednotlivých transakcí, které k těmto poplatkům vedly. V tuto chvíli se na stránce Všechny transakce nezobrazují nevyřízené poplatky za použití. Nevyřízené poplatky za použití najdete na stránce předplatných Azure. Další informace najdete v části [Zobrazení nevyřízených poplatků za použití](#view-pending-usage-charges).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte položku **Správa nákladů a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Zvolte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

4. Na levé straně stránky Vyberte **Všechny transakce**.

5. Vyhledejte *nevyřízené*. Pomocí filtru **Časový interval** zobrazte nevyřízené poplatky za aktuální nebo minulý měsíc.

   ![Snímek obrazovky se seznamem nevyřízených transakcí](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Zobrazení nevyřízených poplatků za použití

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte položku *Správa nákladů a fakturace*.

   ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Zvolte fakturační profil. V závislosti na úrovni vašeho přístupu možná bude potřeba vybrat fakturační účet. Na fakturačním účtu vyberte **Fakturační profily** a potom vyberte fakturační profil.

4. V levé části stránky vyberte **Všechna předplatná**.

5. Stránka předplatných Azure zobrazí poplatky za každé předplatné ve fakturačním profilu za aktuální a minulý měsíc. Poplatky od začátku měsíce jsou nevyřízené poplatky za aktuální měsíc a naúčtují se při generování faktury za daný měsíc. Pokud ještě není vygenerovaná faktura za minulý měsíc, zobrazují se jako nevyřízené i poplatky za minulý měsíc.

    ![Snímek obrazovky se seznamem předplatných Azure pro daný fakturační profil](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analýza poplatků za využívání Azure

K analýze poplatků na základě využití slouží soubor CSV s informacemi o využití a nákladech. Můžete si stáhnout soubor týkající se určité faktury nebo nevyřízených poplatků. Další informace najdete v tématu, které vysvětluje, jak [získat fakturu a informace o denním využívání Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Zobrazení podrobného využití podle oddílu faktury

Soubor s informacemi o využití a nákladech na Azure můžete filtrovat a pak přiřazovat poplatky za využití k různým oddílům faktury.

Následující postup vás provede sesouhlasením poplatků za výpočetní prostředky pro oddíl faktury Účetní oddělení:

![Příklad znázorňující informace v oddílu faktury](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Faktura v PDF | Soubor CSV s informacemi o využití a poplatcích za Azure |
 | --- | --- |
 |Účetní oddělení |invoiceSectionName |
 |Poplatky za využití – plán Microsoft Azure |productOrderName |
 |Compute |serviceFamily |

1. Vyfiltrujte sloupec **invoiceSectionName** v souboru CSV podle položky **Účetní oddělení**.
2. Vyfiltrujte sloupec **productOrderName** v souboru CSV podle položky **Plán Microsoft Azure**.
3. Vyfiltrujte sloupec **serviceFamily** v souboru CSV podle položky **Microsoft.Compute**.

![Snímek obrazovky znázorňující soubor s informacemi o využití a poplatcích filtrovaný podle oddílů faktury](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Zobrazení podrobného využití podle předplatného

Soubor CSV s informacemi o využití a nákladech na Azure můžete filtrovat a pak přiřazovat poplatky za využití k různým předplatným. Informace o tom, jak zobrazit všechna předplatná ve fakturačním profilu, najdete v části [Zobrazení nevyřízených poplatků za použití](#view-pending-usage-charges).

Když si zobrazíte poplatky za určité předplatné, pomocí souboru CSV s informacemi o využití a poplatcích za Azure můžete tyto poplatky analyzovat.

Následující obrázek ukazuje seznam předplatných na webu Azure Portal.

![Snímek obrazovky se seznamem předplatných Azure pro daný fakturační profil](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Sloupec **subscriptionName** v souboru CSV s informacemi o využití a poplatcích za Azure vyfiltrujte podle položky **WA_Subscription**, aby se zobrazily podrobné poplatky za využití pro předplatné WA_Subscription.

![Snímek obrazovky znázorňující soubor s informacemi o využití a poplatcích filtrovaný podle předplatného](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Úhrada faktury

Pokyny pro úhradu faktury najdete ve spodní části faktury. [Zjistěte, jak provést platbu](billing-mca-understand-your-invoice.md#how-to-pay).

Pokud jste už fakturu zaplatili, na stránce Faktury na webu Azure Portal se můžete podívat na stav platby.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Ověření přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a podrobném využití najdete tady:

- [Jak získat data o denním využití a fakturu za Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Vysvětlení výrazů na faktuře za smlouvu se zákazníkem Microsoftu](billing-mca-understand-your-invoice.md)
- [Vysvětlení výrazů v souboru CSV s informacemi o využití za smlouvu se zákazníkem Microsoftu](billing-mca-understand-your-usage.md)
