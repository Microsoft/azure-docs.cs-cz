---
title: Přiřazení uživatele k rolí správce ve službě Azure Active Directory | Microsoft Docs
description: Vysvětluje, jak změnit správce informace o uživateli v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 580c5e66bedbec00495fa5fd0a7632005fa7af2f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712989"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Přiřazení uživatele k rolí správce ve službě Azure Active Directory
Tento článek vysvětluje, jak přiřadit roli správce pro uživatele v Azure Active Directory (Azure AD). Informace o přidávání nových uživatelů v organizaci najdete v tématu [přidání nových uživatelů do služby Azure Active Directory](active-directory-users-create-azure-portal.md). Přidaní uživatelé nemají ve výchozím nastavení oprávnění správce, ale příslušné role jim můžete kdykoli přiřadit.

## <a name="assign-a-role-to-a-user"></a>Přiřazení role uživatele
1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **uživatelů a skupin**.

   ![Správa uživatelů otevírání](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Vyberte **všichni uživatelé**.
  
  ![Otevírání všechny skupiny uživatelů](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Vyberte uživatele ze seznamu.
5. Pro vybraného uživatele, vyberte **Directory role** a pak mu přiřaďte uživatele k roli z **Directory role** seznamu. Další informace o rolích uživatelů a správců najdete v článku [Přiřazení rolí správce ve službě Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Přiřazení uživatele k roli](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
* [Rychlé spuštění: Přidání nebo odstranění uživatelů ve službě Azure Active Directory](add-users-azure-active-directory.md)
* [Správa uživatelských profilů](active-directory-users-profile-azure-portal.md)
* [Přidat uživatele typu Host z jiného adresáře](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Přiřazení uživatele k roli ve službě Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Obnovení odstraněného uživatele](active-directory-users-restore.md)
