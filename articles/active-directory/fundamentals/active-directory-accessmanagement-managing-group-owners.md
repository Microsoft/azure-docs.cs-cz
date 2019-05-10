---
title: Přidání nebo odebrání vlastníků skupiny – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat nebo odebrat vlastníkům pomocí Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd684e1bd48f877a74280b33b4df65d7baaa0fe7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507179"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Přidání nebo odebrání vlastníků skupin v Azure Active Directory
Skupiny Azure Active Directory (Azure AD) je vlastněna a řízena vlastníky skupiny. Vlastníci skupiny můžou mít uživatele nebo instanční objekty a jsou možnost Spravovat skupiny, včetně členství. Jen skupiny onwers můžete přiřadit stávající vlastníci skupiny nebo Správa skupiny administrators. Vlastníci skupiny nemusejí být členy skupiny.

Když skupina nemá vlastníka, budou mít pořád povolený ke správě skupiny správy skupiny administrators.

## <a name="add-an-owner-to-a-group"></a>Přidání vlastníka do skupiny
Níže jsou pokyny pro přidání uživatele jako vlastníka do skupiny pomocí portálu Azure AD. Přidání instančního objektu jako vlastníka skupiny, postupujte podle pokynů k tomu pomocí [Powershellu](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Chcete-li přidat vlastníka skupiny.
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**vyberte **skupiny**a pak vyberte skupinu, pro který chcete přidat jako vlastníka (v tomto příkladu *zásady MDM - západní*).

3. Na **zásady MDM – přehled – západ** stránce **vlastníky**.

    ![Zásady MDM – stránka s přehledem – západ se zvýrazněnou možností vlastníky](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na **MDM policy - západ - Owners** stránce **přidat vlastníky**a poté vyhledejte a vyberte uživatele, který bude nový vlastník skupiny a klikněte na tlačítko **vyberte**.

    ![Stránky vlastníky - západ - zásad MDM se zvýrazněnou možností přidat vlastníky](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po vybrání nového vlastníka, můžete aktualizovat **vlastníky** stránce a podívat se na jméno přidán do seznamu vlastníků.

## <a name="remove-an-owner-from-a-group"></a>Odebrání vlastníka ze skupiny
Odebrání vlastníka ze skupiny pomocí služby Azure AD.

### <a name="to-remove-an-owner"></a>Chcete-li odebrat jako vlastníka
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**vyberte **skupiny**a pak vyberte skupinu, pro kterou chcete odebrat vlastníka (v tomto příkladu *zásady MDM - západní*).

3. Na **zásady MDM – přehled – západ** stránce **vlastníky**.

    ![Zásady MDM – stránka s přehledem – západ se zvýrazněnou možností vlastníky](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na **MDM policy - západ - Owners** stránky, vyberte uživatele, kterou chcete odebrat jako vlastníka skupiny, zvolte **odebrat** ze stránky informace uživatele a vyberte **Ano** k potvrzení vaše rozhodnutí.

    ![Stránka informace o uživatele se zvýrazněnou možností odstranit](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po odebrání vlastníka, můžete se vrátit **vlastníky** stránce a podívejte se název je odebraná ze seznamu vlastníků.

## <a name="next-steps"></a>Další postup
- [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)

- [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md)

- [Použití skupin pro přiřazení přístupu k aplikaci SaaS integrované](../users-groups-roles/groups-saasapps.md)

- [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md)
