---
title: Správa přístupu k fakturaci Azure | Dokumentace Microsoftu
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: cwatson
ms.openlocfilehash: 00b29ddf29ae334154d41eedd0bf9e66556e09ad
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720115"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Správa přístupu k fakturačních údajů pro Azure

Pro většinu předplatných, můžete poskytnout fakturační informace přístup členům vašeho týmu z **předplatná** na webu Azure Portal. Pokud jste zákazník Azure se smlouvou Enterprise (EA zákazníka) a jsou správce rozlehlé sítě, můžete udělit oprávnění k oddělení správci a vlastníci účtu na portálu Enterprise.

## <a name="give-access-to-billing"></a>Poskytnout přístup k fakturaci

Všechny s výjimkou zákazníky se smlouvou EA může udělit přístup k Azure fakturační údaje přiřazením jednu z následujících rolí uživatele členům vašeho týmu:

- Správce účtu
- Správce služeb
- Spolusprávce
- Vlastník
- Přispěvatel
- Čtenář
- Čtenář fakturace

Přiřazení rolí, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

Tyto role mají přístup k fakturační údaje v [webu Azure portal](https://portal.azure.com/). Můžete také použít osoby, která jsou přiřazena těmto rolím [API pro fakturaci](billing-usage-rate-card-overview.md) programově získání faktury a podrobnosti o použití. Další informace najdete v tématu [role v Azure RBAC](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Povolit uživatelům stahovat faktury

Poté co přiřaďte odpovídající role, které chcete členům vašeho týmu účtu správce musíte zapnout vyhodnocení ke stažení faktury na webu Azure Portal. Faktury starší než prosince 2016 jsou k dispozici pouze pro účet správce.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Jako správce účtu, vyberte své předplatné z [okně předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure portal.

1. Vyberte **faktury** a potom **přístup na fakturách**.

    ![Snímek obrazovky ukazuje, jak delegovat přístup k faktury](./media/billing-manage-access/AA-optin.png)

1. Vyberte **na** a uložit.

    ![Snímek obrazovky ukazuje zapnutí nebo vypnutí pro delegování přístupu k faktuře](./media/billing-manage-access/AA-optinAllow.png)

Správce účtu může také nakonfigurovat odesílání faktur e-mailem. Další informace najdete v tématu věnovaném [dostávání faktur e-mailem](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Poskytnout přístup jen pro čtení k fakturaci

Role Čtenář fakturace přiřadíte jinému vyžadující oprávnění jen pro čtení na fakturační informace o předplatném, ale ne schopnost spravovat nebo vytvoření služby Azure. Tato role je vhodný pro uživatele v organizaci, kteří jsou zodpovědní za správu finanční a poplatků za odběr služeb Azure.

Pokud jste zákazník EA, vlastník účtu nebo správce oddělení můžete přiřadit role Čtenář fakturace členům týmu. Pro tento Čtenář fakturace k zobrazení fakturačních údajů pro oddělení nebo účet, musíte povolit správce rozlehlé sítě, ale **AO zobrazit náklady** nebo **DA zobrazit náklady** zásady na portálu Enterprise.

Čtenář fakturace funkce je ve verzi preview a zatím nepodporuje neglobální cloudy.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte požadované předplatné v [okně Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.

1. Vyberte **řízení přístupu (IAM)**.
1. Vyberte **přiřazení rolí** zobrazíte všechna přiřazení rolí pro toto předplatné.
1. Vyberte **přidat přiřazení role**.
1. V **Role** rozevíracího seznamu, zvolte **Čtenář fakturace**.
1. V **vyberte** textového pole zadejte název nebo e-mailu pro uživatele, které chcete přidat.
1. Vyberte uživatele.
1. Vyberte **Uložit**.
1. Po chvíli se má uživatel přiřazenou roli Čtenář fakturace v oboru předplatného.
1. Čtenář fakturace obdrží e-mail s odkazem pro přihlášení.

    ![Snímek obrazovky, který ukazuje, co všechno uvidí Čtenář fakturace na webu Azure portal](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Povolit přístup fakturační oddělení správce nebo vlastníka účtu

Správce rozlehlé sítě umožňuje správcům oddělení a vlastníci účtu k zobrazení informací o využití a náklady spojené s oddělení a účty, které spravují.

1. Jako správce podnikové sítě, přihlaste se k [portál EA](https://ea.azure.com/).
1. Vyberte **spravovat**.
1. V části **registrace**, změnit **DA zobrazit náklady** k **povoleno** pro oddělení správcům zobrazit využití a náklady.
1. Změna **AO zobrazit náklady** k **povoleno** vlastníka účtu, který chcete zobrazit využití a nákladů.


Další informace najdete v tématu [správních rolí pochopit smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Pouze správce účtu může přístup k centru účtů

Správce účtu se jejím právoplatným vlastníkem předplatného. Ve výchozím nastavení, osoby, která registrovanou službu nebo zakoupili předplatné Azure je správcem účtu, pokud [bylo převedeno vlastnictví předplatného](billing-subscription-transfer.md) někomu jinému. Správce účtu může vytvářet předplatná, zrušit předplatná, změnit adresu fakturace předplatného a spravovat zásady přístupu pro předplatné z [centra pro účty](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Další postup

- Uživatelé v jiných rolích, jako je například roli vlastníka nebo přispěvatele, můžou používat jenom fakturační údaje, ale také služby Azure. Ke správě těchto rolí, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).
- Další informace o rolích najdete v tématu [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
