---
title: Synchronizace s vymezeným oborem pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat synchronizaci s oborem z Azure AD do spravované domény Služby Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 9ef7e14cc2a290cc5583e3e599e278f98882152c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654744"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurace synchronizace s vymezeným oborem z Azure AD na Azure Active Directory Domain Services

K poskytování ověřovacích služeb synchronizuje služba Azure Active Directory Domain Services (Azure AD DS) uživatele a skupiny z Azure AD. V hybridním prostředí mohou uživatelé a skupiny z místního prostředí služby Active Directory Domain Services (AD DS) nejprve synchronizovat do Služby Azure AD pomocí služby Azure AD Connect a potom synchronizovat do služby Azure AD DS.

Ve výchozím nastavení jsou všichni uživatelé a skupiny z adresáře Azure AD synchronizováni do spravované domény Služby Azure AD DS. Pokud máte specifické potřeby, můžete místo toho zvolit synchronizaci pouze definované sady uživatelů.

Tento článek ukazuje, jak vytvořit spravovanou doménu Azure AD DS, která používá synchronizaci s vymezeným oborem a potom změnit nebo zakázat sadu uživatelů s vymezeným oborem.

## <a name="scoped-synchronization-overview"></a>Přehled synchronizace s vymezenou masousem

Ve výchozím nastavení jsou všichni uživatelé a skupiny z adresáře Azure AD synchronizováni do spravované domény Služby Azure AD DS. Pokud ke spravované doméně potřebuje přístup pouze několik uživatelů, můžete synchronizovat pouze tyto uživatelské účty. Tato synchronizace s vymezeným oborem je založena na skupině. Při konfiguraci synchronizace s oborem na základě skupiny jsou synchronizovány se spravovanou doménou Azure AD DS pouze uživatelské účty, které patří do zadaných skupin.

Následující tabulka popisuje použití synchronizace s vymezenou oborem:

| Aktuální stav | Požadovaný stav | Požadovaná konfigurace |
| --- | --- | --- |
| Existující spravovaná doména je nakonfigurována tak, aby synchronizovala všechny uživatelské účty a skupiny. | Chcete synchronizovat pouze uživatelské účty, které patří do určitých skupin. | Nelze změnit synchronizaci všech uživatelů na použití synchronizace s vymezenou oborem. [Odstraňte existující spravovanou doménu](delete-aadds.md)a podle pokynů v tomto článku znovu vytvořte spravovanou doménu Služby Azure AD DS s nakonfigurovanou synchronizací s vymezeným oborem. |
| Žádná existující spravovaná doména. | Chcete vytvořit novou spravovanou doménu a synchronizovat pouze uživatelské účty patřící do určitých skupin. | Podle kroků v tomto článku vytvořte spravovanou doménu Azure AD DS s nakonfigurovanou synchronizací s vymezeným oborem. |
| Existující spravovaná doména je nakonfigurována tak, aby synchronizovala pouze účty, které patří do určitých skupin. | Chcete upravit seznam skupin, jejichž uživatelé by měly být synchronizovány do spravované domény Azure AD DS. | Podle pokynů v tomto článku upravte synchronizaci s vymezenou hodnotou. |

K konfiguraci nastavení synchronizace s vymezenou matou používáte portál Azure nebo PowerShell:

| Akce | | |
|--|--|--|
| Vytvoření spravované domény Azure AD DS a konfigurace synchronizace s oborem | [portál Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Změna synchronizace s oborem | [portál Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Zakázat synchronizaci s vymezením | [portál Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Změna rozsahu synchronizace způsobí, že spravovaná doména Azure AD DS znovu synchronizuje všechna data. Platí následující důležité informace:
> 
>  * Když změníte obor synchronizace pro spravovanou doménu Azure AD DS, dojde k úplné synchronizaci.
>  * Objekty, které už nejsou potřeba ve spravované doméně Azure AD DS, se odstraní. Ve spravované doméně jsou vytvořeny nové objekty.
>  * Resynchronizace může trvat dlouhou dobu k dokončení. Doba synchronizace závisí na počtu objektů, jako jsou uživatelé, skupiny a členství ve spravované doméně Azure AD DS a adresáři Azure AD. U velkých adresářů s mnoha stovkami tisíc objektů může synchronizace trvat několik dní.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Povolení synchronizace s vymezením pomocí portálu Azure

Chcete-li povolit synchronizaci s vymezením oborů na webu Azure Portal, proveďte následující kroky:

1. Podle [kurzu vytvořte a nakonfigurujte instanci Azure AD DS](tutorial-create-instance-advanced.md). Dokončete všechny požadavky a kroky nasazení kromě oboru synchronizace.
1. V kroku synchronizace zvolte **Obor** a vyberte skupiny Azure AD, které chcete synchronizovat s instancí Azure AD DS.

Spravovaná doména Azure AD DS může trvat až hodinu k dokončení nasazení. Na webu Azure Portal stránka **Přehled** pro spravovanou doménu Azure AD DS zobrazuje aktuální stav v této fázi nasazení.

Když portál Azure ukazuje, že spravovaná doména Azure AD DS dokončila zřizování, je třeba dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Chcete-li nakonfigurovat službu DNS, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel do služby Azure AD Domain Services,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Úprava synchronizace s vymezením pomocí portálu Azure

Chcete-li upravit seznam skupin, jejichž uživatelé by měli být synchronizováni se spravovanou doménou Služby Azure AD DS, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **služby Domény Azure AD**. Zvolte svou instanci, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **Možnost Synchronizace.**
1. Chcete-li přidat skupinu, zvolte **+ Vybrat skupiny** nahoře a pak zvolte skupiny, které chcete přidat.
1. Chcete-li skupinu odebrat z oboru synchronizace, vyberte ji ze seznamu aktuálně synchronizovaných skupin a zvolte **Odebrat skupiny**.
1. Po dokončení všech změn vyberte **možnost Uložit obor synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména Azure AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně Azure AD DS jsou odstraněny a resynchronizace může trvat dlouhou dobu k dokončení.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Zakázání synchronizace s vymezením pomocí portálu Azure

Chcete-li zakázat synchronizaci s oborem azure a ds na základě skupiny pro spravovanou doménu Služby Azure AD DS, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **služby Domény Azure AD**. Zvolte svou instanci, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **Možnost Synchronizace.**
1. Nastavte obor synchronizace z **Obor na** **Vše**a pak vyberte Uložit **obor synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména Azure AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně Azure AD DS jsou odstraněny a resynchronizace může trvat dlouhou dobu k dokončení.

## <a name="powershell-script-for-scoped-synchronization"></a>Skript prostředí PowerShell pro synchronizaci s vymezením

Chcete-li nakonfigurovat synchronizaci s vymezeným oborem pomocí `Select-GroupsToSync.ps1`prostředí PowerShell, nejprve uložte následující skript do souboru s názvem . Tento skript konfiguruje Azure AD DS pro synchronizaci vybraných skupin z Azure AD. Všechny uživatelské účty, které jsou součástí zadané skupiny jsou synchronizovány do spravované domény Azure AD DS.

Tento skript se používá v dalšíkroky v tomto článku.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Povolení synchronizace s vymezením pomocí Prostředí PowerShell

Pomocí prostředí PowerShell proveďte následující sadu kroků. V pokynech k [povolení služby Azure Active Directory Domain Services pomocí prostředí PowerShell](powershell-create-instance.md). Několik kroků v tomto článku jsou mírně upraveny ke konfiguraci synchronizace s vymezeným oborem.

1. Dokončete následující úkoly z článku a povolte Azure AD DS pomocí PowerShellu. Zastavte se u kroku a skutečně vytvořte spravovanou doménu. Nakonfigurujete synchronizaci s vymezeným oborem, kterou vytvoříte spravovanou doménu Azure AD DS.

   * [Nainstalujte požadované moduly prostředí PowerShell](powershell-create-instance.md#prerequisites).
   * [Vytvořte požadovaný instanční objekt a skupinu Azure AD pro přístup pro správu](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Vytvořte podpůrné prostředky Azure, jako je virtuální síť a podsítě](powershell-create-instance.md#create-supporting-azure-resources).

1. Určete skupiny a uživatele, které obsahují, které chcete synchronizovat z Azure AD. Vytvořte seznam zobrazovaných názvů skupin, které mají být synchronizovány s Azure AD DS.

1. Spusťte [skript z předchozí části](#powershell-script-for-scoped-synchronization) a pomocí parametru *-groupsToAdd* předajte seznam skupin pro synchronizaci.

   > [!WARNING]
   > Skupinu *Správci řadiče domény aadu AAD* je nutné zahrnout do seznamu skupin pro synchronizaci s vymezeným oborem. Pokud tuto skupinu nezahrnete, bude spravovaná doména Azure AD DS nepoužitelná.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Teď vytvořte spravovanou doménu Azure AD DS a povolte synchronizaci s oborem založenou na skupinách. Do parametru *-Properties* zahrňte *"filteredSync" = "Enabled".*

    Nastavte ID předplatného Azure a zadejte název spravované domény, například *aaddscontoso.com*. ID předplatného můžete získat pomocí rutiny [Get-AzSubscription.][Get-AzSubscription] Nastavte název skupiny prostředků, název virtuální sítě a oblast na hodnoty použité v předchozích krocích k vytvoření podpůrných prostředků Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Vytvoření prostředku a vrácení ovládacího prvku do výzvy prostředí PowerShell trvá několik minut. Spravovaná doména Azure AD DS se nadále zřaží na pozadí a dokončení nasazení může trvat až hodinu. Na webu Azure Portal stránka **Přehled** pro spravovanou doménu Azure AD DS zobrazuje aktuální stav v této fázi nasazení.

Když portál Azure ukazuje, že spravovaná doména Azure AD DS dokončila zřizování, je třeba dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Chcete-li nakonfigurovat službu DNS, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatické konfiguraci těchto nastavení DNS.
* Pokud jste vytvořili spravovanou doménu Azure AD DS v oblasti, která podporuje zóny dostupnosti, vytvořte skupinu zabezpečení sítě, která omezí provoz ve virtuální síti pro spravovanou doménu Azure AD DS. Azure standardní vyrovnávání zatížení je vytvořen, který vyžaduje, aby tato pravidla být místo. Tato skupina zabezpečení sítě zabezpečuje Azure AD DS a je vyžadována pro spravovanou doménu pracovat správně.
    * Pokud chcete vytvořit skupinu zabezpečení sítě a požadovaná pravidla, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatickému vytvoření a konfiguraci skupiny zabezpečení sítě.
* [Povolte synchronizaci hesel do služby Azure AD Domain Services,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="modify-scoped-synchronization-using-powershell"></a>Úprava synchronizace s oborem pomocí Prostředí PowerShell

Chcete-li upravit seznam skupin, jejichž uživatelé by měly být synchronizovány do spravované domény Azure AD DS, znovu spusťte [skript Prostředí PowerShell](#powershell-script-for-scoped-synchronization) a zadejte nový seznam skupin. V následujícím příkladu skupiny k synchronizaci již nezahrnuje *GroupName2*a nyní obsahuje *GroupName3*.

> [!WARNING]
> Skupinu *Správci řadiče domény aadu AAD* je nutné zahrnout do seznamu skupin pro synchronizaci s vymezeným oborem. Pokud tuto skupinu nezahrnete, bude spravovaná doména Azure AD DS nepoužitelná.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Změna rozsahu synchronizace způsobí, že spravovaná doména Azure AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně Azure AD DS jsou odstraněny a resynchronizace může trvat dlouhou dobu k dokončení.

## <a name="disable-scoped-synchronization-using-powershell"></a>Zakázání synchronizace s oborem pomocí prostředí PowerShell

Chcete-li zakázat synchronizaci s oborem na základě skupiny pro spravovanou doménu Azure AD DS, nastavte *"filteredSync" = "Disabled"* na prostředku Azure AD DS a aktualizujte spravovanou doménu. Po dokončení jsou všichni uživatelé a skupiny nastaveny na synchronizaci z Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Změna rozsahu synchronizace způsobí, že spravovaná doména Azure AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně Azure AD DS jsou odstraněny a resynchronizace může trvat dlouhou dobu k dokončení.

## <a name="next-steps"></a>Další kroky

Další informace o procesu synchronizace najdete v [tématu Principy synchronizace ve službě Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
