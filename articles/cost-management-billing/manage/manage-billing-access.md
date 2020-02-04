---
title: Správa přístupu k fakturaci Azure | Microsoft Docs
description: Zjistěte, jak poskytnout přístup k informacím o fakturaci Azure i dalším členům týmu.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 9f78675878bd7a36e27ec1bdce0f3e38540d6039
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "75991435"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Správa přístupu k fakturačním údajům v Azure

K fakturačním údajům na vašemu účtu na webu Azure Portal můžete poskytnout přístup i jiným osobám. Typy fakturačních rolí a pokyny k poskytnutí přístupu k fakturačním údajům se liší v závislosti na typu fakturačního účtu. Pokud chcete zjistit, jaký typ fakturačního účtu máte, přejděte do části [Ověření typu fakturačního účtu](#check-the-type-of-your-billing-account).

Tento článek se týká zákazníků s účty v rámci programu Microsoft Online Service. Pokud jste zákazníkem Azure se smlouvou Enterprise (EA) a jste zároveň podnikovým správcem, můžete udělit oprávnění správcům oddělení a vlastníkům účtů na webu Enterprise Portal. Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md). Pokud máte smlouvu se zákazníkem Microsoftu, přečtěte si téma [Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Správci účtů pro účty programu Microsoft Online Service

Správce účtu je jediným vlastníkem fakturačního účtu programu Microsoft Online Service. Tato role se přiřadí k osobě, která provedla registraci do Azure. Správci účtů mají oprávnění provádět různé úkony spojené s fakturací, jako je vytváření předplatných, zobrazení faktur nebo změna fakturace pro určité předplatné.

## <a name="give-others-access-to-view-billing-information"></a>Udělení přístupu k zobrazení fakturačních údajů jiným osobám

Správce účtu může udělit přístup k fakturačním údajům Azure jiným osobám tak, že jim v předplatném na příslušném účtu přiřadí některou z následujících rolí.

- Správce služeb
- Spolusprávce
- Vlastník
- Přispěvatel
- Čtenář
- Čtenář fakturace

Tyto role mají přístup k fakturačním údajům na webu [Azure Portal](https://portal.azure.com/). Lidé, kteří mají přiřazené tyto role, můžou také využívat [rozhraní API pro fakturaci](usage-rate-card-overview.md), která umožňují programový přístup k fakturám a informacím o využití.

Informace o přiřazování rolí najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

** Pokud jste zákazníkem se smlouvou Enterprise, vlastník účtu může přiřadit výše uvedenou roli ostatním uživatelům ze svého týmu. Pokud si ale tito uživatelé chtějí zobrazovat fakturační údaje, musí podnikový správce na webu Enterprise Portal povolit nastavení AO view charges (Vlastník účtu může zobrazit náklady).


### <a name="opt-in"></a> Povolení stahování faktur uživateli

Jakmile správce účtu přiřadí jiným uživatelům příslušné role, musí zapnout přístup ke stahování faktur na webu Azure Portal. Faktury vytvořené dříve než v prosinci 2016 jsou dostupné jenom správci účtu.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce účtu.

1. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. V levém podokně vyberte **Předplatná**. V závislosti na způsobu přístupu možná budete muset vybrat rozsah fakturace a potom **Předplatná**.

    ![Snímek obrazovky znázorňující výběr předplatných](./media/manage-billing-access/billing-select-subscriptions.png)

1. Vyberte **Faktury** a potom **Přístup k fakturám**.

    ![Snímek obrazovky znázorňující, jak delegovat přístup k fakturám](./media/manage-billing-access/aa-optin01.png)

1. Vyberte **Zapnuto** a uložte změny.

    ![Snímek obrazovky znázorňující, jak zapnout nebo vypnout delegování přístupu k fakturám](./media/manage-billing-access/aa-optinallow01.png)

Správce účtu může také nakonfigurovat odesílání faktur e-mailem. Další informace najdete v tématu věnovaném [dostávání faktur e-mailem](download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Poskytnutí přístupu k fakturaci jen pro čtení

Roli Čtenář fakturace můžete přiřadit uživateli, který potřebuje přístup k informacím o fakturaci pro dané předplatné jen pro čtení, ale nepotřebuje mít možnost spravovat nebo vytvářet služby Azure. Tato role je vhodná pro uživatele v organizaci, kteří zodpovídají za finance a správu nákladů na předplatná Azure.

Funkce role Čtenář fakturace je ve verzi Preview a ještě nepodporuje jiné než globální cloudy.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce účtu.

1. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. V levém podokně vyberte **Předplatná**. V závislosti na způsobu přístupu možná budete muset vybrat rozsah fakturace a potom **Předplatná**.

    ![Snímek obrazovky znázorňující výběr předplatných](./media/manage-billing-access/billing-select-subscriptions.png)

1. Vyberte **Řízení přístupu (IAM)** .
1. V horní části stránky vyberte **Přidat**.

    ![Snímek obrazovky znázorňující kliknutí na tlačítko Přidat přiřazení role](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte **Čtenář fakturace**.
1. Do textového pole **Vyberte** zadejte jméno nebo e-mail uživatele, kterého chcete přidat.
1. Vyberte uživatele.
1. Vyberte **Uložit**.
    ![Snímek obrazovky znázorňující kliknutí na tlačítko Přidat přiřazení role](./media/manage-billing-access/billing-save-role-assignment.png)

1. Po chvíli se uživateli přiřadí k roli Čtenář fakturace pro toto předplatné.

** Pokud jste zákazníkem se smlouvou EA, může roli Čtenář fakturace přiřazovat ke členům týmu vlastník účtu nebo správce oddělení. Pokud má mít ale čtenář fakturace možnost zobrazovat fakturační údaje pro dané oddělení nebo účet, podnikový správce musí na webu Enterprise Portal povolit zásady **AO view charges** (Vlastník účtu může zobrazit náklady) nebo **DA view charges** (Správce oddělení může zobrazit náklady).

## <a name="check-the-type-of-your-billing-account"></a>Ověřte typu fakturačního účtu
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Další kroky

- Uživatelé v jiných rolích, například Vlastník nebo Přispěvatel, mají přístup nejen k fakturačním údajům, ale také ke službám Azure. Informace o správě těchto rolí najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).
- Další informace o rolích najdete v tématu [Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
