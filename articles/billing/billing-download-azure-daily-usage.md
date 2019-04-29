---
title: Zobrazení a Azure stáhnout využití a poplatků | Dokumentace Microsoftu
description: Popisuje, jak stáhnout nebo zobrazení Azure denního využití a poplatků.
keywords: fakturace využití, poplatky za využití, používání stáhnout, zobrazit využití využití azure faktury, azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918973"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Zobrazovat a stahovat využití Azure a poplatky za

Pokud jste zákazník EA nebo máte [smlouvy zákazníka se společností Microsoft](#check-your-access-to-a-microsoft-customer-agreement), si můžete stáhnout využití Azure a poplatky [webu Azure portal](https://portal.azure.com/). Pro jiné předplatné, přejděte [centra účtů Azure](https://account.azure.com/Subscriptions) stáhnout využití.

Pouze některé role mají oprávnění k získání informací o využití Azure, jako je účet správce nebo správce podnikové sítě. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte [smlouvy zákazníka se společností Microsoft](#check-your-access-to-a-microsoft-customer-agreement), musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo správce zobrazíte využití Azure a poplatky fakturovat. Další informace o rolích fakturace pro smlouvy Microsoft zákazníka najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Stáhnout využití z centra účtů (CSV)

1. Přihlaste se [centra účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu.

2. Vyberte předplatné, pro které chcete informace o faktury a využití.

3. Vyberte **HISTORIE FAKTURACE**.

    ![Snímek obrazovky zobrazující možnost historie fakturace](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Zobrazí se vaše příkazy pro posledních šest fakturačních období a aktuální nefakturované období.

    ![Snímek obrazovky s fakturační období, možnosti pro stažení faktury a denního využití a celkové náklady pro každého fakturačního období](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Pokud chcete zobrazit odhadované poplatky k datu generování odhadu, vyberte **Zobrazit aktuální výpis**. Tyto informace se aktualizují každý den a nemusí obsahovat celkové využití. Měsíční faktuře může od odhadu lišit.

    ![Snímek obrazovky zobrazující možnost zobrazit aktuální výpis](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Snímek obrazovky zobrazující odhad aktuální poplatky](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Pokud chcete stáhnout data o denním využití jako soubor CSV, vyberte **Stáhnout informace o využití**. Pokud se zobrazí dvě dostupné verze, stáhněte si verzi 2.

    ![Snímek obrazovky zobrazující možnost stažení informací o využití](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Pouze správce účtu může přístup k centru účtů Azure. Jiní správci fakturace, jako je například vlastníka, můžete získat pomocí informace o využití [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md).

Další informace o denním využití najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md). Pomoci se správou nákladů, naleznete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Stáhnout využití pro zákazníky se smlouvou EA

Zobrazovat a stahovat data o využití jako zákazník EA, musíte být správce podnikové sítě, vlastníka účtu, nebo správce oddělení, když je zobrazení poplatky za povolenou zásadou.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Vyberte **a poplatky za využití**.
1. Za měsíc, kterou chcete stáhnout, vyberte **Stáhnout**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Stáhnout využití pro smlouvy Microsoft zákazníka

Pokud máte smlouvu Microsoft zákazníka, můžete stáhnout využití Azure a poplatky za fakturační profilu. Musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo fakturovat manager stáhnout využití Azure a poplatky za sdíleného svazku clusteru.

### <a name="download-usage-for-billed-charges"></a>Stáhnout využití fakturuje za

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte **Cost Management a fakturace**.
3. Vyberte fakturační profil. V závislosti na přístup potřebujete nejprve vybrat fakturační účet.
4. Vyberte **Faktury**.
5. V mřížce faktury nalezen řádek odpovídající využití, které chcete stáhnout fakturu.
6. Klikněte na symbol tří teček (`...`) na konci řádku.

    ![Snímek obrazovky zobrazující na tři tečky na konci řádku](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. V místní nabídce stahování vyberte **využití Azure a poplatky za**.

     ![Snímek obrazovky zobrazující využití Azure a poplatky za vybrané](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Stáhnout využití pro čekající poplatky

Můžete také stáhnout využití za měsíc k datu v aktuálním fakturačním období. Tyto poplatky za využívání, které nebyly dosud účtuje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte **Cost Management a fakturace**.
3. Vyberte fakturační profil. V závislosti na přístup potřebujete nejprve vybrat fakturační účet.
4. V **přehled** okně Najít odkazy ke stažení pod poplatky za měsíc k datu.
5. Vyberte **využití Azure a poplatky za**.

    ![Snímek obrazovky zobrazující stahování z přehledu](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Zkontrolujte svůj přístup na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o poplatcích faktury a využití najdete v tématu:

- [Vysvětlení podmínek na Microsoft Azure podrobné využití](billing-understand-your-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md)
- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat, ceny za Azure vaší organizace](billing-ea-pricing.md)

Pokud máte smlouvu Microsoft zákazníka, naleznete v tématu:

- [Vysvětlení podmínek smlouvy zákazníka Microsoft Azure podrobné využití](billing-mca-understand-your-usage.md)
- [Principy poplatků za na vaší faktuře smlouvy zákazníka se společností Microsoft](billing-mca-understand-your-bill.md)
- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat daňové doklady pro smlouvy Microsoft zákazníka](billing-mca-download-tax-document.md)
- [Zobrazovat a stahovat, ceny za Azure vaší organizace](billing-ea-pricing.md)
