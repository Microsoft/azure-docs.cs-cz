---
title: Vytvořte další předplatné Azure pro váš fakturační účet
description: Zjistěte, jak přidat nové předplatné Azure na webu Azure Portal.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490951"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Vytvořte další předplatné Azure pro smlouvy Microsoft zákazníka

Vytvořte další předplatná pro váš fakturační účet vytvořit samostatná prostředí pro vývoj a testování, zabezpečení a izolace dat kvůli dodržování předpisů.

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access). Pokud chcete vytvářet předplatná pro ostatní typy fakturační účty, najdete v článku [vytvořte další předplatné na webu Azure Portal](billing-create-subscription.md).

Jak vytvoříte odběr, musí být **vlastník části faktury**, **Přispěvatel části faktury**, nebo **předplatného Azure Tvůrce**. Další informace najdete v tématu [fakturace předplatného role a úlohy](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). K poskytování dalších oprávnění vytvářet předplatná Azure pro váš fakturační účet, najdete v článku [dalším osobám udělit oprávnění vytvářet předplatná Azure](#give-others-permission).

## <a name="create-a-subscription"></a>Vytvoření odběru

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **předplatná**.

   ![Snímek obrazovky zobrazující vyhledávání v portálu pro předplatná](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Vyberte **přidat**

4. Pokud máte přístup k několika účtům fakturace, vyberte fakturační účet pro smlouvy Microsoft zákazníka.

   ![Vytvořit snímek obrazovky zobrazující stránku odběru služby](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Vyberte fakturační profil. Poplatky za vaše předplatné se účtuje na vybraný fakturační profil. Pokud máte přístup k fakturačním jenom jeden profil, výběr šedě.

6. Vyberte oddíl faktury. Poplatky za vaše předplatné se účtuje na v této části faktury fakturační profil. Pokud máte přístup k pouze jeden oddíl faktury, výběr šedě.

7. Vyberte plán pro předplatné. Vyberte **plán Microsoft Azure DevTest**, pokud máte v plánu použít toto předplatné pro vývoj nebo testování zatížení else používají **plánu Microsoft Azure**. Pokud máte přístup k jenom jeden plán, výběr šedě.

8. Zadejte název odběru. Název vám umožní snadno identifikovat předplatné na webu Azure Portal.

9. Vyberte **Vytvořit**.

## <a name="give-others-permission"></a>Dalším osobám udělit oprávnění

Přidáte uživatele jako tvůrce předplatného Azure na oddíl faktury jim dát oprávnění vytvářet předplatná Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující vyhledávání v portálu pro předplatná](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Přejděte do oddílu faktury. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil. Fakturace účtu nebo profil, vyberte **fakturovat oddíly** a potom část faktury ze seznamu. Jakékoli předplatné vytvořené uživateli se bude fakturovat do této části faktury.
   
   ![Snímek obrazovky s výběrem části faktury](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Vyberte **řízení přístupu (IAM)** ze strany levého horního rohu.

5. V horní části stránky vyberte **Přidat**.

6. Vyberte **předplatného Azure creator** pro roli.

7. Zadejte e-mailovou adresu uživatele, kterým chcete udělit přístup.

8. Vyberte **Uložit**.

## <a name="check-access"></a>Zkontrolovat přístup
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Ostatním uživatelům oprávnění k vytváření prostředků Azure pomocí předdefinované role](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Vytvoření virtuálního počítače s windows](../virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s linuxem](../virtual-machines/linux/quick-create-portal.md)
- [Vytvoření skupin pro správu pro organizaci poskytující prostředky a správu](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
