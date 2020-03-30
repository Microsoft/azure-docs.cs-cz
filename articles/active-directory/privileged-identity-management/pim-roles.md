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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895205"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, které nelze spravovat ve správě privilegovaných identit

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umožňuje spravovat všechny [role Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a všechny [role prostředků Azure](../../role-based-access-control/built-in-roles.md). Tyto role také zahrnují vlastní role připojené k vašim skupinám pro správu, předplatným, skupinám prostředků a prostředkům. Existuje však několik rolí, které nelze spravovat. Tento článek popisuje role, které nelze spravovat ve správě privilegovaných identit.

## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

Ve správě privilegovaných identit nelze spravovat následující klasické role správce předplatného:

- Správce účtu
- Správce služeb
- Spolusprávce

Další informace o klasických rolích správce předplatného najdete v [tématu Klasické role správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>A co role správců Office 365?

Role v rámci Exchange Online nebo SharePointu Online, s výjimkou správce Exchange a správce SharePointu, nejsou ve službě Azure AD reprezentované, a proto je nelze spravovat ve správě privilegovaných identit. Další informace o těchto službách Office 365 najdete v [tématu Role správců Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Správce SharePointu má přístup pro správce k SharePointu Online přes Centrum pro správu SharePointu Online a v SharePointu Online může provádět téměř jakýkoli úkol. Oprávnění uživatelé mohou po aktivaci v aplikaci Privileged Identity Management zaznamenat zpoždění při používání této role v rámci služby SharePoint.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
- [Přiřazení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-assign-roles.md)
