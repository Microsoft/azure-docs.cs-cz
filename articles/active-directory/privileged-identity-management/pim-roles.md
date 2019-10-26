---
title: Role, které nemůžete spravovat v Privileged Identity Management-Azure Active Directory | Microsoft Docs
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895205"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, které nemůžete spravovat v Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umožňuje spravovat všechny [role Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a všechny [role prostředků Azure](../../role-based-access-control/built-in-roles.md). Tyto role taky zahrnují vaše vlastní role připojené ke skupinám pro správu, předplatným, skupinám prostředků a prostředkům. Existuje však několik rolí, které nelze spravovat. Tento článek popisuje role, které nemůžete spravovat v Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

V Privileged Identity Management nemůžete spravovat tyto klasické role správců předplatného:

- Správce účtu
- Správce služeb
- Spolusprávce

Další informace o rolích Správce předplatného pro Classic najdete v tématu [role správců pro klasické předplatné, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Co jsou role správců Office 365?

Role v rámci Exchange Online nebo SharePoint Online, s výjimkou správce Exchange a správce služby SharePoint, nejsou ve službě Azure AD zastoupeny a nelze je proto spravovat v Privileged Identity Management. Další informace o těchto službách Office 365 najdete v tématu [role správce sady office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Správce služby SharePoint má přístup správce k SharePointu Online prostřednictvím centra pro správu SharePointu Online a může provádět skoro jakoukoli úlohu v SharePointu Online. Po aktivaci v Privileged Identity Management můžou na základě této role v rámci služby SharePoint docházet k prodlevám s oprávněnými uživateli.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
