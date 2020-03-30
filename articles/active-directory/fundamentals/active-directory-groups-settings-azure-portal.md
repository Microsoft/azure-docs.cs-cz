---
title: Úprava informací o skupině – Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak upravit informace o skupině pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561874"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Úprava informací o skupině pomocí služby Azure Active Directory

Pomocí služby Azure Active Directory (Azure AD) můžete upravit nastavení skupiny, včetně aktualizace jejího názvu, popisu nebo typu členství.

## <a name="to-edit-your-group-settings"></a>Úprava nastavení skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Službu Azure Active Directory**a pak vyberte **Skupiny**.

    Zobrazí se stránka **Skupiny – všechny skupiny,** která zobrazuje všechny aktivní skupiny.

3. Na stránce **Skupiny – všechny skupiny** zadejte do vyhledávacího pole co nejvíce názvu **skupiny.** Pro účely tohoto článku hledáme **zásady MDM - západní** skupiny.

    Výsledky hledání se zobrazí pod polem **Hledat** a aktualizují se při psaní dalších znaků.

    ![Stránka Všechny skupiny s hledaným textem v poli Hledat](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Vyberte **zásadu MDM skupiny – západ**a pak v oblasti **Správa** vyberte **Vlastnosti.**

    ![Stránka Přehled skupiny se zvýrazněnou možností Člen a informacemi](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Podle potřeby aktualizujte obecné informace o **nastavení,** včetně:

    ![Nastavení vlastností skupiny](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Název skupiny.** Upravte název existující skupiny.
    
    - **Popis skupiny**: Upravte popis existující skupiny.

    - **Typ skupiny.** Typ skupiny po vytvoření nelze změnit. Chcete-li změnit **typ skupiny**, je nutné skupinu odstranit a vytvořit novou.
    
    - **Typ členství.** Změňte typ členství. Další informace o různých dostupných typech členství najdete v [tématu Postup: Vytvoření základní skupiny a přidání členů pomocí portálu Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
    - **ID objektu.** ID objektu nemůžete změnit, ale můžete ho zkopírovat a použít ho v příkazech PowerShellu pro skupinu. Další informace o používání rutin prostředí PowerShell najdete v [tématu Rutiny služby Azure Active Directory, které najdete v tématu konfigurace nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Další kroky
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Přidání nebo odebrání členů ze skupiny](active-directory-groups-members-azure-portal.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Přidružení nebo přidání předplatného Azure do Služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
