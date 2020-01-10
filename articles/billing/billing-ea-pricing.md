---
title: Zobrazení a stažení cen Azure pro vaši organizaci
description: Zjistěte, jak zobrazit a stáhnout informace o cenách nebo odhadnout náklady s použitím cen pro vaši organizaci.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 60e366466e399bbde78b026e0b401cd6bd1edbee
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644388"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Zobrazení a stažení cen Azure pro vaši organizaci

Zákazníci Azure se smlouvou Azure Enterprise (EA), smlouvou se zákazníkem Microsoftu (MCA) nebo smlouvou s partnerem Microsoftu (MPA) můžou zobrazit a stáhnout informace o cenách na webu Azure Portal. [Zjistěte, jak zkontrolovat typ vašeho fakturačního účtu](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Stažení informací o cenách pro smlouvu Enterprise

V závislosti na zásadách nastavených pro vaši organizaci podnikovým správcem mají přístup k informacím o cenách EA pro vaši organizaci pouze určité role pro správu. Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

1. Jako podnikový správce se přihlaste k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte *Cost Management a fakturace*.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Ve fakturačním účtu vyberte **Využití a poplatky**.

   ![Snímek obrazovky ukazující možnost Využití a poplatky v části Fakturace](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. U požadovaného měsíce vyberte ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-ea-pricing/download-icon.png) **Stáhnout**.

1. V části **Ceník** vyberte **Stáhnout CSV**.

   ![Snímek obrazovky ukazující tlačítko Stáhnout CSV v části Ceník](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Stažení informací o cenách pro účet se smlouvou MCA nebo MPA

Pokud máte smlouvu MCA a chcete zobrazit a stáhnout informace o cenách, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Pokud máte smlouvu MPA a chcete zobrazit a stáhnout informace o cenách, musíte mít roli Globální správce nebo Agent správy v partnerské organizaci.

### <a name="download-price-sheets-for-billed-charges"></a>Stažení ceníků účtovaných poplatků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. Vyberte **Faktury**.
1. V tabulce faktur vyhledejte řádek faktury odpovídající ceníku, který chcete stáhnout.
1. Klikněte na tři tečky (`...`) na konci řádku.
![Snímek obrazovky ukazující vybrané tři tečky](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Pokud chcete zobrazit ceny služeb na vybrané faktuře, vyberte **Ceník faktury**.
1. Pokud chcete zobrazit ceny všech služeb Azure pro dané fakturační období, vyberte **Ceník Azure**.

![Snímek obrazovky s místní nabídkou ceníků](./media/billing-ea-pricing/contextmenu-pricesheet.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Stažení ceníků pro aktuální fakturační období

Pokud máte smlouvu MCA, můžete si stáhnout informace o cenách pro aktuální fakturační období.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. V oblasti **Přehled** najdete odkazy ke stažení pod poplatky od začátku měsíce.
1. Vyberte **Ceník Azure**.
![Snímek obrazovky znázorňující stažení v části Přehled](./media/billing-ea-pricing/open-pricing.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Odhad nákladů pomocí cenové kalkulačky Azure

Informace o cenách pro vaši organizaci můžete využít také k odhadu nákladů pomocí cenové kalkulačky Azure.

1. Přejděte na [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator).
1. V pravém horním rohu vyberte **Přihlásit se**.
1. V části **Programy a nabídky** > **Licenční program** vyberte **Smlouva Enterprise (EA)** .
1. V části **Programy a nabídky** > **Vybraná smlouva** vyberte **Nic není vybrané**.

    ![Snímek obrazovky ukazující tlačítko Stáhnout CSV v části Ceník](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Zvolte organizaci.
1. Vyberte **Použít**.
1. Vyhledejte produkty a přidejte je k odhadu.
1. Uvedené odhadované ceny vycházejí z cen pro vybranou organizaci.

## <a name="check-your-billing-account-type"></a>Kontrola typu fakturačního účtu
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Další kroky

Pokud jste zákazníkem se smlouvou EA, projděte si tato témata:

- [Správa přístupu k fakturačním údajům smlouvy EA v Azure](billing-manage-access.md)
- [Zobrazení a stažení faktury za Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích za Microsoft Azure](billing-download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře pro zákazníky se smlouvou Enterprise](billing-understand-your-bill-ea.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení výrazů v ceníku pro smlouvu se zákazníkem Microsoftu](billing-mca-understand-pricesheet.md)
- [Zobrazení a stažení faktury za Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích Microsoft Azure](billing-download-azure-daily-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](billing-mca-download-tax-document.md)
- [Vysvětlení poplatků na faktuře pro váš fakturační profil](billing-mca-understand-your-bill.md)
