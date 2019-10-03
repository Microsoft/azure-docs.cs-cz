---
title: Vytvoření dalšího předplatného Azure pro fakturační účet
description: Naučte se, jak na webu Azure Portal přidat nové předplatné Azure.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e1fafafe5f67d1775ca80a1f7c2aff4dec9e0bc4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709575"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Vytvoření dalšího předplatného Azure pro smlouvu se zákazníkem Microsoftu

Vytvořením dalších předplatných pro váš fakturační účet můžete nastavit oddělená prostředí pro účely vývoje a testování, zabezpečení nebo izolace dat z důvodu dodržování předpisů.

Tento článek se týká fakturačních účtů smluv se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access). Pokud chcete vytvořit předplatná pro jiné typy fakturačních účtů, přečtěte si téma [Vytvoření dalšího předplatného na webu Azure Portal](billing-create-subscription.md).

Pokud chcete vytvořit předplatné, musíte být **vlastníkem oddílu faktur**, **přispěvatelem oddílu faktur** nebo **tvůrcem předplatného Azure**. Další informace najdete v tématu [Role a úlohy fakturace předplatného](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Pokud chcete udělit oprávnění k vytváření předplatných Azure pro váš fakturační účet ostatním uživatelům, přečtěte si téma [Udělení oprávnění k vytváření předplatných Azure ostatním uživatelům](#give-others-permission).

## <a name="create-a-subscription"></a>Vytvoření odběru

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Předplatná**.

   ![Snímek obrazovky, který zobrazuje hledání předplatných na portálu](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Vyberte **Přidat**.

4. Pokud máte přístup k více fakturačním účtům, vyberte fakturační účet pro vaši smlouvu se zákazníkem Microsoftu.

   ![Snímek obrazovky se stránkou Vytvořit předplatné](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Vyberte fakturační profil. Na vybraný fakturační profil se budou účtovat poplatky za vaše předplatné. Pokud máte přístup pouze k jednomu fakturačnímu profilu, možnost výběru bude neaktivní.

6. Vyberte oddíl faktury. Na tento oddíl faktury pro fakturační profil se budou účtovat poplatky za vaše předplatné. Pokud máte přístup pouze k jednomu oddílu faktury, možnost výběru bude neaktivní.

7. Vyberte plán pro předplatné. Pokud chcete toto předplatné používat pro účely vývoje a testování úloh, vyberte **Plán Azure pro vývoj a testování**, jinak použijte **Plán Microsoft Azure**. Pokud máte přístup pouze k jednomu plánu, možnost výběru bude neaktivní.

8. Zadejte název odběru. Tento název vám pomůže snadno identifikovat předplatné na webu Azure Portal.

9. Vyberte **Vytvořit**.

## <a name="give-others-permission"></a>Udělení oprávnění ostatním uživatelům

Uživatele, kterým chcete udělit oprávnění k vytváření předplatných Azure, přidejte do oddílu faktury jako tvůrce předplatného Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky, který zobrazuje hledání předplatných na portálu](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Přejděte do oddílu faktury. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet nebo fakturační profil. Ve fakturačním účtu nebo profilu vyberte **Oddíly faktury** a pak v seznamu vyberte oddíl faktury. Na tento oddíl faktury se budou účtovat všechna předplatná vytvořená uživateli.
   
   ![Snímek obrazovky znázorňující výběr možnosti Oddíly faktury](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. V levé horní části vyberte **Správa přístupu (IAM)** .

5. V horní části stránky vyberte **Přidat**.

6. Jako roli vyberte **Tvůrce předplatného Azure**.

7. Zadejte e-mailovou adresu uživatele, kterému chcete udělit přístup.

8. Vyberte **Uložit**.

## <a name="check-access"></a>Kontrola přístupu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Udělení oprávnění k vytváření prostředků Azure ostatním uživatelům s využitím předdefinovaných rolí](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Vytvoření virtuálního počítače s Windows](../virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s Linuxem](../virtual-machines/linux/quick-create-portal.md)
- [Vytvoření skupin pro správu pro organizaci a správu prostředků](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
