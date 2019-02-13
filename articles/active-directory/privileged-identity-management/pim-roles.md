---
title: Role nelze spravovat v PIM – Azure | Dokumentace Microsoftu
description: Popisuje role, které nelze spravovat v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 666b534c3ab07be2b1e2335015fc4723782a1249
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181571"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Role, které nelze spravovat v PIM

Azure AD Privileged Identity Management (PIM) umožňuje spravovat všechny [role adresáře Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a všechny [role prostředků Azure](../../role-based-access-control/built-in-roles.md). Pracovníci v těchto rolích také obsahovat vaše vlastní role připojené skupiny pro správu, předplatná, skupiny prostředků a prostředky. Existuje však několik rolí, které nelze spravovat. Tento článek popisuje role, které nelze spravovat v PIM.

## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

Následující role správce klasickém modelu předplatného v PIM nemůže spravovat:

- Správce účtu
- Správce služeb
- Spolusprávce

Další informace o rolích správce klasickém modelu předplatného najdete v tématu [Classic role správců předplatného, role Azure RBAC a rolích správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Co o rolích správců Office 365?

Role v systému Exchange Online nebo SharePoint Online, s výjimkou správce serveru Exchange a Správce služby SharePoint, nejsou reprezentovány ve službě Azure AD a není možné spravovat v PIM. Další informace o těchto službách Office 365 najdete v tématu [role správců Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Správce služby SharePoint má přístupová oprávnění k Sharepointu Online prostřednictvím centra pro správu Sharepointu Online a můžete provádět skoro všechny úlohy v Sharepointu Online. Oprávnění uživatelé setkat zpoždění až po dokončení aktivace v PIM pomocí této role v rámci služby SharePoint.

## <a name="next-steps"></a>Další postup

- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
