---
title: PowerShell pro role Azure AD v PIM – Azure AD | Microsoft Docs
description: Spravujte role Azure AD pomocí rutin PowerShellu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2774c63d34de3de951aa8076b56d203b976158ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548746"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell pro role Azure AD v Privileged Identity Management

Tento článek obsahuje pokyny k používání rutin prostředí PowerShell pro Azure Active Directory (Azure AD) ke správě rolí Azure AD v Privileged Identity Management (PIM). Dozvíte se taky, jak si nastavit modul Azure AD PowerShell.

> [!Note]
> Náš oficiální PowerShell je podporován pouze v případě, že používáte novou verzi Azure AD Privileged Identity Management. Přejděte prosím na Privileged Identity Management a ujistěte se, že je v okně rychlý Start k dispozici následující nápis.
> [![ověřte verzi Privileged Identity Management, kterou máte](media/pim-how-to-add-role-to-user/pim-new-version.png "Vyberte Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) . Pokud tento banner neznáte, počkejte prosím, než v současnosti probíhá zavádění tohoto aktualizovaného prostředí za několik následujících týdnů.
> Rutiny prostředí PowerShell pro Privileged Identity Management jsou podporovány prostřednictvím modulu Azure AD Preview. Pokud používáte jiný modul a tento modul vrací chybovou zprávu, začněte prosím používat tento nový modul. Pokud máte nějaké produkční systémy postavené na jiném modulu, obraťte se na [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>Instalace a nastavení

1. Instalace modulu Azure AD Preview

    ```powershell
    Install-module AzureADPreview
    ```

1. Než budete pokračovat, ujistěte se, že máte požadovaná oprávnění role. Pokud se pokoušíte provádět úlohy správy, jako je poskytnutí přiřazení role nebo aktualizace nastavení role, ujistěte se, že máte roli správce globálního správce nebo privilegované role. Pokud se právě pokoušíte aktivovat vlastní přiřazení, nemusíte mít žádná oprávnění nad rámec výchozích oprávnění uživatele.

1. Připojte se k Azure AD.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Vyhledejte ID tenanta pro vaši organizaci Azure AD tak, že na **Azure Active Directory**  >  **vlastnosti**  >  **ID adresáře**. V části rutiny použijte toto ID vždy, když potřebujete zadat resourceId.

    ![Vyhledání ID organizace ve vlastnostech organizace Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> V následujících částech najdete jednoduché příklady, které vám pomůžou začít pracovat. Podrobnější dokumentaci týkající se následujících rutin najdete na adrese [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management) . Je však nutné nahradit "azureResources" v parametru providerID parametrem "aadRoles". Bude také nutné pamatovat na použití ID tenanta pro vaši organizaci Azure AD jako parametr resourceId.

## <a name="retrieving-role-definitions"></a>Načítání definic rolí

Pomocí následující rutiny můžete získat všechny předdefinované a vlastní role služby Azure AD ve vaší organizaci Azure AD. V tomto důležitém kroku získáte mapování mezi názvem role a roleDefinitionId. RoleDefinitionId se používá v rámci těchto rutin, aby odkazovaly na konkrétní roli.

RoleDefinitionId je specifický pro vaši organizaci Azure AD a liší se od roleDefinitionId vráceného rozhraním API pro správu rolí.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Result:

![Získat všechny role pro organizaci Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Načítají se přiřazení rolí.

Pomocí následující rutiny načtěte všechna přiřazení rolí ve vaší organizaci Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Pomocí následující rutiny načtěte všechna přiřazení rolí pro konkrétního uživatele. Tento seznam se také označuje jako "Moje role" na portálu Azure AD. Jediným rozdílem je, že jste přidali filtr pro ID předmětu. ID subjektu v tomto kontextu je ID uživatele nebo ID skupiny.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Pomocí následující rutiny načtěte všechna přiřazení rolí pro určitou roli. RoleDefinitionId je zde ID, které vrací předchozí rutina.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

Výsledkem rutin jsou seznam objektů přiřazení rolí, které jsou uvedeny níže. ID subjektu je ID uživatele, ke kterému se role přiřadí. Stav přiřazení může být buď aktivní, nebo oprávněný. Pokud je uživatel aktivní a v poli LinkedEligibleRoleAssignmentId je ID, znamená to, že role je aktuálně aktivována.

Result:

![Načíst všechna přiřazení rolí pro organizaci Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Přiřazení role

K vytvoření oprávněného přiřazení použijte následující rutinu.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

Plán, který definuje počáteční a koncový čas přiřazení, je objekt, který lze vytvořit podobně jako v následujícím příkladu:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Pokud je hodnota Koncovédatumačas nastavená na null, indikuje se trvalé přiřazení.

## <a name="activate-a-role-assignment"></a>Aktivace přiřazení role

K aktivaci oprávněného přiřazení použijte následující rutinu.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

Tato rutina je skoro shodná s rutinou pro vytvoření přiřazení role. Klíčový rozdíl mezi rutinami je, že pro parametr – Type je aktivace "userAdd" místo "adminAdd". Druhým rozdílem je, že parametr – AssignmentState je "aktivní" místo "způsobilý".

> [!Note]
> Existují dva omezení scénářů pro aktivaci rolí prostřednictvím PowerShellu.
> 1. Pokud v nastavení role vyžadujete číslo systému nebo lístku lístku, neexistuje způsob, jak je dodat jako parametr. Proto by nebylo možné tuto roli aktivovat mimo Azure Portal. Tato funkce je v rámci prostředí PowerShell zahrnuta v následujících několika měsících.
> 1. Pokud pro aktivaci rolí požadujete vícefaktorové ověřování, neexistuje v současnosti žádný způsob, jak by prostředí PowerShell při aktivaci své role pomohlo uživatele spustit výzvu. Místo toho budou muset uživatelé při připojení ke službě Azure AD aktivovat výzvu MFA pomocí [tohoto příspěvku na blogu](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) od některého z našich inženýrů. Pokud vyvíjíte aplikaci pro PIM, může být jednou z možných implementací výzvou uživatelů a jejich opětovné připojení k modulu poté, co obdrží chybu "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Načítání a aktualizace nastavení rolí

K získání všech nastavení rolí ve vaší organizaci Azure AD použijte následující rutinu.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Nastavení obsahuje čtyři hlavní objekty. PIM používá jenom tři z těchto objektů. UserMemberSettings jsou nastavení aktivace, AdminEligibleSettings jsou nastavení přiřazení pro oprávněná přiřazení a AdminmemberSettings jsou nastavení přiřazení pro aktivní přiřazení.

[![Získá a aktualizuje nastavení role.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Chcete-li aktualizovat nastavení role, je nutné získat existující objekt nastavení pro určitou roli a provést změny v něm:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

Pak můžete použít nastavení na jeden z objektů pro určitou roli, jak je znázorněno níže. ID tady je ID nastavení role, které se dá načíst z výsledku rutiny nastavení role seznamu.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí v Azure AD](../roles/permissions-reference.md)
