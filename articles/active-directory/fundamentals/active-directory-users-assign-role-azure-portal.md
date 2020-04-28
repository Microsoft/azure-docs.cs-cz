---
title: Přiřazení rolí adresáře uživatelům – Azure Active Directory | Microsoft Docs
description: Pokyny, jak přiřadit role správců a uživatelů bez oprávnění správce k Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422912"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory
Pokud uživatel ve vaší organizaci potřebuje oprávnění ke správě prostředků Azure Active Directory (Azure AD), musíte uživateli přiřadit příslušnou roli v Azure AD na základě akcí, které uživatel potřebuje k provedení.

Další informace o dostupných rolích najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Další informace o přidávání uživatelů najdete v tématu [Přidání nových uživatelů do Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Přiřazení rolí
Běžný způsob, jak přiřadit role Azure AD uživateli, je na stránce **role adresáře** pro uživatele.

Role můžete přiřadit také pomocí Privileged Identity Management (PIM). Podrobnější informace o tom, jak používat PIM, najdete v tématu [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Přiřazení role uživateli
1. Přejít na [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí účtu globálního správce pro adresář. 

2. Vyhledejte a vyberte **Azure Active Directory**.

      ![Azure Portal hledání Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Vyberte **Uživatelé**.

4. Vyhledejte a vyberte uživatele, který získává přiřazení role. Například _Alain Charon_.

      ![Stránka všichni uživatelé – vybrat uživatele](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stránce **Alain Charon-profil** vyberte **přiřazené role**.

    Zobrazí se stránka **role adresáře Charon pro Alain** .

6. Vyberte **Přidat přiřazení**, vyberte roli, kterou chcete přiřadit Alain (například _správce aplikace_) a pak zvolte **Vybrat**.

    ![Stránka přiřazených rolí – zobrazuje vybranou roli](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Role správce aplikace je přiřazena k Alain Charon a zobrazí se na stránce **role Alain Charon-Directory** .

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role
Pokud potřebujete odebrat přiřazení role z uživatele, můžete to udělat taky ze stránky **role Alain Charon-Directory** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Odebrání přiřazení role z uživatele

1. Vyberte **Azure Active Directory**, vyberte **uživatele**a pak vyhledejte a vyberte uživatele, který získal přiřazení role odebrané. Například _Alain Charon_.

2. Vyberte **přiřazené role**, vyberte **správce aplikace**a pak vyberte **odebrat přiřazení**.

    ![Stránka přiřazené role, zobrazení vybrané role a možnosti odebrat](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Role správce aplikace je odebrána z Alain Charon a už se nezobrazuje na stránce **role Alain Charon-Directory** .

## <a name="next-steps"></a>Další kroky
- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)

- [Přidání uživatelů typu host z jiného adresáře](../b2b/what-is-b2b.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je přiřazení delegátů, používání zásad a sdílení uživatelských účtů. Další informace o dalších dostupných akcích najdete v tématu [Azure Active Directory dokumentace správy uživatelů](../users-groups-roles/index.yml).


