---
title: Vymezená synchronizace pomocí prostředí PowerShell pro Azure AD Domain Services | Microsoft Docs
description: Naučte se používat Azure AD PowerShell ke konfiguraci synchronizace s vymezeným oborem z Azure AD do spravované domény Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: justinha
ms.openlocfilehash: 04c611b8a902d27f40893a05f301898c0111748f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660945"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Konfigurace vymezené synchronizace z Azure AD na Azure Active Directory Domain Services pomocí Azure AD PowerShellu

Pro poskytování ověřovacích služeb Azure Active Directory Domain Services (Azure služba AD DS) synchronizuje uživatele a skupiny z Azure AD. V hybridním prostředí je možné nejdřív synchronizovat uživatele a skupiny z místního prostředí Active Directory Domain Services (služba AD DS) do Azure AD pomocí Azure AD Connect a pak je synchronizovat s Azure služba AD DS.

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény Azure služba AD DS. Pokud máte konkrétní potřeby, můžete místo toho synchronizovat jenom definovanou skupinu uživatelů.

V tomto článku se dozvíte, jak vytvořit spravovanou doménu používající synchronizaci s vymezeným oborem, a potom změnit nebo zakázat sadu uživatelů s vymezeným oborem pomocí Azure AD PowerShellu. [Tyto kroky můžete provést také pomocí Azure Portal][scoped-sync].

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][tutorial-create-instance].
* Ke změně oboru synchronizace služby Azure služba AD DS potřebujete oprávnění *globálního správce* v tenantovi Azure AD.

## <a name="scoped-synchronization-overview"></a>Přehled vymezené synchronizace

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény. Pokud k spravované doméně potřebuje jenom několik uživatelů, můžete synchronizovat jenom tyto uživatelské účty. Tato synchronizace s vymezeným oborem je založena na skupině. Když nakonfigurujete synchronizaci s rozsahem na základě skupin, budou se do spravované domény synchronizovat jenom uživatelské účty patřící do zadaných skupin. Vnořené skupiny se nesynchronizují, pouze konkrétní skupiny, které vyberete.

Obor synchronizace můžete změnit před nebo po vytvoření spravované domény. Rozsah synchronizace je definován instančním objektem s identifikátorem aplikace 2565bd9d-DA50-47d4-8B85-4c97f669dc36. Chcete-li zabránit ztrátě rozsahu, neodstraňujte ani neměňte instanční objekt. Pokud je omylem odstraněn, nelze obnovit rozsah synchronizace. 

Při změně rozsahu synchronizace Pamatujte na následující upozornění:

- Dojde k úplné synchronizaci.
- Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny. Ve spravované doméně se vytvoří nové objekty.

Další informace o procesu synchronizace najdete [v tématu pochopení synchronizace v Azure AD Domain Services][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShellový skript pro vymezenou synchronizaci

Pokud chcete nakonfigurovat vymezenou synchronizaci pomocí PowerShellu, nejdřív uložte následující skript do souboru s názvem `Select-GroupsToSync.ps1` .

Tento skript nakonfiguruje Azure služba AD DS k synchronizaci vybraných skupin z Azure AD. Všechny uživatelské účty, které jsou součástí zadaných skupin, se synchronizují se spravovanými doménami.

Tento skript se používá v dalších krocích v tomto článku.

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

## <a name="enable-scoped-synchronization"></a>Povolit vymezenou synchronizaci

Pokud chcete povolit synchronizaci s rozsahem na základě skupin pro spravovanou doménu, proveďte následující kroky:

1. Nejdřív nastavte *"filteredSync" = "Enabled"* na prostředku Azure služba AD DS a pak aktualizujte spravovanou doménu.

    Po zobrazení výzvy zadejte přihlašovací údaje *globálního správce* pro přihlášení k vašemu TENANTOVI Azure AD pomocí rutiny [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Nyní zadejte seznam skupin, jejichž uživatelé mají být synchronizováni do spravované domény.

    Spusťte `Select-GroupsToSync.ps1` skript a zadejte seznam skupin, které se mají synchronizovat. V následujícím příkladu jsou skupiny, které se mají synchronizovat, *GroupName1* a *GroupName2*.

    > [!WARNING]
    > V seznamu skupin pro účely vymezené synchronizace musíte zahrnout skupinu *správců řadiče domény AAD* . Pokud tuto skupinu nezadáte, je spravovaná doména nepoužitelná.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="modify-scoped-synchronization"></a>Upravit vymezenou synchronizaci

Chcete-li upravit seznam skupin, jejichž uživatelé mají být synchronizováni do spravované domény, spusťte `Select-GroupsToSync.ps1` skript a zadejte nový seznam skupin pro synchronizaci.

V následujícím příkladu skupiny pro synchronizaci již nezahrnují *GroupName2* a teď obsahují *GroupName3*.

> [!WARNING]
> V seznamu skupin pro účely vymezené synchronizace musíte zahrnout skupinu *správců řadiče domény AAD* . Pokud tuto skupinu nezadáte, je spravovaná doména nepoužitelná.

Po zobrazení výzvy zadejte přihlašovací údaje *globálního správce* pro přihlášení k vašemu TENANTOVI Azure AD pomocí rutiny [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="disable-scoped-synchronization"></a>Zakázat synchronizaci s vymezeným oborem

Pokud chcete zakázat synchronizaci s rozsahem na základě skupin pro spravovanou doménu, nastavte v prostředku Azure služba AD DS *filteredSync = disabled* a pak aktualizujte spravovanou doménu. Po dokončení bude pro všechny uživatele a skupiny nastavená synchronizace z Azure AD.

Po zobrazení výzvy zadejte přihlašovací údaje *globálního správce* pro přihlášení k vašemu TENANTOVI Azure AD pomocí rutiny [Connect-AzureAD][Connect-AzureAD] :

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="next-steps"></a>Další kroky

Další informace o procesu synchronizace najdete [v tématu pochopení synchronizace v Azure AD Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
