---
title: Zobrazení a stažení informací o využití a poplatcích za Azure
description: Vysvětluje, jak stáhnout nebo zobrazit informace o denním využití a poplatcích za Azure.
keywords: billing usage, usage charges, usage download, view usage, azure invoice, azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491446"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Zobrazení a stažení informací o využití a poplatcích za Azure

Pokud jste zákazníkem se smlouvou Enterprise nebo máte [smlouvu se zákazníkem Microsoftu](#check-your-access-to-a-microsoft-customer-agreement), můžete si z webu [Azure Portal](https://portal.azure.com/) stáhnout informace o využití a poplatcích za Azure. V případě jiných předplatných si můžete informace o využití stáhnout v [Centru účtů Azure](https://account.azure.com/Subscriptions).

K získání informací o využití Azure mají oprávnění jenom některé role, třeba správce účtu nebo podnikový správce. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte [smlouvu se zákazníkem Microsoftu](#check-your-access-to-a-microsoft-customer-agreement) a chcete zobrazit informace o využití a poplatcích za Azure, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Další informace o fakturačních rolích u smluv se zákazníkem Microsoftu najdete v tématu [Role a úlohy související s fakturačním profilem](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Stažení informací o využití z Centra účtů (CSV)

1. Přihlaste se do [Centra účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu.

2. Vyberte předplatné, pro které chcete fakturu a informace o využití.

3. Vyberte **HISTORIE FAKTURACE**.

    ![Snímek obrazovky s možností Historie fakturace](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Zobrazí se výpisy za posledních šest fakturačních období i za aktuální, ještě nevyfakturované období.

    ![Snímek obrazovky ukazující fakturační období, možnosti stažení faktury a informací o denním využití a celkové poplatky za každé fakturační období](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Pokud chcete zobrazit odhadované poplatky k datu generování odhadu, vyberte **Zobrazit aktuální výpis**. Tyto informace se aktualizují jenom jednou denně, takže nemusí obsahovat celkové využití. Vaše měsíční faktura se může od tohoto odhadu lišit.

    ![Snímek obrazovky ukazující možnost Zobrazit aktuální výpis](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Snímek obrazovky ukazující odhad aktuálních poplatků](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Pokud chcete stáhnout data o denním využití jako soubor CSV, vyberte **Stáhnout informace o využití**. Pokud se zobrazí dvě dostupné verze, stáhněte si verzi 2.

    ![Snímek obrazovky ukazující možnost stažení informací o využití](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

K Centru účtů Azure má přístup pouze správce účtu. Ostatní správci fakturace, například vlastník, můžou získat informace o využití pomocí [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md).

Další informace o denním využití najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md). Pokud potřebujete pomoc se správou nákladů, přečtěte si, [jak zabránit neočekávaným nákladům v rámci fakturace Azure a jak používat správu nákladů](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Stažení informací o využití pro zákazníky se smlouvou Enterprise

Pokud si chcete zobrazit a stáhnout data o využití jako zákazník se smlouvou Enterprise, musíte být podnikový správce, vlastník účtu nebo správce oddělení s povolenou zásadou zobrazení poplatků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.

    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Vyberte **Využití a poplatky**.
1. U měsíce, který chcete zobrazit, vyberte **Stáhnout**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Stažení informací o využití pro smlouvu se zákazníkem Microsoftu

Pokud máte smlouvu se zákazníkem Microsoftu, můžete si stáhnout informace o využití a poplatcích za Azure pro váš fakturační profil. Pokud si chcete stáhnout soubor CSV s informacemi o využití a poplatcích za Azure, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu.

### <a name="download-usage-for-billed-charges"></a>Stažení informací o využití u účtovaných poplatků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte *Cost Management a fakturace*.
3. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
4. Vyberte **Faktury**.
5. V tabulce faktur najděte řádek s fakturou odpovídající informacím o využití, které chcete stáhnout.
6. Klikněte na tři tečky (`...`) na konci řádku.

    ![Snímek obrazovky ukazující tři tečky na konci řádku](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. V místní nabídce pro stažení vyberte **Využití a poplatky Azure**.

     ![Snímek obrazovky ukazující vybranou možnost Využití a poplatky Azure](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Stažení informací o využití u nevyřízených poplatků

Pro aktuální fakturační období si můžete stáhnout také informace o využití od začátku měsíce. Jedná se o poplatky za využití, které se ještě nefakturovaly.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte *Cost Management a fakturace*.
3. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
4. V oblasti **Přehled** najdete odkazy ke stažení pod poplatky od začátku měsíce.
5. Vyberte **Využití a poplatky Azure**.

    ![Snímek obrazovky ukazující možnost stažení v části Přehled](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích za využití najdete tady:

- [Vysvětlení výrazů v podrobných informacích o využití Microsoft Azure](billing-understand-your-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md)
- [Zobrazení a stažení faktury za Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazení a stažení cen Azure pro vaši organizaci](billing-ea-pricing.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení výrazů v podrobných informacích o využití Azure v rámci smlouvy se zákazníkem Microsoftu](billing-mca-understand-your-usage.md)
- [Vysvětlení poplatků na faktuře za smlouvu se zákazníkem Microsoftu](billing-mca-understand-your-bill.md)
- [Zobrazení a stažení faktury za Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazení a stažení daňových dokumentů pro smlouvu se zákazníkem Microsoftu](billing-mca-download-tax-document.md)
- [Zobrazení a stažení cen Azure pro vaši organizaci](billing-ea-pricing.md)
