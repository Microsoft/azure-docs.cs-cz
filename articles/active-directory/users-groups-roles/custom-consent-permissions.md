---
title: Oprávnění pro vyjádření souhlasu aplikace pro vlastní role v Azure Active Directory | Microsoft Docs
description: V Azure Portal, PowerShellu nebo v Graph API se zobrazí náhled oprávnění pro vlastní role služby Azure AD.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 10/06/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: dbaf49d6be547137e4b082e2fc2a9c65d734907b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804434"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Oprávnění pro vyjádření souhlasu aplikace pro vlastní role v Azure Active Directory

Tento článek obsahuje aktuálně dostupná oprávnění pro vyjádření souhlasu aplikace pro definice vlastních rolí v Azure Active Directory (Azure AD). V tomto článku najdete oprávnění požadovaná pro některé běžné scénáře týkající se souhlasu a oprávnění aplikací.

## <a name="required-license-plan"></a>Požadovaný licenční plán

Použití této funkce vyžaduje licenci Azure AD Premium P1 pro vaši organizaci Azure AD. Pokud chcete najít správnou licenci pro vaše požadavky, přečtěte si [porovnání obecně dostupných funkcí edic Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Oprávnění pro vyjádření souhlasu aplikace

Pomocí oprávnění uvedených v tomto článku můžete spravovat zásady pro vyjádření souhlasu s aplikacemi a také oprávnění k udělení souhlasu aplikacím.

> [!NOTE]
> Portál pro správu Azure AD ještě nepodporuje přidávání oprávnění uvedených v tomto článku do definice role vlastního adresáře. [K vytvoření vlastní role adresáře](roles-create-custom.md#create-a-role-using-powershell) s oprávněními uvedenými v tomto článku musíte použít Azure AD PowerShell.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Udělení delegovaných oprávnění aplikacím jménem sebe (souhlas s uživatelem)

Aby uživatelům bylo umožněno udělit souhlas aplikacím jménem samotného (souhlas s uživatelem), a to v souladu se zásadami pro vyjádření souhlasu aplikace.

- Microsoft. Directory/servicePrincipals/managePermissionGrantsForSelf. účet

Kde `{id}` se nahradí ID [zásad souhlasu aplikace](../manage-apps/manage-app-consent-policies.md) , které nastaví podmínky, které musí být splněné, aby toto oprávnění bylo aktivní.

Pokud například chcete, aby uživatelé mohli udělit souhlas jménem vlastního uživatele v souladu s předdefinovanými zásadami souhlasu aplikace s ID `microsoft-user-default-low` , měli byste použít oprávnění `...managePermissionGrantsForSelf.microsoft-user-default-low` .

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Udělování oprávnění aplikacím jménem všech (souhlas správce)

Delegování souhlasu správce v úrovni tenanta aplikacím pro delegovaná oprávnění a oprávnění aplikací (role aplikací):

- Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. účet

Kde `{id}` se nahradí ID [zásad souhlasu aplikace](../manage-apps/manage-app-consent-policies.md) , které nastaví podmínky, které musí být splněny, aby toto oprávnění bylo možné použít.

Pokud třeba chcete, aby role assignees mohla udělit souhlas správce na úrovni tenanta pro aplikace, na které se vztahují [zásady souhlasu vlastních aplikací](../manage-apps/manage-app-consent-policies.md) s ID `low-risk-any-app` , použijete oprávnění `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` .

### <a name="managing-app-consent-policies"></a>Správa zásad pro vyjádření souhlasu s aplikacemi

Delegování vytváření, aktualizace a odstraňování [zásad pro vyjádření souhlasu s aplikacemi](../manage-apps/manage-app-consent-policies.md).

- Microsoft. Directory/permissionGrantPolicies/Create
- Microsoft. Directory/permissionGrantPolicies/Standard/Read
- Microsoft. Directory/permissionGrantPolicies/Basic/Update
- Microsoft. Directory/permissionGrantPolicies/DELETE

## <a name="full-list-of-permissions"></a>Úplný seznam oprávnění

Oprávnění | Popis
---------- | -----------
Microsoft. Directory/servicePrincipals/managePermissionGrantsForSelf. účet | Udělí možnost udělit souhlas aplikacím jménem sebe (souhlas s uživatelem), a to v souladu se zásadami pro vyjádření souhlasu s aplikací `{id}` .
Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. účet | Udělí oprávnění pro vyjádření souhlasu s aplikacemi jménem všech (souhlasu správce na úrovni tenanta) a podléhá zásadám pro vyjádření souhlasu s aplikacemi `{id}` .
Microsoft. Directory/permissionGrantPolicies/Standard/Read | Udělí možnost číst zásady pro vyjádření souhlasu s aplikacemi.
Microsoft. Directory/permissionGrantPolicies/Basic/Update | Udělí možnost aktualizovat základní vlastnosti u stávajících zásad pro vyjádření souhlasu s aplikacemi.
Microsoft. Directory/permissionGrantPolicies/Create | Udělí možnost vytvářet zásady pro vyjádření souhlasu s aplikacemi.
Microsoft. Directory/permissionGrantPolicies/DELETE | Udělí možnost odstraňovat zásady pro vyjádření souhlasu s aplikacemi.

## <a name="next-steps"></a>Další kroky

- [Vytváření vlastních rolí pomocí Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md)
