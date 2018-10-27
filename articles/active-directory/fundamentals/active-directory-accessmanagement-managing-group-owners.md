---
title: Postup přidání nebo odebrání vlastníků skupin Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak přidat nebo odebrat vlastníky skupiny pomocí služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: fae68bccbeaa54ca1bab9d77510fe6baecd11fcc
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139716"
---
# <a name="how-to-add-or-remove-group-owners-in-azure-active-directory"></a>Postupy: Přidání nebo odebrání vlastníků skupin v Azure Active Directory
Skupiny Azure Active Directory (Azure AD) je vlastněna a řízena vlastníky skupiny. Vlastníci skupiny jsou přiřazeny ke správě skupiny a její členy podle vlastníka prostředku (správce). Vlastníci skupiny nemusejí být členy skupiny. Po přiřazení vlastníka skupiny jenom vlastník prostředku můžete přidat nebo odebrat vlastníky.

V některých případech se jako správce může rozhodnete přiřadit jako vlastníka skupiny. V tomto případě stát vlastníkem skupiny. Vlastníci kromě toho můžete přiřadit další vlastníky do skupiny, pokud jste to omezuje v nastavení skupiny.

## <a name="add-an-owner-to-a-group"></a>Přidání vlastníka do skupiny
Přidáte vlastníky skupiny. Další skupiny pomocí služby Azure AD.

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
