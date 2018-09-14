---
title: Jak upravit údaje skupiny pomocí služby Azure Active Directory | Dokumentace Microsoftu
description: Jak lze upravit informace o panelu skupiny s využitím Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 0bac740a5da9c1e57ea1755c58579463da4a883a
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580430"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>Postupy: Úprava skupiny informací pomocí Azure Active Directory

Pomocí Azure Active Directory, můžete upravit nastavení skupiny, včetně aktualizace jeho název, popis nebo typ členství.

## <a name="to-edit-your-group-settings"></a>Chcete-li upravit nastavení skupiny
1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

    **Skupiny – všechny skupiny** se zobrazí stránka zobrazuje všechny aktivní skupiny.

3. Z **skupiny – všechny skupiny** stránky, zadejte co největší část názvu skupiny, jak můžete do **hledání** pole. Pro účely tohoto článku jsme při hledání **zásady MDM - západní** skupiny.

    Výsledky hledání zobrazeny podle **hledání** pole, aktualizují se při psaní více znaků.

    ![Stránka všech skupin, se hledaný text do vyhledávacího pole](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Vyberte skupinu **zásady MDM - západní**a pak vyberte **vlastnosti** z **spravovat** oblasti.

    ![Stránka Přehled skupiny s číslem a členů a zvýrazněnou možností člena](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Aktualizace **obecné nastavení** informace podle potřeby, včetně:

    ![Nastavení vlastností pro skupinu](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Název skupiny.** Upravte název existující skupiny.
    
    - **Popis skupiny.** Upravte existující popis skupiny.

    - **Typ skupiny.** Typ skupiny nelze změnit po vytvoření. Chcete-li změnit **typ skupiny**, musíte odstranit skupinu a vytvořte novou.
    
    - **Typ členství.** Změníte typ členství. Další informace o různých typech dostupné členy najdete v tématu [postupy: vytvoření základní skupiny a přidání členů pomocí portálu Azure Active Directory](active-directory-groups-create-azure-portal.md)
    
    - **ID objektu.** ID objektu nelze změnit, ale můžete je zkopírovat používané k příkazům Powershellu pro skupinu. Další informace o používání rutin prostředí PowerShell najdete v tématu [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Další postup
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Postup přidání nebo odebrání členů ze skupiny](active-directory-groups-members-azure-portal.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Přiřazení nebo přidání předplatného Azure ke službě Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
