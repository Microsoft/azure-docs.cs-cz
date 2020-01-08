---
title: Přiřazení role adresáře pro uživatele – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přiřadit role správce a bez oprávnění správce pro uživatele se službou Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422912"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Přiřazení rolí správce a bez oprávnění správce uživatelům v Azure Active Directory
Pokud uživatel ve vaší organizaci potřebuje oprávnění ke správě prostředků Azure Active Directory (Azure AD), musíte přiřadit uživateli příslušné role ve službě Azure AD podle akce, které uživatel potřebuje oprávnění k provedení.

Další informace o dostupných rolí najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Další informace o přidávání uživatelů najdete v tématu [přidání nových uživatelů do služby Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Přiřazení rolí
Běžný způsob, jak přiřadit role Azure AD pro uživatele je na **role adresáře** stránky pro uživatele.

Můžete také přiřadit role pomocí Privileged Identity Management (PIM). Podrobné informace o tom, jak používat PIM, naleznete v tématu [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Přiřazení role uživateli
1. Přejít na [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí účtu globálního správce pro adresář. 

2. Vyhledejte a vyberte **Azure Active Directory**.

      ![Azure Portal hledání Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Vyberte možnost **Uživatelé**.

4. Vyhledejte a vyberte uživatele, který získává přiřazení role. Například _Alain Charon_.

      ![Stránka všichni uživatelé – vybrat uživatele](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stránce **Alain Charon-profil** vyberte **přiřazené role**.

    **Alain Charon - role adresáře** se zobrazí stránka.

6. Vyberte **Přidat přiřazení**, vyberte roli, kterou chcete přiřadit Alain (například _správce aplikace_) a pak zvolte **Vybrat**.

    ![Stránka přiřazených rolí – zobrazuje vybranou roli](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Role správce aplikace je přiřazená Alain Charon a zobrazí se na **Alain Charon - role adresáře** stránky.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role
Pokud je potřeba odebrat přiřazení role od uživatele, můžete také provést z **Alain Charon - role adresáře** stránky.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Odebrání uživateli přiřazení role

1. Vyberte **Azure Active Directory**vyberte **uživatelé**a poté vyhledejte a vyberte uživatele, získávání odebrat přiřazení role. Například _Alain Charon_.

2. Vyberte **přiřazené role**, vyberte **správce aplikace**a pak vyberte **odebrat přiřazení**.

    ![Stránka přiřazené role, zobrazení vybrané role a možnosti odebrat](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Role správce aplikace se odebere z Alain Charon a již nezobrazuje v **Alain Charon - role adresáře** stránky.

## <a name="next-steps"></a>Další kroky
- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přidání nebo změně informací profilu](active-directory-users-profile-azure-portal.md)

- [Přidání uživatelů typu host z jiného adresáře](../b2b/what-is-b2b.md)

Nebo můžete provádět další úkoly při správě uživatelů, jako je například přiřazení delegáty, pomocí zásad, sdílení a uživatelské účty. Další informace o dalších dostupných akcí najdete v tématu [dokumentace ke službě Azure Active Directory uživatele management](../users-groups-roles/index.yml).


