---
title: Role, které nelze spravovat ve službě Privileged Identity Management – Azure Active Directory | Dokumenty společnosti Microsoft
description: Popisuje role, které nelze spravovat v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607527"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, které nelze spravovat ve správě privilegovaných identit

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umožňuje spravovat všechny [role Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a všechny [role Azure](../../role-based-access-control/built-in-roles.md). Role Azure můžou taky zahrnovat vaše vlastní role připojené k vašim skupinám pro správu, předplatným, skupinám prostředků a prostředkům. Existuje však několik rolí, které nelze spravovat. Tento článek popisuje role, které nelze spravovat ve správě privilegovaných identit.

## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

Ve správě privilegovaných identit nelze spravovat následující klasické role správce předplatného:

- Správce účtu
- Správce služeb
- Spolusprávce

Další informace o klasických rolích správce předplatného najdete v [tématu Klasické role správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>A co role správců Office 365?

Podporujeme všechny role Office365 v prostředí portálu Role Azure a Správců a správců, jako je správce Exchange a správce SharePointu, ale nepodporujeme konkrétní role v rámci Exchange RBAC nebo SharePoint RBAC. Další informace o těchto službách Office 365 najdete v [tématu Role správců Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Oprávnění uživatelé pro roli správce služby SharePoint a všechny role, které se pokoušejí získat přístup k Centru zabezpečení a dodržování předpisů společnosti Microsoft, mohou zaznamenat zpoždění až několik hodin po aktivaci své role. Spolupracujeme s těmito týmy na vyřešení problémů.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
- [Přiřazení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-assign-roles.md)
