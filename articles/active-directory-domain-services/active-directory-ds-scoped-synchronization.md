---
title: 'Azure Active Directory Domain Services: Obor synchronizace | Dokumentace Microsoftu'
description: Konfigurace synchronizace s vymezeným oborem z Azure AD do spravované domény
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: maheshu
ms.openlocfilehash: 1f7e07880c22ec6975f93d8349343986716b9445
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46499213"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurace vymezených synchronizace z Azure AD do spravované domény
V tomto článku se dozvíte, jak nakonfigurovat pouze konkrétní uživatelské účty, které se mají synchronizovat z adresáře služby Azure AD do spravované domény služby Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Na základě skupin synchronizace s vymezeným oborem
Ve výchozím nastavení všichni uživatelé a skupiny v adresáři služby Azure AD jsou synchronizovány do spravované domény. Pokud spravovanou doménu je používán pouze několik uživatelů, můžete chtít raději synchronizovat pouze uživatelské účty do spravované domény. Na základě skupin s vymezeným oborem synchronizace umožňuje udělat. Při konfiguraci, jsou synchronizovány pouze uživatelské účty, které patří do skupiny, které jste zadali do spravované domény.


## <a name="get-started-install-the-required-powershell-modules"></a>Začínáme: nainstalujte požadované moduly prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure AD PowerShell a připojte se k Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure PowerShell a připojte se ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).



## <a name="enable-group-based-scoped-synchronization"></a>Povolit synchronizaci s vymezeným oborem se na základě skupin
Proveďte následující kroky konfigurace na základě skupin s vymezeným oborem synchronizace se spravovanou doménou:

1. Vyberte skupiny, které chcete synchronizovat, a zadání zobrazovaného názvu skupiny, které chcete synchronizovat vaši spravovanou doménu.

2. Uložte skript v části do souboru s názvem ```Select-GroupsToSync.ps1```. Spusťte skript podobná níže uvedenému příkladu:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @(“GroupName1”, “GroupName2”)
  ```

3. Nyní umožňují na základě skupiny v oboru synchronizace pro spravovanou doménu.

  ```powershell
  // Login to your Azure AD tenant
  Login-AzureRmAccount

  // Retrieve the Azure AD Domain Services resource.
  $DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

  // Enable group-based scoped synchronization.
  $enableScopedSync = @{"filteredSync" = "Enabled"}

  Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
  ```

## <a name="disable-group-based-scoped-synchronization"></a>Zakažte na základě skupin synchronizace s vymezeným oborem
Použijte následující skript Powershellu pro zakázání skupinové nastavit rozsah synchronizace vaší spravované domény:

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

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
        Write-Error "Exception occured assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="next-steps"></a>Další postup
* [Principy synchronizace ve službě Azure AD Domain Services](active-directory-ds-synchronization.md)
