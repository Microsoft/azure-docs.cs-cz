---
title: Vytvořit oddíly na vaší faktuře uspořádat svoje náklady – Azure
description: Zjistěte, jak uspořádat náklady s využitím části faktury.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490786"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Vytvořit oddíly na vaší faktuře uspořádat svoje náklady

Vytvořit oddíly na vaší faktuře uspořádat svoje náklady podle oddělení, vývojové prostředí, nebo na základě potřeb vaší organizace. Potom ostatním uživatelům oprávnění k vytvoření předplatného Azure, které se fakturují na části. Do části pak účtovat žádné poplatky za využívání a nákup předplatných. Můžete zobrazit celkové poplatky pro oddíl na vaší faktuře na webu Azure Portal, nebo Seznamte se s nimi v analýze nákladů na Azure. Další informace najdete v tématu [zobrazit transakce podle části faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Vytvořte oddíl faktury na webu Azure Portal

K vytvoření oddílu faktury, musíte být **fakturační vlastníka profilu** nebo **fakturační Přispěvatel profilu**. Další informace najdete v tématu [spravovat části faktury pro fakturační profil](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Vyberte **fakturovat oddíly** v levém podokně. V závislosti na přístup, budete muset vybrat fakturační profil nebo fakturační účet a potom vyberte **fakturovat oddíly**.

   ![Snímek obrazovky zobrazující seznam oddílů faktury](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. V horní části stránky vyberte **Přidat**.

5. Zadejte název pro část faktury a vyberte fakturační profil. V části uvidíte na faktuře fakturační profil odráží využití každé předplatné a nákup, které jste přiřadili do části. 

   ![Snímek obrazovky zobrazující stránku vytvoření části faktury](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Vyberte **Vytvořit**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- [Vytvořte další předplatné Azure pro smlouvy Microsoft zákazníka](billing-mca-create-subscription.md)
- [Ostatním uživatelům oprávnění k vytvoření předplatného Azure](billing-mca-create-subscription.md#give-others-permission)
- [Získat vlastnictví fakturace předplatného Azure od uživatelů v jiných účtech fakturace](billing-mca-request-billing-ownership.md)
