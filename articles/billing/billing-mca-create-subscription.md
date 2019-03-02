---
title: Vytvořte další předplatné Azure pro váš fakturační účet | Dokumentace Microsoftu
description: Zjistěte, jak přidat nové předplatné Azure na webu Azure Portal.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 89c2f380fb4007256273b10b1fcc0fc99627ccb4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248864"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Vytvořte další předplatné Azure pro smlouvy Microsoft zákazníka

Vytvořte další předplatná pro váš fakturační účet vytvořit samostatná prostředí pro vývoj a testování, zabezpečení a izolace dat kvůli dodržování předpisů.

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement). Pokud chcete vytvářet předplatná pro jiné fakturační účty, najdete v článku [vytvořte další předplatné na webu Azure Portal](billing-create-subscription.md).

Jak vytvoříte odběr, musí být **vlastník části faktury**, **Přispěvatel části faktury**, nebo **předplatného Azure Tvůrce**. Další informace najdete v tématu [předplatné role a úlohy](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). K poskytování dalších oprávnění vytvářet předplatná Azure pro váš fakturační účet, najdete v článku [dalším osobám udělit oprávnění vytvářet předplatná Azure](#give-others-permission-to-create-azure-subscriptions).

## <a name="create-a-subscription-in-the-azure-portal"></a>Vytvoření odběru na portálu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **předplatná**.

   ![Snímek obrazovky zobrazující vyhledávání v portálu pro předplatná](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Vyberte **přidat**

4. Pokud máte přístup k několika účtům fakturace, vyberte fakturační účet pro smlouvy Microsoft zákazníka.

   ![Vytvořit snímek obrazovky zobrazující stránku odběru služby](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Vyberte fakturační profil. Poplatky za předplatné se zobrazí na faktuře fakturační profil a bude se budou hradit pomocí jeho způsoby platby. Pokud máte přístup k fakturačním jenom jeden profil, výběr šedě.

6. Vyberte oddíl faktury. Poplatky za vaše předplatné se zobrazí v této části faktury fakturační profil. Pokud máte přístup k pouze jeden oddíl faktury, výběr šedě.

7. Vyberte plán pro předplatné. Vyberte **plán Microsoft Azure DevTest**, pokud máte v plánu použít toto předplatné pro vývoj nebo testování zatížení else používají **plánu Microsoft Azure**. Pokud máte přístup k jenom jeden plán, výběr šedě.

8. Zadejte název odběru. Název vám umožní snadno identifikovat předplatné na webu Azure Portal.

9. Vyberte **Vytvořit**.

## <a name="give-others-permission-to-create-azure-subscriptions"></a>Dalším osobám udělit oprávnění vytvářet předplatná Azure

Přidáte uživatele jako tvůrce předplatného Azure na oddíl faktury jim dát oprávnění vytvářet předplatná Azure.

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující vyhledávání v portálu pro předplatná](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Přejděte do části faktury. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil. Fakturace účtu nebo profil, vyberte **fakturovat oddíly** a potom část faktury.

4. Vyberte **řízení přístupu (IAM)** ze strany levého horního rohu.

5. V horní části stránky vyberte **přidat**.

6. Vyberte **předplatného Azure creator** pro roli.

   ![Snímek obrazovky zobrazující přidělení role Tvůrce předplatného Azure na uživatele](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. Zadejte e-mailovou adresu uživatele, kterým chcete udělit přístup.

8. Vyberte **Uložit**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- [Ostatním uživatelům oprávnění k vytváření prostředků Azure pomocí předdefinované role](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Vytvoření virtuálního počítače s windows](../virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s linuxem](../virtual-machines/linux/quick-create-portal.md)
- [Vytvoření skupin pro správu pro organizaci poskytující prostředky a správu](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
