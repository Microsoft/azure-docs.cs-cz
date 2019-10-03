---
title: Zobrazení a stažení cen Azure pro vaši organizaci
description: Zjistěte, jak zobrazit a stáhnout informace o cenách nebo odhadnout náklady s použitím cen pro vaši organizaci.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 486a6b46b6c4590b7f49cd8aba449204cd8f4fac
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709706"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Zobrazení a stažení cen Azure pro vaši organizaci

Zákazníci Azure se smlouvou Azure Enterprise (EA) nebo [smlouvou se zákazníkem Microsoftu](#check-your-access-to-a-microsoft-customer-agreement) můžou zobrazit a stáhnout informace o cenách na webu Azure Portal.

## <a name="ea-pricing"></a>Ceny EA

V závislosti na zásadách nastavených pro vaši organizaci podnikovým správcem mají přístup k informacím o cenách EA pro vaši organizaci pouze určité role pro správu. Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

1. Jako podnikový správce se přihlaste k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte *Cost Management a fakturace*.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Ve fakturačním účtu vyberte **Využití a poplatky**.

   ![Snímek obrazovky ukazující možnost Využití a poplatky v části Fakturace](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. U požadovaného měsíce vyberte ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-ea-pricing/download-icon.png) **Stáhnout**.

1. V části **Ceník** vyberte **Stáhnout CSV**.

   ![Snímek obrazovky ukazující tlačítko Stáhnout CSV v části Ceník](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>Ceny pro smlouvu se zákazníkem Microsoftu

Pokud chcete zobrazit a stáhnout informace o cenách, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Další informace o fakturačních rolích u smluv se zákazníkem Microsoftu najdete v tématu [Role a úlohy související s fakturačním profilem](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Stažení ceníků pro aktuální fakturační období

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. V oblasti **Přehled** najdete odkazy ke stažení pod poplatky od začátku měsíce.
1. Vyberte **Ceník Azure**.
![Snímek obrazovky znázorňující stažení v části Přehled](./media/billing-ea-pricing/open-pricing.png)

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

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Další kroky

Pokud jste zákazníkem se smlouvou EA, projděte si tato témata:

- [Správa přístupu k fakturačním údajům smlouvy EA v Azure](billing-manage-access.md)
- [Zobrazení a stažení faktury za Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích za Microsoft Azure](billing-download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře pro zákazníky se smlouvou Enterprise](billing-understand-your-bill-ea.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení výrazů v ceníku pro smlouvu se zákazníkem Microsoftu](billing-mca-understand-pricesheet.md)
- [Zobrazení a stažení faktury za Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích za Microsoft Azure](billing-download-azure-daily-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](billing-mca-download-tax-document.md)
- [Vysvětlení poplatků na faktuře pro váš fakturační profil](billing-mca-understand-your-bill.md)
