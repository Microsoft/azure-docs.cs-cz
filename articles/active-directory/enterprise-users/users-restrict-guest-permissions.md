---
title: Omezení oprávnění přístupu uživatele typu Host – Azure Active Directory | Microsoft Docs
description: Omezení oprávnění k přístupu uživatele typu Host pomocí Azure Portal, PowerShellu nebo Microsoft Graph v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501497"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Omezení oprávnění k přístupu hosta (Preview) v Azure Active Directory

Azure Active Directory (Azure AD) umožňuje omezit, co můžou externí uživatelé typu Host vidět ve svých organizacích ve službě Azure AD. Uživatelé typu Host jsou ve výchozím nastavení ve službě Azure AD nastavené na úroveň omezeného oprávnění, zatímco výchozí nastavení pro členské uživatele je úplná sada výchozích oprávnění uživatele. Toto je verze Preview nové úrovně oprávnění uživatele hosta v nastavení externích spolupráci vaší organizace Azure AD pro ještě více omezený přístup, takže vaše možnosti přístupu k hostům teď jsou:

Úroveň oprávnění         | Úroveň přístupu | Hodnota
----------------         | ------------ | -----
Stejné jako členské uživatele     | Hosté mají stejný přístup k prostředkům Azure AD jako členské uživatele. | a0b1b346-4d3e-4e8b-98f8-753987be4970
Omezený přístup (výchozí) | Hosté můžou zobrazit členství všech neskrytých skupin. | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Omezený přístup (nový)**  | **Hosté nemůžou zobrazit členství v žádné skupině.** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

Když je přístup hosta omezený, můžou hosté zobrazit jenom svůj vlastní profil uživatele. Oprávnění k zobrazení jiných uživatelů není povoleno ani v případě, že host hledá hlavní název uživatele nebo identifikátor objectId. Omezený přístup taky omezuje uživatelům typu Host, aby viděli členství ve skupinách, ve kterých se nachází. Další informace o celkových výchozích uživatelských oprávněních, včetně uživatelských oprávnění typu Host, najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Oprávnění a licence

Abyste mohli nakonfigurovat externí nastavení spolupráce, musíte být v roli globálního správce. Pro omezení přístupu hostů nejsou k dispozici žádné další licenční požadavky.

## <a name="update-in-the-azure-portal"></a>Aktualizace v Azure Portal

Provedli jsme změny stávajících Azure Portalch ovládacích prvků pro uživatelská oprávnění typu Host.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění globálního správce.
1. Na stránce Přehled **Azure Active Directory** vaší organizace vyberte **nastavení uživatele**.
1. V části **externí uživatelé** vyberte **Spravovat nastavení externí spolupráce**.
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
Jak omezená oprávnění ovlivňují, které skupiny můžou zobrazit hosté? | Bez ohledu na výchozí nebo omezené oprávnění hosta nemohou hosté vytvořit výčet seznamu skupin nebo uživatelů. Hosté můžou zobrazit skupiny, které jsou členy v Azure Portal i na portálu moje aplikace v závislosti na oprávněních:<li>**Výchozí oprávnění**: Pokud chcete najít skupiny, které jsou členy v Azure Portal, musí uživatel v seznamu **Všichni uživatelé** vyhledat své ID objektu a pak vybrat **skupiny**. Tady uvidí seznam skupin, které jsou členy, včetně všech podrobností o skupině, včetně názvu, e-mailu a tak dále. Na portálu moje aplikace uvidí seznam skupin, které vlastní, a skupiny, které jsou členy.</li><li>**Omezená oprávnění hostů**: v Azure Portal stále můžou najít seznam skupin, které jsou členy, tak, že v seznamu všichni uživatelé vyhledá své ID objektu a pak vybere skupiny. Mohou zobrazit pouze velmi omezené podrobnosti o skupině, zejména ID objektu. Podle návrhu jsou sloupce název a E-mail prázdné a typ skupiny nebyl rozpoznán. Na portálu moje aplikace nebudou mít přístup k seznamu skupin, které vlastní nebo které skupiny jsou členy.</li><br>Podrobnější porovnání oprávnění adresáře, která pocházejí z Graph API, najdete v tématu věnovaném [výchozím oprávněním uživatele](../fundamentals/users-default-permissions.md#member-and-guest-users).
Na které části portálu moje aplikace bude tato funkce mít vliv? | Tato nová oprávnění budou respektovat funkce skupin na portálu moje aplikace. To zahrnuje všechny cesty pro zobrazení seznamu skupin a členství ve skupinách v mých aplikacích. V dostupnosti dlaždice skupiny nebyly provedeny žádné změny. Existující nastavení skupiny v Azure Portal nadále řídí dostupnost dlaždice skupiny.
Potlačí tato oprávnění nastavení hostů pro SharePoint nebo Microsoft Teams? | No. Tato stávající nastavení stále ovládají prostředí a přístup k nim v těchto aplikacích. Pokud se například zobrazí problémy na SharePointu, zkontrolujte nastavení externího sdílení.
Jaké jsou známé problémy s kompatibilitou v Planneru a Yammeru? | <li>S oprávněním nastaveným na hodnotu "omezeno" můžou hosté přihlášení do mobilní aplikace Planner získat přístup k jejich plánům nebo úlohám.<li>S oprávněním nastaveným na hodnotu "omezeno" budou hosté přihlášení do Yammeru moci opustit skupinu.
Budou se moje stávající oprávnění hostů v mém tenantovi měnit? | V aktuálním nastavení se neudělaly žádné změny. Udržujeme zpětnou kompatibilitu s vaším stávajícím nastavením. Rozhodnete, že chcete provést změny.
Budou tato oprávnění nastavena ve výchozím nastavení? | No. Existující výchozí oprávnění zůstanou beze změny. Volitelně můžete nastavit oprávnění pro více omezující podmínky.
Existují pro tuto funkci nějaké licenční požadavky? | Ne, s touto funkcí neexistují žádné nové licenční požadavky.

## <a name="next-steps"></a>Další kroky

- Další informace o stávajících oprávněních hostů v Azure AD najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Chcete-li zobrazit metody rozhraní API Microsoft Graph pro omezení přístupu hostů, přečtěte si téma [typ prostředku authorizationPolicy](/graph/api/resources/authorizationpolicy)
- Pokud chcete všem uživatelům přístup odvolat, přečtěte si téma [Odvolání přístupu uživatele ve službě Azure AD](users-revoke-access.md) .
