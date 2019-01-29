---
title: 'Azure Active Directory Domain Services: Obor synchronizace | Dokumentace Microsoftu'
description: Konfigurace synchronizace s vymezeným oborem z Azure AD do spravované domény
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: e3d13082e3c076061b8d343827266ec04ae80646
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180683"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurace vymezených synchronizace z Azure AD do spravované domény
V tomto článku se dozvíte, jak nakonfigurovat pouze konkrétní uživatelské účty, které se mají synchronizovat z adresáře služby Azure AD do spravované domény služby Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Na základě skupin synchronizace s vymezeným oborem
Ve výchozím nastavení všichni uživatelé a skupiny v adresáři služby Azure AD jsou synchronizovány do spravované domény. Pokud pouze několika uživatelům používat spravovanou doménu, může synchronizovat pouze tyto uživatelské účty. Na základě skupin s vymezeným oborem synchronizace umožňuje udělat. Při konfiguraci, jsou synchronizovány pouze uživatelské účty, které patří do skupiny, které jste zadali do spravované domény.

Následující tabulka vám pomůže určit, jak používat vymezenou synchronizace:

| **Aktuální stav** | **Požadovaný stav** | **Požadované konfigurace** |
| --- | --- | --- |
| Svoji existující spravovanou doménu je nakonfigurovaný k synchronizaci všech uživatelských účtů a skupin. | Chcete synchronizovat pouze uživatelské účty, které patří do konkrétní skupiny do spravované domény. | [Odstranit existující spravovanou doménu](active-directory-ds-disable-aadds.md). Potom postupujte podle pokynů v tomto článku znovu vytvořit s vymezeným oborem synchronizace nakonfigurovaný. |
| Nemáte existující spravovanou doménu. | Chcete vytvořit nové spravované domény a synchronizovat pouze uživatelské účty, které patří do konkrétní skupiny. | Postupujte podle pokynů v tomto článku a vytvořte nové spravované doméně s vymezeným oborem synchronizace nakonfigurovaný. |
| Svoji existující spravovanou doménu je nakonfigurovaný k synchronizaci pouze účty, které patří do konkrétní skupiny. | Chcete-li upravit seznam skupin, jejichž uživatelé měli být synchronizováni na spravovat domény. | Postupujte podle pokynů v tomto článku a upravit vymezené synchronizace. |

> [!WARNING]
> **Změna rozsahu synchronizace způsobí, že kvůli tomu provádět znovu synchronizovat vaši spravovanou doménu.**
>
 * Při změně oboru synchronizace pro spravovanou doménu, dojde k úplné opětovné synchronizace.
 * Objekty, které jsou již není požadován ve spravované doméně se odstraní. Vytvoření nových objektů ve spravované doméně.
 * Opětovná synchronizace může trvat dlouhou dobu v závislosti na počtu objektů (uživatelů, skupin a členství ve skupinách) ve vaší spravované domény a adresáře služby Azure AD. Pro velké adresáře s mnoha stovek tisíc objektů může pár dní trvat opětovné synchronizace.
>
>


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Vytvořit nové spravované domény a povolit na základě skupin s vymezeným oborem synchronizace pomocí webu Azure portal

1. Postupujte podle [příručce Začínáme](active-directory-ds-getting-started.md) vytvořit spravovanou doménu.
2. Zvolte **obor** při výběru stylu synchronizace v Průvodci vytvořením Azure AD Domain Services.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Vytvořit nové spravované domény a povolit na základě skupin s vymezeným oborem synchronizace pomocí Powershellu
Dokončete tuto sadu kroků pomocí prostředí PowerShell. Přečtěte si pokyny k [povolit Azure Active Directory Domain Services pomocí Powershellu](active-directory-ds-enable-using-powershell.md). Několik kroků v tomto článku jsou mírně upravit tak, aby konfigurace synchronizace s vymezeným oborem.

Proveďte následující kroky konfigurace na základě skupin s vymezeným oborem synchronizace se spravovanou doménou:

1. Proveďte následující úkoly:
  * [Úloha 1: Nainstalujte požadované moduly Powershellu](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
  * [Úloha 2: Vytvoření instančního objektu požadovaná služba v adresáři služby Azure AD](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
  * [Úloha 3: Vytvořte a nakonfigurujte skupinu "Správci AAD DC"](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
  * [Úloha 4: Registrace poskytovatele prostředků služby Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
  * [Úloha 5: Vytvořte skupinu prostředků](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
  * [Krok 6: Vytvoření a konfigurace virtuální sítě](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Vyberte skupiny, které chcete synchronizovat, a zadání zobrazovaného názvu skupiny, které chcete synchronizovat vaši spravovanou doménu.

3. Uložit [skript v následujícím oddílu](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) do souboru s názvem ```Select-GroupsToSync.ps1```. Spusťte skript podobná níže uvedenému příkladu:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
  ```

  > [!WARNING]
  > **Nezapomeňte zahrnout skupinu "Správci AAD DC".**
  >
  > V seznamu skupiny nakonfigurované pro synchronizaci s vymezeným oborem musí obsahovat skupinu "Správci AAD DC". Pokud není zadána tuto skupinu, budou spravované domény nepoužitelné.
  >

4. Teď vytvořte spravovanou doménu a povolte na základě skupin s vymezeným oborem synchronizace pro spravovanou doménu. Zahrnout vlastnost ```"filteredSync" = "Enabled"``` v ```Properties``` parametru. Pro instanci, najdete v následující fragment skriptu zkopírovanými z [7 úloh: Zřízení spravované doméně služby Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

  ```powershell
  $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
  $ManagedDomainName = "contoso100.com"
  $ResourceGroupName = "ContosoAaddsRg"
  $VnetName = "DomainServicesVNet_WUS"
  $AzureLocation = "westus"

  # Enable Azure AD Domain Services for the directory.
  New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
  ```

  > [!TIP]
  > Nezapomeňte zahrnout ```"filteredSync" = "Enabled"``` v ```-Properties``` parametrů, takže s vymezeným oborem synchronizace je povolena pro spravovanou doménu.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skript a vyberte skupiny, které chcete synchronizovat do spravované domény (Select-GroupsToSync.ps1)
Uložte následující skript do souboru (```Select-GroupsToSync.ps1```). Tento skript nakonfiguruje synchronizace vybraných skupin ke spravované doméně Azure AD Domain Services. Všechny uživatelské účty, které patří do zadané skupiny se budou synchronizovat na spravované doméně.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Upravit vymezené synchronizace podle skupin
Chcete-li upravit seznam skupin uživatelů mají být synchronizovány s vaší spravované domény, znovu spusťte [skript prostředí PowerShell](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) a zadejte nový seznam skupin. Nezapomeňte si vždy zadejte skupinu "Správci AAD DC' v tomto seznamu.

> [!WARNING]
> **Nezapomeňte zahrnout skupinu "Správci AAD DC".**
>
> V seznamu skupiny nakonfigurované pro synchronizaci s vymezeným oborem musí obsahovat skupinu "Správci AAD DC". Pokud není zadána tuto skupinu, budou spravované domény nepoužitelné.
>


## <a name="disable-group-based-scoped-synchronization"></a>Zakažte na základě skupin synchronizace s vymezeným oborem
Použijte následující skript Powershellu pro zakázání skupinové nastavit rozsah synchronizace vaší spravované domény:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Další postup
* [Principy synchronizace ve službě Azure AD Domain Services](active-directory-ds-synchronization.md)
* [Povolit Azure Active Directory Domain Services pomocí Powershellu](active-directory-ds-enable-using-powershell.md)
