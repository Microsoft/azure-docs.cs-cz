---
title: 'Azure Active Directory Domain Services: Synchronizace s vymezeným oborem | Microsoft Docs'
description: Konfigurace vymezené synchronizace z Azure AD do spravovaných domén
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7d3bd8c6c62c0b8a1be6203e426337fcee7d2126
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617116"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurace vymezené synchronizace z Azure AD do spravované domény
V tomto článku se dozvíte, jak nakonfigurovat jenom konkrétní uživatelské účty, které se mají synchronizovat z adresáře Azure AD, do spravované domény Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Synchronizace s rozsahem na základě skupin
Ve výchozím nastavení se všechny uživatele a skupiny v adresáři služby Azure AD synchronizují do vaší spravované domény. Pokud spravovaná doména používá jenom několik uživatelů, můžete synchronizovat jenom tyto uživatelské účty. Synchronizace vymezená na základě skupin vám to umožňuje. Po nakonfigurování budou do spravované domény synchronizovány pouze uživatelské účty patřící do skupin, které jste zadali.

Následující tabulka vám pomůže určit, jak se má použít vymezená synchronizace:

| **Aktuální stav** | **Požadovaný stav** | **Požadovaná konfigurace** |
| --- | --- | --- |
| Vaše stávající spravovaná doména je nakonfigurovaná pro synchronizaci všech uživatelských účtů a skupin. | Chcete synchronizovat jenom uživatelské účty patřící do konkrétních skupin do spravované domény. | [Odstraňte existující spravovanou doménu](delete-aadds.md). Pak postupujte podle pokynů v tomto článku, abyste ho znovu vytvořili s nakonfigurovanou synchronizací s vymezeným oborem. |
| Nemáte existující spravovanou doménu. | Chcete vytvořit novou spravovanou doménu a synchronizovat jenom uživatelské účty patřící do konkrétních skupin. | Podle pokynů v tomto článku vytvořte novou spravovanou doménu s nakonfigurovanou synchronizací s vymezeným oborem. |
| Vaše stávající spravovaná doména je nakonfigurovaná tak, aby synchronizoval jenom účty patřící do konkrétních skupin. | Chcete upravit seznam skupin, jejichž uživatelé se mají synchronizovat s doménou Správa. | Podle pokynů v tomto článku můžete upravit vymezenou synchronizaci. |

> [!WARNING]
> **Změna rozsahu synchronizace způsobí, že vaše spravovaná doména projde opětovnou synchronizací.**
> 
>  * Když změníte rozsah synchronizace pro spravovanou doménu, dojde k úplné opakované synchronizaci.
>  * Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny. Ve spravované doméně se vytvoří nové objekty.
>  * Opakovaná synchronizace může trvat dlouhou dobu v závislosti na počtu objektů (uživatelů, skupin a členství ve skupinách) ve vaší spravované doméně a adresáři služby Azure AD. Pro velké adresáře s mnoha stovkami tisíc objektů může opětovná synchronizace trvat několik dní.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Vytvořit novou spravovanou doménu a povolit synchronizaci vymezenou podle skupin pomocí Azure Portal

1. Použijte [průvodce Začínáme](tutorial-create-instance.md) k vytvoření spravované domény.
2. Vyberte **vymezený rozsah** během výběru stylu synchronizace v Průvodci vytvořením Azure AD Domain Services.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Vytvoření nové spravované domény a povolení synchronizace s rozsahem na základě skupin pomocí PowerShellu
K dokončení této sady kroků použijte PowerShell. Informace o [povolení Azure Active Directory Domain Services pomocí PowerShellu](powershell-create-instance.md)najdete v pokynech. Několik kroků v tomto článku se mírně změnila a konfiguruje se synchronizace s vymezeným oborem.

Provedením následujících kroků nakonfigurujte synchronizaci s rozsahem na základě skupin do spravované domény:

1. Proveďte následující úlohy:
   * [Úloha 1: Nainstalujte požadované moduly](powershell-create-instance.md#task-1-install-the-required-powershell-modules)prostředí PowerShell.
   * [Úkol 2: Vytvořte požadovaný instanční objekt v adresáři](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)služby Azure AD.
   * [Úloha 3: Vytvořte a nakonfigurujte skupinu ' AAD DC Administrators '] PowerShell-Create-instance. MD # Task-3-Create-a-Configure-the-AAD-DC-Administrators-Group).
   * [Úloha 4: Zaregistrujte poskytovatele](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)prostředků Azure AD Domain Services.
   * [5. úkol: Vytvořte skupinu](powershell-create-instance.md#task-5-create-a-resource-group)prostředků.
   * [Úloha 6: Vytvořte a nakonfigurujte virtuální síť](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network).

2. Vyberte skupiny, které chcete synchronizovat, a zadejte zobrazované názvy skupin, které chcete synchronizovat do spravované domény.

3. Uložte [skript v následující části](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) do souboru s názvem ```Select-GroupsToSync.ps1```. Spusťte skript podobný následujícímu:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Nezapomeňte zahrnout skupinu ' AAD DC Administrators '.**
   >
   > Do seznamu skupin nakonfigurovaných pro vymezenou synchronizaci musíte zahrnout skupinu ' AAD DC Administrators '. Pokud tuto skupinu nezahrnete, spravovaná doména nebude použitelná.
   >

4. Nyní vytvořte spravovanou doménu a povolte synchronizaci s vymezeným rozsahem na základě skupin pro spravovanou doménu. Zahrňte vlastnost ```"filteredSync" = "Enabled"``` ```Properties``` do parametru. Například se podívejte na následující fragment skriptu zkopírovaný z [úlohy 7: Zřídí Azure AD Domain Services spravovanou doménu](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
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
   > Nezapomeňte do ```"filteredSync" = "Enabled"``` ```-Properties``` parametru zahrnout, takže je pro spravovanou doménu povolená synchronizace s vymezeným oborem.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skript pro výběr skupin pro synchronizaci do spravované domény (Select-GroupsToSync. ps1)
Uložte následující skript do souboru (```Select-GroupsToSync.ps1```). Tento skript nakonfiguruje Azure AD Domain Services k synchronizaci vybraných skupin do spravované domény. Všechny uživatelské účty patřící do zadaných skupin budou synchronizovány do spravované domény.

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


## <a name="modify-group-based-scoped-synchronization"></a>Úprava synchronizace s rozsahem na základě skupin
Chcete-li upravit seznam skupin, jejichž uživatelé mají být synchronizováni do spravované domény, spusťte znovu [skript prostředí PowerShell](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) a zadejte nový seznam skupin. Nezapomeňte v tomto seznamu vždycky zadat skupinu "AAD DC Administrators".

> [!WARNING]
> **Nezapomeňte zahrnout skupinu ' AAD DC Administrators '.**
>
> Do seznamu skupin nakonfigurovaných pro vymezenou synchronizaci musíte zahrnout skupinu ' AAD DC Administrators '. Pokud tuto skupinu nezahrnete, spravovaná doména nebude použitelná.
>


## <a name="disable-group-based-scoped-synchronization"></a>Zakázat synchronizaci s rozsahem na základě skupin
Pomocí následujícího skriptu PowerShellu zakažte synchronizaci s rozsahem na základě skupin pro spravovanou doménu:

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
* [Pochopení synchronizace v Azure AD Domain Services](synchronization.md)
* [Povolení Azure Active Directory Domain Services pomocí prostředí PowerShell](powershell-create-instance.md)
