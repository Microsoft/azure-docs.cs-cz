---
title: Role, které nemůžete spravovat v PIM-Azure Active Directory | Microsoft Docs
description: Popisuje role, které nemůžete spravovat v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d66d433d9de537358777e54e3c7d5489c25c849b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804091"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Role, které nemůžete spravovat v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umožňuje spravovat všechny [role Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a všechny [role prostředků Azure](../../role-based-access-control/built-in-roles.md). Tyto role taky zahrnují vaše vlastní role připojené ke skupinám pro správu, předplatným, skupinám prostředků a prostředkům. Existuje však několik rolí, které nelze spravovat. Tento článek popisuje role, které nemůžete spravovat v PIM.

## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

Následující role Správce předplatného s klasickými oprávněními nemůžete spravovat v PIM:

- Správce účtu
- Správce služeb
- Spolusprávce

Další informace o rolích Správce předplatného pro Classic najdete v tématu [role správců pro klasické předplatné, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Co jsou role správců Office 365?

Role v rámci Exchange Online nebo SharePoint Online, s výjimkou správce Exchange a správce služby SharePoint, nejsou ve službě Azure AD zastoupeny a nelze je spravovat v PIM. Další informace o těchto službách Office 365 najdete v tématu [role správce sady office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Správce služby SharePoint má přístup správce k SharePointu Online prostřednictvím centra pro správu SharePointu Online a může provádět skoro jakoukoli úlohu v SharePointu Online. Oprávnění uživatelé můžou po aktivaci v rámci správce osobních informací v rámci služby SharePoint zaznamenat prodlevy s použitím této role.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Přiřazení rolí prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
