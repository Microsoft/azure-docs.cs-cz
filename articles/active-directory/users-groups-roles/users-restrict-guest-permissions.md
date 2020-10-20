---
title: Omezení oprávnění přístupu uživatele typu Host – Azure Active Directory | Microsoft Docs
description: Omezení oprávnění k přístupu uživatele typu Host pomocí Azure Portal, PowerShellu nebo Microsoft Graph v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 09/04/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b86cadefea41bc438c8f2e91c5eaa86af172b0
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204967"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Omezení oprávnění k přístupu hosta (Preview) v Azure Active Directory

Azure Active Directory (Azure AD) umožňuje omezit, co můžou externí uživatelé typu Host vidět ve svých organizacích ve službě Azure AD. Uživatelé typu Host jsou ve výchozím nastavení ve službě Azure AD nastavené na úroveň omezeného oprávnění, zatímco výchozí nastavení pro členské uživatele je úplná sada výchozích oprávnění uživatele. Toto je verze Preview nové úrovně oprávnění uživatele hosta v nastavení externích spolupráci vaší organizace Azure AD pro ještě více omezený přístup, takže vaše možnosti přístupu k hostům teď jsou:

Úroveň oprávnění         | Úroveň přístupu
----------------         | ------------
Stejné jako členské uživatele     | Hosté mají stejný přístup k prostředkům Azure AD jako členské uživatele.
Omezený přístup (výchozí) | Hosté můžou zobrazit členství všech neskrytých skupin.
**Omezený přístup (nový)**  | **Hosté nemůžou zobrazit členství v žádné skupině.**

Když je přístup hosta omezený, můžou hosté zobrazit jenom svůj vlastní profil uživatele. Oprávnění k zobrazení jiných uživatelů není povoleno ani v případě, že host hledá hlavní název uživatele nebo identifikátor objectId. Omezený přístup taky omezuje uživatelům typu Host, aby viděli členství ve skupinách, ve kterých se nachází. Další informace o celkových výchozích uživatelských oprávněních, včetně uživatelských oprávnění typu Host, najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Oprávnění a licence

Abyste mohli nakonfigurovat externí nastavení spolupráce, musíte být v roli globálního správce. Pro omezení přístupu hostů nejsou k dispozici žádné další licenční požadavky.

## <a name="update-in-the-azure-portal"></a>Aktualizace v Azure Portal

Provedli jsme změny stávajících Azure Portalch ovládacích prvků pro uživatelská oprávnění typu Host.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění globálního správce.
1. Na stránce Přehled **Azure Active Directory** vaší organizace vyberte **nastavení uživatele**.
1. V části **externí uživatelé**vyberte **Spravovat nastavení externí spolupráce**.
1. Na stránce **externí nastavení spolupráce** vyberte možnost **přístup uživatelů typu Host je omezený na vlastnosti a členství v vlastní možnosti adresářových objektů** .

    ![Stránka nastavení externí spolupráce Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Vyberte **Uložit**. Změny může trvat až 15 minut, než se projeví pro uživatele typu Host.

## <a name="update-with-the-microsoft-graph-api"></a>Aktualizace pomocí rozhraní Microsoft Graph API

Přidali jsme nové rozhraní Microsoft Graph API pro konfiguraci oprávnění hostů ve vaší organizaci Azure AD. K přiřazení libovolné úrovně oprávnění je možné provést následující volání rozhraní API. Hodnota pro guestUserRoleId, kterou zde využijete, slouží k ilustraci nejvíce omezeného uživatelského nastavení hostů. Další informace o použití Microsoft Graph k nastavení oprávnění hostů najdete v tématu [typ prostředku authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>První konfigurace

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Odpověď by měla být úspěšná 204.

### <a name="updating-the-existing-value"></a>Aktualizuje se stávající hodnota.

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Odpověď by měla být úspěšná 204.

### <a name="view-the-current-value"></a>Zobrazit aktuální hodnotu

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Příklad odpovědi:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Aktualizace pomocí rutin prostředí PowerShell

Díky této funkci jsme přidali možnost konfigurovat omezená oprávnění prostřednictvím rutin prostředí PowerShell v2. Rutiny PowerShellu Get a set byly publikovány ve verzi 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get – příkaz: Get-AzureADMSAuthorizationPolicy

Příklad:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set – příkaz: Set-AzureADMSAuthorizationPolicy

Příklad:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> V případě vyžádání musíte zadat authorizationPolicy jako ID.

## <a name="supported-microsoft-365-services"></a>Podporované služby Microsoft 365 Services

### <a name="supported-services"></a>Podporované služby

Tím, že je to podporováno, znamenáme, že prostředí je očekávané. Konkrétně to je totéž jako aktuální prostředí pro hosty.

- Teams
- Outlook (OWA)
- SharePoint
- Planner v týmech
- Webová aplikace Planner

### <a name="services-currently-not-supported"></a>Aktuálně nepodporované služby

Služba bez aktuální podpory může mít problémy s kompatibilitou s novým nastavením omezení hostů.

- Formuláře
- Mobilní aplikace Planneru
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Otázka | Odpověď
-------- | ------
Kde se tato oprávnění vztahují? | Tato oprávnění na úrovni adresáře se vynutila napříč službami a portály Azure AD, včetně Microsoft Graph, PowerShell v2, Azure Portal a portálu moje aplikace. Ovlivněné jsou taky Microsoft 365 služby, které využívají Microsoft 365 skupiny pro scénáře spolupráce, konkrétně Outlook, Microsoft teams a SharePoint.
Na které části portálu moje aplikace bude tato funkce mít vliv? | Tato nová oprávnění budou respektovat funkce skupin na portálu moje aplikace. To zahrnuje všechny cesty pro zobrazení seznamu skupin a členství ve skupinách v mých aplikacích. V dostupnosti dlaždice skupiny nebyly provedeny žádné změny. Existující nastavení skupiny na portálu pro správu Azure pořád řídí dostupnost dlaždic skupiny.
Potlačí tato oprávnění nastavení hostů pro SharePoint nebo Microsoft Teams? | Ne. Tato stávající nastavení stále ovládají prostředí a přístup k nim v těchto aplikacích. Pokud se například zobrazí problémy na SharePointu, zkontrolujte nastavení externího sdílení.
Jaké jsou známé problémy s kompatibilitou v Planneru a Yammeru? | <li>S oprávněním nastaveným na hodnotu "omezeno" budou hosté přihlášení do mobilní aplikace Planner moci získat přístup k jejich plánům nebo úlohám.<li>S oprávněním nastaveným na hodnotu "omezeno" budou hosté přihlášení do Yammeru moci opustit skupinu.
Budou se moje stávající oprávnění hostů v mém tenantovi měnit? | V aktuálním nastavení se neudělaly žádné změny. Udržujeme zpětnou kompatibilitu s vaším stávajícím nastavením. Rozhodnete, že chcete provést změny.
Budou tato oprávnění nastavena ve výchozím nastavení? | Ne. Existující výchozí oprávnění zůstanou beze změny. Volitelně můžete nastavit oprávnění pro více omezující podmínky.
Existují pro tuto funkci nějaké licenční požadavky? | Ne, s touto funkcí neexistují žádné nové licenční požadavky.

## <a name="next-steps"></a>Další kroky

- Další informace o stávajících oprávněních hostů v Azure AD najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../fundamentals/users-default-permissions.md).
- Pokud chcete zobrazit metody rozhraní API Microsoft Graph pro omezení přístupu hostů, přečtěte si téma [typ prostředku authorizationPolicy](/graph/api/resources/authorizationpolicy).
- Pokud chcete pro uživatele odvolat veškerý přístup, přečtěte si téma [Odvolání přístupu uživatele ve službě Azure AD](users-revoke-access.md).