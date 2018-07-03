---
title: Přiřazení uživatele k rolím správce v Azure Active Directory | Microsoft Docs
description: Změna informací pro správu pro uživatele v Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: 7ed564d5954841f96109568b33183908d25bb8be
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939520"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Přiřazení uživatele k rolím správce v Azure Active Directory
Tento článek vysvětluje, jak uživateli přiřadit roli správce v Azure Active Directory (Azure AD). Informace o přidávání nových uživatelů v organizaci najdete v tématu [Přidávání nových uživatelů do služby Azure Active Directory](../add-users-azure-active-directory.md). Přidaní uživatelé nemají ve výchozím nastavení oprávnění správce, ale příslušné role jim můžete kdykoli přiřadit.

## <a name="assign-a-role-to-a-user"></a>Přiřazení role uživateli
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce nebo správce privilegovaných rolí.

2. Vyberte **Azure Active Directory**, pak **Uživatelé** a pak v seznamu vyberte konkrétního uživatele.

    ![Otevírání správy uživatelů](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. Pro vybraného uživatele vyberte **Role adresáře**, pak **Přidat roli** a pak v seznamu **Role adresáře** vyberte příslušné role správce, například **Správce podmíněného přístupu**. Další informace o rolích správce najdete v tématu [Přiřazování rolí správce ve službě Azure AD](../active-directory-assign-admin-roles-azure-portal.md). 

    ![Přiřazení uživatele k roli](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Stisknutím **Vybrat** uložte nastavení.

## <a name="next-steps"></a>Další kroky
* [Rychlý start: Přidání nebo odstranění uživatelů v Azure Active Directory](add-users-azure-active-directory.md)
* [Správa uživatelských profilů](active-directory-users-profile-azure-portal.md)
* [Přidání uživatelů typu host z jiného adresáře](../b2b/what-is-b2b.md) 
* [Přiřazení uživatele k roli ve službě Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Obnovení odstraněného uživatele](active-directory-users-restore.md)
