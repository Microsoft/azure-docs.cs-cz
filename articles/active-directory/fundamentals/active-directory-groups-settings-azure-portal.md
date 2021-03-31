---
title: Úprava informací o skupině – Azure Active Directory | Microsoft Docs
description: Pokyny k úpravám informací o skupině pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c346877d29c5fef03b45c895cbf8d462f90d0979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369127"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Úprava informací o skupině pomocí Azure Active Directory

Pomocí Azure Active Directory (Azure AD) můžete upravit nastavení skupiny, včetně aktualizace jejího názvu, popisu nebo typu členství.

## <a name="to-edit-your-group-settings"></a>Úprava nastavení skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory** a pak vyberte **skupiny**.

    Zobrazí se stránka **skupiny – všechny skupiny** se zobrazenými všemi aktivními skupinami.

3. Na stránce **skupiny – všechny skupiny** zadejte do vyhledávacího pole tolik názvu skupiny jako do **vyhledávacího** pole. Pro účely tohoto článku hledáme skupinu **zásad MDM – západ** .

    Výsledky hledání se zobrazí pod **vyhledávacím** polem a při psaní více znaků se aktualizují.

    ![Stránka všechny skupiny s textem hledání ve vyhledávacím poli](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Vyberte skupinu **MDM Policy-západ** a potom v oblasti **Správa** vyberte **vlastnosti** .

    ![Stránka s přehledem skupin s možnostmi členů a zvýrazněnými informacemi](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Podle potřeby aktualizujte Obecné informace o **Nastavení** , včetně:

    ![Nastavení vlastností pro skupinu](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Název skupiny** Upravte název existující skupiny.
    
    - **Popis skupiny** Upravte popis existující skupiny.

    - **Typ skupiny** Po vytvoření skupiny nelze změnit její typ. Chcete-li změnit **typ skupiny**, je nutné odstranit skupinu a vytvořit novou.
    
    - **Typ členství.** Změňte typ členství. Další informace o různých dostupných typech členství najdete v tématu [Postup: Vytvoření základní skupiny a přidání členů pomocí portálu Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
    - **ID objektu** ID objektu nemůžete změnit, ale můžete ho zkopírovat, abyste ho mohli použít v příkazech PowerShellu pro danou skupinu. Další informace o použití rutin PowerShellu najdete v tématu [rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../enterprise-users/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Další kroky
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Postup přidání nebo odebrání členů ze skupiny](active-directory-groups-members-azure-portal.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../enterprise-users/groups-create-rule.md)

- [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Přidružte nebo přidejte předplatné Azure k Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
