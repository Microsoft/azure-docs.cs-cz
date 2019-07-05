---
title: Správa přístupu k fakturaci Azure | Dokumentace Microsoftu
description: Zjistěte, jak poskytnout přístup k Azure fakturačními informacemi pro členy vašeho týmu.
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
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491072"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Správa přístupu k fakturačních údajů pro Azure

Můžete zadat jiné přístup k informacím o fakturaci pro svůj účet na webu Azure Portal. Typ fakturace rolí a pokyny pro poskytnutí přístupu k fakturační informace se liší podle typu fakturačnímu účtu. Chcete-li určit typ fakturační účet, najdete v článku [zkontrolujte typ fakturačnímu účtu](#check-the-type-of-your-billing-account).

Se článek vztahuje na zákazníky s účty Online služeb Microsoftu programu. Pokud jste zákazník Azure se smlouvou Enterprise (EA) a jsou správce rozlehlé sítě, můžete udělit oprávnění k oddělení správci a vlastníci účtu na portálu Enterprise. Další informace najdete v tématu [správních rolí pochopit smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md). Pokud jste zákazníkem Microsoft smlouvy zákazníka, najdete v článku, [pochopit smlouvy zákazníků společnosti Microsoft pro správu role v Azure](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Správci účtu pro program účty Online služeb společnosti Microsoft

Účet správce je vlastníkem pouze pro fakturační účet programu služeb Microsoft Online Service. Se role přiřadí osobě, která zaregistrovali službu Azure. Správci účtu oprávnění k provádění různých úloh fakturace jako vytvářet předplatná, Zobrazit faktury nebo změnit fakturace za předplatné.

## <a name="give-others-access-to-view-billing-information"></a>Poskytnout ostatním přístup k zobrazení fakturačních údajů

Účet správce ostatním uživatelům můžete udělit přístup k Azure fakturační údaje přiřazením jednu z následujících rolí na předplatném v jejich účtu.

- Správce služeb
- Spolusprávce
- Vlastník
- Přispěvatel
- Čtenář
- Čtenář fakturace

Tyto role mají přístup k fakturační údaje v [webu Azure portal](https://portal.azure.com/). Osoby, které jsou přiřazeny tyto role můžete také použít [API pro fakturaci](billing-usage-rate-card-overview.md) programově získání faktury a podrobnosti o použití.

Přiřazení rolí, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

** Pokud jste zákazník EA, můžete přiřadit vlastníka účtu výše uvedené role jiným uživatelům svého týmu. Ale pro tyto uživatele bude možné zobrazit informace o fakturaci, musí povolit správce rozlehlé sítě AO zobrazit náklady na portálu Enterprise.


### <a name="opt-in"></a> Povolit uživatelům stahovat faktury

Jakmile účet správce přiřadil příslušné role jiným uživatelům, musíte zapnout přístup ke stažení faktury na webu Azure Portal. Faktury starší než prosince 2016 jsou k dispozici pouze pro účet správce.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/), jako správce účtu

1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Vyberte **předplatná** v levém podokně. V závislosti na přístup, budete muset vybrat fakturační obor a pak vyberte **předplatná**.
 
    ![Snímek obrazovky, který ukazuje vyberete předplatná](./media/billing-manage-access/billing-select-subscriptions.png)

1. Vyberte **faktury** a potom **přístup k faktuře**.

    ![Snímek obrazovky ukazuje, jak delegovat přístup k faktury](./media/billing-manage-access/AA-optin.png)

1. Vyberte **na** a uložit.

    ![Snímek obrazovky ukazuje zapnutí nebo vypnutí pro delegování přístupu k faktuře](./media/billing-manage-access/AA-optinAllow.png)

Správce účtu může také nakonfigurovat odesílání faktur e-mailem. Další informace najdete v tématu věnovaném [dostávání faktur e-mailem](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Poskytnout přístup jen pro čtení k fakturaci

Role Čtenář fakturace přiřadíte jinému vyžadující oprávnění jen pro čtení na fakturační informace o předplatném, ale ne schopnost spravovat nebo vytvoření služby Azure. Tato role je vhodný pro uživatele v organizaci, kteří jsou zodpovědní za správu finanční a poplatků za odběr služeb Azure.

Čtenář fakturace funkce je ve verzi preview a zatím nepodporuje neglobální cloudy.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/), jako správce účtu

1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Vyberte **předplatná** v levém podokně. V závislosti na přístup, budete muset vybrat fakturační obor a pak vyberte **předplatná**.
 
    ![Snímek obrazovky, který ukazuje vyberete předplatná](./media/billing-manage-access/billing-select-subscriptions.png)

1. Vyberte **řízení přístupu (IAM)** .
1. Vyberte **přidat** z horní části stránky.

    ![Snímek obrazovky s kliknutím na Přidat přiřazení role](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. V **Role** rozevíracího seznamu, zvolte **Čtenář fakturace**.
1. V **vyberte** textového pole zadejte název nebo e-mailu pro uživatele, které chcete přidat.
1. Vyberte uživatele.
1. Vyberte **Uložit**.
    ![Snímek obrazovky s kliknutím na Přidat přiřazení role](./media/billing-manage-access/billing-save-role-assignment.png)

1. Po chvíli se má uživatel přiřazenou roli Čtenář fakturace pro předplatné.

** Pokud jste zákazník EA, vlastník účtu nebo správce oddělení můžete přiřadit role Čtenář fakturace členům týmu. Pro tento Čtenář fakturace k zobrazení fakturačních údajů pro oddělení nebo účet, musíte povolit správce rozlehlé sítě, ale **AO zobrazit náklady** nebo **DA zobrazit náklady** zásady na portálu Enterprise.

## <a name="check-the-type-of-your-billing-account"></a>Kontrola typu fakturační účet
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Další postup

- Uživatelé v jiných rolích, jako je například roli vlastníka nebo přispěvatele, můžou používat jenom fakturační údaje, ale také služby Azure. Ke správě těchto rolí, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).
- Další informace o rolích najdete v tématu [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
