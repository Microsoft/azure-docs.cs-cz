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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d1d7ad27c43a0f8b4bbb763a1eb933a63cec58e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83115638"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, které nemůžete spravovat v Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umožňuje spravovat všechny [role Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a všechny [role Azure](../../role-based-access-control/built-in-roles.md). Role Azure můžou zahrnovat i vaše vlastní role připojené ke skupinám pro správu, předplatným, skupinám prostředků a prostředkům. Existuje však několik rolí, které nelze spravovat. Tento článek popisuje role, které nemůžete spravovat v Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

V Privileged Identity Management nemůžete spravovat tyto klasické role správců předplatného:

- Správce účtu
- Správce služeb
- Spolusprávce

Další informace o rolích Správce předplatného pro Classic najdete v tématu [role správců pro klasické předplatné, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Co jsou role správců Office 365?

Všechny role Office 365 podporujeme v prostředí portálu rolí a správců Azure AD, jako je například správce Exchange a správce služby SharePoint, ale nepodporujeme konkrétní role v rámci Exchange RBAC nebo SharePoint RBAC. Další informace o těchto službách Office 365 najdete v tématu [role správce sady office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Oprávnění uživatelé pro roli správce služby SharePoint, role Správce zařízení a všech rolí, které se pokoušejí získat přístup k centru zabezpečení a dodržování předpisů, můžou po aktivaci své role zaznamenat prodlevu až několik hodin. S těmito týmy pracujeme na řešení těchto problémů.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
