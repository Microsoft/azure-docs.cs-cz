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
ms.openlocfilehash: 80fbad64cda9267e468f9385d48dd5d40468eaca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203415"
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

- [Zahájení práce s PIM](pim-getting-started.md)
- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
