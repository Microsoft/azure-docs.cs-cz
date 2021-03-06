---
title: Přidat uživatele spolupráce B2B do role – Azure Active Directory
description: Přidat uživatele typu Host do role v Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575662"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Udělení oprávnění uživatelům z partnerských organizací ve vašem tenantovi Azure Active Directory

Uživatelé spolupráce B2B v Azure Active Directory (Azure AD) se uživatelům přidávají jako uživatelé typu Host do adresáře a oprávnění hosta v adresáři se standardně omezují. Vaše podnikání může vyžadovat, aby někteří uživatelé typu Host mohli ve vaší organizaci plnit role s vyšším oprávněním. Aby bylo možné podporovat definování rolí s vyšším oprávněním, můžete uživatele typu Host přidat k libovolným požadovaným rolím na základě potřeb vaší organizace.

Pokud je role adresáře přiřazena uživateli typu Host, bude se uživateli typu Host udělena další oprávnění, která jsou součástí role, včetně základních oprávnění ke čtení. Viz [předdefinované role Azure AD](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference).

## <a name="default-role"></a>Výchozí role

![Snímek obrazovky s výchozí rolí adresáře](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Role globálního správce

![Snímek obrazovky zobrazující roli globálního správce](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Omezená role správce

![Snímek obrazovky s rolí omezená role správce](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Vlastnosti uživatele spolupráce B2B](user-properties.md)
