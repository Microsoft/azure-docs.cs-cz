---
title: Vytváření oddílů na faktuře pro organizaci nákladů – Azure
description: Naučte se organizovat náklady pomocí oddílů faktury.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ff8b2da353d623cd9f05c8d0b0317587d7093ce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75389260"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Uspořádání nákladů na faktuře pomocí oddílů

Vytvořte na faktuře oddíly, pomocí kterých uspořádáte náklady podle oddělení, vývojového prostředí nebo podle jiných požadavků organizace. Dalším uživatelům pak můžete udělit oprávnění k vytváření předplatných Azure, která se účtují na daný oddíl. V něm se pak budou zobrazovat všechny poplatky za využití a nákupy v rámci daných předplatných. Můžete zobrazit celkové poplatky za oddíl na faktuře, na webu Azure Portal nebo je přezkoumat v analýze nákladů Azure. Další informace najdete v tématu [Zobrazení transakcí podle oddílů faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Vytvoření oddílu faktury na webu Azure Portal

Pokud chcete vytvořit oddíl faktury, musíte být **vlastníkem fakturačního profilu** nebo **přispěvatelem fakturačního profilu**. Další informace najdete v tématu [Správa oddílů faktur pro fakturační profil](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. V levém podokně vyberte **Oddíly faktury**. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační profil nebo fakturační účet a pak vybrat **Oddíly faktury**.

   ![Snímek obrazovky, který zobrazuje seznam oddílů faktury](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. V horní části stránky vyberte **Přidat**.

5. Zadejte název oddílu faktury a vyberte fakturační profil. Teto oddíl se zobrazí na faktuře pro fakturační profil a bude odrážet využití jednotlivých předplatných a nákupy, které jste k oddílu přiřadili.

   ![Snímek obrazovky, který zobrazuje stránku pro vytvoření oddílu faktury](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Vyberte **Vytvořit**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vytvoření dalšího předplatného Azure pro smlouvu se zákazníkem Microsoftu](billing-mca-create-subscription.md)
- [Správa fakturačních rolí na webu Azure Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Získání role vlastníka fakturace pro předplatná Azure od uživatelů v jiných fakturačních účtech](billing-mca-request-billing-ownership.md)
