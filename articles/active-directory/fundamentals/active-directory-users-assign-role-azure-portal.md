---
title: Přiřazení rolí adresářů uživatelům – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přiřadit role správce a role bez oprávnění správce uživatelům služby Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422912"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Přiřazení rolí správce a role bez oprávnění správce uživatelům služby Azure Active Directory
Pokud uživatel ve vaší organizaci potřebuje oprávnění ke správě prostředků Služby Azure Active Directory (Azure AD), musíte mu přiřadit odpovídající roli ve službě Azure AD na základě akcí, které uživatel potřebuje oprávnění k provedení.

Další informace o dostupných rolích najdete v [tématu Přiřazení rolí správce ve službě Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Další informace o přidávání uživatelů najdete v tématu [Přidání nových uživatelů do služby Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Přiřazení rolí
Běžný způsob, jak přiřadit role Azure AD uživateli, je na stránce **role Adresář** pro uživatele.

Role můžete také přiřadit pomocí správy privilegovaných identit (PIM). Podrobnější informace o používání PIM naleznete v [tématu Správa privilegovaných identit](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Přiřazení role uživateli
1. Přejděte na [portál Azure](https://portal.azure.com/) a přihlaste se pomocí globálního účtu správce pro adresář. 

2. Vyhledejte a vyberte **Azure Active Directory**.

      ![Hledání azure portálu pro Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Vyberte **Uživatelé**.

4. Vyhledejte a vyberte uživatele, který získává přiřazení role. Například _Alain Charon_.

      ![Stránka Všichni uživatelé - vyberte uživatele](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stránce **Alain Charon - Profil** vyberte **Přiřazené role**.

    Zobrazí se stránka **role Alain Charon - Directory.**

6. Vyberte **Přidat přiřazení**, vyberte roli, kterou chcete přiřadit Alainovi (například _Správce aplikace_), a pak zvolte **Vybrat**.

    ![Stránka Přiřazené role - zobrazení vybrané role](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Role správce aplikace je přiřazena Alainu Charonovi a zobrazí se na stránce **role Alain Charon - Directory.**

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role
Pokud potřebujete odebrat přiřazení role od uživatele, můžete to udělat také ze stránky **role Alain Charon - Directory.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Odebrání přiřazení role uživateli

1. Vyberte **Službu Azure Active Directory**, vyberte **Uživatelé**a pak vyhledejte a vyberte uživatele, který má odebráno přiřazení role. Například _Alain Charon_.

2. Vyberte **Přiřazené role**, vyberte **Správce aplikace**a pak vyberte **Odebrat přiřazení**.

    ![Stránka Přiřazené role zobrazující vybranou roli a možnost odebrat](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Role správce aplikace je odebrána z Alain Charon a již se nezobrazuje na stránce **role Alain Charon - Directory.**

## <a name="next-steps"></a>Další kroky
- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přidání nebo změna informací o profilu](active-directory-users-profile-azure-portal.md)

- [Přidání uživatelů typu host z jiného adresáře](../b2b/what-is-b2b.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je například přiřazení delegátů, použití zásad a sdílení uživatelských účtů. Další informace o dalších dostupných akcích naleznete v [dokumentaci ke správě uživatelů služby Azure Active Directory](../users-groups-roles/index.yml).


