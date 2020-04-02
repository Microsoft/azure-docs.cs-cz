---
title: Role PowerShellu pro Azure AD v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Spravujte role Azure AD pomocí rutin Prostředí PowerShell v privilegované správě identit (PIM) azure ad.s.PowerShell (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519653"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Role PowerShellu pro Azure AD ve správě privilegovaných identit

Tento článek obsahuje pokyny pro použití rutin prostředí PowerShell azure active directory (Azure AD) ke správě rolí Azure AD v privilegované správě identit (PIM). Taky vám řekne, jak nastavit pomocí modulu Azure AD PowerShell.

> [!Note]
> Naše oficiální Prostředí PowerShell je podporované jenom v případě, že používáte novou verzi Azure AD Privileged Identity Management. Přejděte na privilegovanou správu identit a ujistěte se, že máte na okně rychlého startu následující banner.
> [![zkontrolujte verzi služby Privileged Identity Management, kterou máte](media/pim-how-to-add-role-to-user/pim-new-version.png "Vyberte službu Azure AD > správu privilegovaných identit")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Pokud nemáte tento banner, počkejte, prosím, jak jsme v současné době v procesu zavádění této aktualizované zkušenosti v příštích několika týdnech.
> Rutiny Prostředí PowerShell pro správu privilegovaných identit jsou podporované prostřednictvím modulu Azure AD Preview. Pokud jste používali jiný modul a tento modul nyní vrací chybovou zprávu, začněte používat tento nový modul. Pokud máte nějaké výrobní systémy postavené na jiném modulu, obraťte se napim_preview@microsoft.com

## <a name="installation-and-setup"></a>Instalace a nastavení

1. Instalace modulu Azure AD Preview

        Install-module AzureADPreview

1. Před pokračováním se ujistěte, že máte požadovaná oprávnění role. Pokud se pokoušíte provádět úlohy správy, jako je přiřazení role nebo aktualizace nastavení role, ujistěte se, že máte roli globálního správce nebo správce privilegované role. Pokud se právě pokoušíte aktivovat vlastní přiřazení, nejsou vyžadována žádná oprávnění nad rámec výchozích uživatelských oprávnění.

1. Připojte se ke službě Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Najděte id klienta tak, že přejdete na > **ID adresáře vlastností****služby** **Azure Active Directory** > . V části rutiny použijte toto ID, kdykoli potřebujete zadat resourceId.

    ![Najděte ID klienta ve vlastnostech organizace Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Následující části jsou jednoduché příklady, které vám mohou pomoci zprovoznit. Podrobnější dokumentaci týkající se následujících rutin https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_managementnaleznete na adrese . Však budete muset nahradit "azureResources" v parametru providerID s "aadRoles". Budete také muset pamatovat na použití ID klienta pro organizaci Azure AD jako parametr resourceId.

## <a name="retrieving-role-definitions"></a>Načítání definic rolí

Pomocí následující rutiny získat všechny předdefinované a vlastní role Azure AD ve vaší organizaci Azure AD (tenanta). Tento důležitý krok poskytuje mapování mezi názvem role a roleDefinitionId. RoleDefinitionId se používá v rámci těchto rutin, aby bylo možné odkazovat na konkrétní roli.

RoleDefinitionId je specifická pro vaši organizaci Azure AD a liší se od roleDefinitionId vrácené rozhraníapi pro správu rolí.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Result:

![Získání všech rolí pro organizaci Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Načítání přiřazení rolí

Pomocí následující rutiny načtěte všechna přiřazení rolí ve vaší organizaci Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Pomocí následující rutiny načtěte všechna přiřazení rolí pro konkrétního uživatele. Tento seznam se také označuje jako "Moje role" na portálu Azure AD. Jediný rozdíl je v tom, že jste přidali filtr pro ID předmětu. ID předmětu v tomto kontextu je ID uživatele nebo ID skupiny.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Následující rutina slouží k načtení všech přiřazení rolí pro určitou roli. RoleDefinitionId zde je ID, který je vrácen předchozí rutiny.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Výsledkem rutiny je seznam objektů přiřazení rolí uvedených níže. ID předmětu je ID uživatele, kterému je role přiřazena. Stav přiřazení může být aktivní nebo způsobilý. Pokud je uživatel aktivní a v poli LinkedEligibleRoleAssignmentId je ID, znamená to, že role je aktuálně aktivována.

Result:

![Načtení všech přiřazení rolí pro organizaci Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Přiřazení role

K vytvoření způsobilého přiřazení použijte následující rutinu.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

Plán, který definuje počáteční a koncový čas přiřazení, je objekt, který lze vytvořit jako následující příklad:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Pokud je hodnota endDateTime nastavena na hodnotu null, znamená trvalé přiřazení.

## <a name="activate-a-role-assignment"></a>Aktivace přiřazení role

K aktivaci způsobilého přiřazení použijte následující rutinu.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Tato rutina je téměř totožná s rutinou pro vytvoření přiřazení role. Hlavní rozdíl mezi rutinami je, že pro parametr –Type je aktivace "userAdd" místo "adminAdd". Dalším rozdílem je, že parametr -AssignmentState je "Aktivní" namísto "Způsobilé".

> [!Note]
> Existují dva omezující scénáře pro aktivaci role prostřednictvím prostředí PowerShell.
> 1. Pokud požadujete číslo lístku / lístku ve vašem nastavení role, neexistuje žádný způsob, jak je poskytnout jako parametr. Proto by nebylo možné aktivovat roli mimo portál Azure. Tato funkce se zavádí do prostředí PowerShell v příštích několika měsících.
> 1. Pokud pro aktivaci role vyžadujete vícefaktorové ověřování, neexistuje v současné době žádný způsob, jak powershell vyzvat uživatele při aktivaci jejich role. Místo toho uživatelé budou muset aktivovat výzvu MFA při připojení k Azure AD podle [tohoto blogu](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) od jednoho z našich inženýrů. Pokud vyvíjíte aplikaci pro PIM, jednou z možných implementací je vyzvat uživatele a znovu je připojit k modulu poté, co obdrží chybu "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Načítání a aktualizace nastavení rolí

Pomocí následující rutiny získáte všechna nastavení rolí ve vaší organizaci Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

V nastavení jsou čtyři hlavní objekty. Pouze tři z těchto objektů jsou aktuálně používány PIM. UserMemberSettings jsou nastavení aktivace, AdminEligibleSettings jsou nastavení přiřazení pro způsobilá přiřazení a AdminmemberSettings jsou nastavení přiřazení pro aktivní přiřazení.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Chcete-li aktualizovat nastavení role, budete muset nejprve definovat objekt nastavení následujícím způsobem:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Potom můžete pokračovat a použít nastavení na jeden z objektů pro konkrétní roli, jak je znázorněno níže. ID zde je ID nastavení role, které lze načíst z výsledku rutiny nastavení role seznamu.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
