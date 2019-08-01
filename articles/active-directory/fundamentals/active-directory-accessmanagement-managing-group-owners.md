---
title: Přidání nebo odebrání vlastníků skupiny – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat nebo odebrat vlastníkům pomocí Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d259be0c04af0fcf3628a9f296730749404610cb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562087"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Přidání nebo odebrání vlastníků skupin v Azure Active Directory
Skupiny Azure Active Directory (Azure AD) je vlastněna a řízena vlastníky skupiny. Vlastníci skupiny můžou být uživatelé nebo instanční objekty a můžou spravovat skupinu včetně členství. Onwers skupiny můžou přiřadit jenom stávající vlastníci skupiny nebo skupiny – Správa správců. Vlastníci skupiny nemusejí být členy skupiny.

Pokud skupina nemá žádného vlastníka, skupina – Správa správců stále dokáže spravovat skupinu.

## <a name="add-an-owner-to-a-group"></a>Přidání vlastníka do skupiny
Níže jsou uvedeny pokyny pro přidání uživatele jako vlastníka do skupiny pomocí portálu Azure AD. Pokud chcete přidat instanční objekt jako vlastníka skupiny, postupujte podle pokynů v tématu použití [PowerShellu](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

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
