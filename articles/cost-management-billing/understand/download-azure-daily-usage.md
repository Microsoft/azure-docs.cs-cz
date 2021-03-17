---
title: Zobrazení a stažení informací o využití a poplatcích za Azure
description: Zjistěte, jak stáhnout nebo zobrazit informace o denním využití Azure a poplatcích, a projděte si další dostupné zdroje informací.
keywords: billing usage, usage charges, usage download, view usage, azure invoice, azure usage
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 141da3dfab9996110952ec266733271582e66ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439204"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Zobrazení a stažení informací o využití a poplatcích za Azure

Na webu Azure Portal si můžete stáhnout denní rozpis využití Azure a souvisejících poplatků. K získání informací o využití Azure mají oprávnění jenom některé role, třeba správce účtu nebo podnikový správce. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](../manage/manage-billing-access.md).

Pokud máte smlouvu se zákazníkem Microsoftu (MCA) a chcete zobrazit informace o využití a poplatcích za Azure, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu.  Pokud máte smlouvu s partnerem Microsoftu (MPA), informace o využití a poplatcích za Azure může zobrazit a stáhnout pouze globální správce nebo agent správy v partnerské organizaci Microsoftu. [Zkontrolujte typ svého fakturačního účtu na webu Azure Portal](#check-your-billing-account-type).

Možnosti stažení informací o využití a poplatcích se liší v závislosti na typu předplatného, které používáte.

## <a name="download-usage-from-the-azure-portal-csv"></a>Stažení informací o využití (ve formátu .csv) z webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.  
    ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet nebo fakturační profil.
1. V nabídce vlevo v části **Fakturace** vyberte **Faktury**.
1. V tabulce faktur najděte řádek s fakturačním obdobím odpovídajícím informacím o využití, které chcete stáhnout.
1. Klikněte na **ikonu stahování** nebo tři tečky (`...`) na pravé straně.  
  ![Snímek obrazovky se stránkou Faktury v části Cost Management + Billing a možností Stáhnout](./media/download-azure-daily-usage/download-usage-others.png)  
1. Napravo se otevře podokno stahování. V části **Podrobnosti o využití** vyberte **Stáhnout**.  

## <a name="download-usage-for-ea-customers"></a>Stažení informací o využití pro zákazníky se smlouvou Enterprise

Pokud si chcete zobrazit a stáhnout data o využití jako zákazník se smlouvou Enterprise, musíte být podnikový správce, vlastník účtu nebo správce oddělení s povolenou zásadou zobrazení poplatků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.  
    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Vyberte **Využití a poplatky**.
1. U měsíce, který chcete zobrazit, vyberte **Stáhnout**.  
    ![Snímek obrazovky se stránkou Faktury v části Cost Management + Billing pro zákazníky se smlouvou Enterprise](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>Stažení informací o využití u nevyřízených poplatků

Pokud máte smlouvu se zákazníkem Microsoftu, můžete si stáhnout informace o využití od začátku měsíce za aktuální fakturační období. Jedná se o poplatky za využití, které se ještě nefakturovaly.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte *Cost Management a fakturace*.
3. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
4. V oblasti **Přehled** najdete odkazy ke stažení pod posledními poplatky.
5. Vyberte **Stáhnout využití a ceny**.  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="Snímek obrazovky znázorňující stažení v části Přehled" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>Kontrola typu fakturačního účtu
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích za využití najdete tady:

- [Vysvětlení výrazů v podrobných informacích o využití Microsoft Azure](understand-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](review-individual-bill.md)
- [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md)
- [Zobrazení a stažení cen Azure pro vaši organizaci](../manage/ea-pricing.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení výrazů v podrobných informacích o využití Azure v rámci smlouvy se zákazníkem Microsoftu](mca-understand-your-usage.md)
- [Vysvětlení poplatků na faktuře za smlouvu se zákazníkem Microsoftu](review-customer-agreement-bill.md)
- [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md)
- [Zobrazení a stažení daňových dokumentů pro smlouvu se zákazníkem Microsoftu](mca-download-tax-document.md)
- [Zobrazení a stažení cen Azure pro vaši organizaci](../manage/ea-pricing.md)
