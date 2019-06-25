---
title: Zobrazovat a stahovat, ceny za Azure vaší organizace | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit a stáhnout, ceny nebo odhadněte náklady díky cenám vaší organizace.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127629"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Zobrazovat a stahovat, ceny za Azure vaší organizace

Zákazníci Azure se smlouvou Enterprise (EA) nebo [smlouvy zákazníka se společností Microsoft](#check-your-access-to-a-microsoft-customer-agreement) může zobrazovat a stahovat, jejich ceny na webu Azure Portal. Pokud máte smlouvu Microsoft zákazníka, naleznete v tématu ke stažení ceny pro smlouvy Microsoft zákazníka a zobrazení.

## <a name="view-and-download-ea-pricing"></a>Zobrazovat a stahovat ceny EA

Pouze určité správních rolí v závislosti na zásadách nastavených pro vaši organizaci správcem organizace, poskytují přístup k vaší organizaci informace o cenách EA. Další informace najdete v tématu [správních rolí pochopit smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

1. Jako může podnikový správce přihlásit a [webu Azure portal](https://portal.azure.com/).
1. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. V části fakturační účet, vyberte **a poplatky za využití**.

   ![Snímek obrazovky zobrazující využití a poplatků za fakturaci](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Vyberte ![snímek obrazovky zobrazující Azure portal hledání](./media/billing-ea-pricing/download-icon.png) **Stáhnout** za měsíc.
1. V části **ceníku**vyberte **stáhnout csv**.

   ![Snímek obrazovky zobrazující ceníku sdíleného svazku clusteru tlačítko pro stažení](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Zobrazovat a stahovat ceny pro smlouvy Microsoft zákazníka

Musí být fakturační profil vlastníkem, přispěvatelem, Čtenář nebo správce faktur zobrazovat a stahovat ceny. Další informace o rolích fakturace pro smlouvy Microsoft zákazníka najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Stáhněte si ceníky pro aktuální fakturační období

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Hledat na **Cost Management a fakturace**.
1. Vyberte fakturační profil. V závislosti na přístup potřebujete nejprve vybrat fakturační účet.
1. V **přehled** okně Najít odkazy ke stažení pod poplatky za měsíc k datu.
1. Vyberte **Azure ceníku**.
![Snímek obrazovky zobrazující stahování z přehledu](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Stáhněte si ceníky se fakturuje za

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Hledat na **Cost Management a fakturace**.
1. Vyberte fakturační profil. V závislosti na přístup potřebujete nejprve vybrat fakturační účet.
1. Vyberte **Faktury**.
1. V mřížce faktury nalezen řádek odpovídající ceníku, který chcete stáhnout fakturu.
1. Klikněte na symbol tří teček (`...`) na konci řádku.
![Snímek obrazovky zobrazující se třemi tečkami vybrané](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Pokud chcete zobrazit ceny pro služby ve vybrané faktury, vyberte **faktury ceníku**.
1. Pokud chcete zobrazit ceny pro všechny služby Azure v daném fakturačním období, vyberte **Azure ceníku**.

![Snímek obrazovky zobrazující kontextovou nabídku s ceníky](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Odhad nákladů s cenovou kalkulačku Azure

Můžete také použít ceny vaší organizace k odhadu nákladů s cenovou kalkulačku Azure.

1. Přejděte [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator).
1. Vpravo nahoře vyberte **přihlášení**.
1. V části **programy a nabídky** > **licenční Program**vyberte **Enterprise Agreement (EA)** .
1. V části **programy a nabídky** > **vybrané smlouvy**vyberte **nic Nevybráno**.

    ![Snímek obrazovky zobrazující ceníku sdíleného svazku clusteru tlačítko pro stažení](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Zvolte organizaci.
1. Vyberte **Použít**.
1. Vyhledat a přidat produkty k odhadu.
1. Odhadované uvedené ceny jsou založené na ceny pro organizace, které jste vybrali.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Zkontrolujte svůj přístup na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Další postup

Pokud jste zákazník EA, naleznete v tématu:

- [Správa přístupu k vaší smlouvy EA fakturačních údajů pro Azure](billing-manage-access.md)
- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat využití Microsoft Azure a poplatky za](billing-download-azure-daily-usage.md)
- [Vysvětlení vašeho vyúčtování služeb pro zákazníky se smlouvou Enterprise](billing-understand-your-bill-ea.md)

Pokud máte smlouvu Microsoft zákazníka, naleznete v tématu:

- [Vysvětlení podmínek vašeho ceníku zákaznické smlouvy Microsoft](billing-mca-understand-pricesheet.md)
- [Zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md)
- [Zobrazovat a stahovat využití Microsoft Azure a poplatky za](billing-download-azure-daily-usage.md)
- [Zobrazovat a stahovat daňové doklady pro váš fakturační profil](billing-mca-download-tax-document.md)
- [Principy poplatků za fakturační profil faktury](billing-mca-understand-your-bill.md)
