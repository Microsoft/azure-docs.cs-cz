---
title: Vymezená synchronizace pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat vymezenou synchronizaci z Azure AD do spravované domény Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/06/2019
ms.author: iainfou
ms.openlocfilehash: 5fe19d3800883782187ae15c0a6fc0cd9709f0e9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842684"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurace vymezené synchronizace z Azure AD na Azure Active Directory Domain Services

Pro poskytování ověřovacích služeb Azure Active Directory Domain Services (Azure služba AD DS) synchronizuje uživatele a skupiny z Azure AD. V hybridním prostředí je možné nejdřív synchronizovat uživatele a skupiny z místního prostředí Active Directory Domain Services (služba AD DS) do Azure AD pomocí Azure AD Connect a pak je synchronizovat s Azure služba AD DS. Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény Azure služba AD DS. Pokud máte konkrétní potřeby, můžete místo toho synchronizovat jenom definovanou skupinu uživatelů.

V tomto článku se dozvíte, jak vytvořit spravovanou doménu služba AD DS Azure, která používá vymezenou synchronizaci, a pak změnit nebo zakázat sadu uživatelů s vymezeným oborem.

## <a name="scoped-synchronization-overview"></a>Přehled vymezené synchronizace

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény Azure služba AD DS. Pokud k spravované doméně potřebuje jenom několik uživatelů, můžete synchronizovat jenom tyto uživatelské účty. Tato synchronizace s vymezeným oborem je založena na skupině. Když nakonfigurujete synchronizaci s rozsahem na základě skupin, budou se do spravované domény služba AD DS Azure synchronizovat jenom uživatelské účty patřící do zadaných skupin.

Následující tabulka popisuje, jak použít vymezenou synchronizaci:

| Aktuální stav | Požadovaný stav | Požadovaná konfigurace |
| --- | --- | --- |
| Stávající spravovaná doména je nakonfigurovaná pro synchronizaci všech uživatelských účtů a skupin. | Chcete synchronizovat pouze uživatelské účty, které patří do konkrétních skupin. | Nemůžete změnit synchronizaci všech uživatelů, aby používali vymezenou synchronizaci. [Odstraňte existující spravovanou doménu](delete-aadds.md)a pak postupujte podle kroků v tomto článku a znovu vytvořte spravovanou doménu služby Azure služba AD DS s nakonfigurovanou synchronizací s vymezeným oborem. |
| Neexistuje žádná existující spravovaná doména. | Chcete vytvořit novou spravovanou doménu a synchronizovat jenom uživatelské účty patřící do konkrétních skupin. | Podle pokynů v tomto článku vytvořte spravovanou doménu Azure služba AD DS s nakonfigurovanou synchronizací s vymezeným oborem. |
| Stávající spravovaná doména je nakonfigurovaná tak, aby synchronizoval jenom účty, které patří do konkrétních skupin. | Chcete upravit seznam skupin, jejichž uživatelé by se měli synchronizovat do spravované domény Azure služba AD DS. | Podle kroků v tomto článku můžete upravit vymezenou synchronizaci. |

Pomocí Azure Portal nebo PowerShellu můžete nakonfigurovat vymezená nastavení synchronizace:

| Action | | |
|--|--|--|
| Vytvoření spravované domény Azure služba AD DS a konfigurace vymezené synchronizace | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Upravit vymezenou synchronizaci | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Zakázat synchronizaci s vymezeným oborem | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Změna rozsahu synchronizace způsobí, že doména spravovaná službou Azure služba AD DS znovu synchronizuje všechna data.
> 
>  * Když změníte rozsah synchronizace pro spravovanou doménu Azure služba AD DS, dojde k úplné opakované synchronizaci.
>  * Objekty, které již nejsou vyžadovány ve spravované doméně Azure služba AD DS, jsou odstraněny. Ve spravované doméně se vytvoří nové objekty.
>  * Dokončení opakované synchronizace může trvat dlouhou dobu. Čas synchronizace závisí na počtu objektů, jako jsou uživatelé, skupiny a členství ve skupinách, ve spravované doméně Azure služba AD DS a adresáři služby Azure AD. Pro velké adresáře s mnoha stovkami tisíc objektů může opětovná synchronizace trvat několik dní.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Povolit vymezenou synchronizaci pomocí Azure Portal

1. Postup [Vytvoření a konfigurace instance Azure služba AD DS](tutorial-create-instance.md)najdete v tomto kurzu. Dokončete všechny požadavky a kroky nasazení kromě oboru synchronizace.
1. Zvolte **vymezený** v kroku synchronizace a pak vyberte skupiny Azure AD, které chcete synchronizovat s instancí Azure služba AD DS.

Pro dokončení nasazení může trvat až hodinu, než se spravovaná doména Azure služba AD DS. Na stránce s **přehledem** pro spravovanou doménu Azure služba AD DS se Azure Portal v rámci této fáze nasazení zobrazuje aktuální stav.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény Azure služba AD DS, je nutné provést následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte na portálu spravovanou doménu Azure služba AD DS. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Úprava rozsahu synchronizace pomocí Azure Portal

Pokud chcete upravit seznam skupin, jejichž uživatelé by se měli synchronizovat do spravované domény Azure služba AD DS, proveďte následující kroky:

1. V Azure Portal vyberte svou instanci Azure služba AD DS, například *contoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Chcete-li přidat skupinu, zvolte možnost **+ Vybrat skupiny** v horní části a pak zvolte skupiny, které chcete přidat.
1. Chcete-li odebrat skupinu z oboru synchronizace, vyberte ji ze seznamu aktuálně synchronizovaných skupin a zvolte možnost **odebrat skupiny**.
1. Po provedení všech změn vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že doména spravovaná službou Azure služba AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně služby Azure služba AD DS, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Zakázat vymezenou synchronizaci pomocí Azure Portal

Pokud chcete zakázat synchronizaci s rozsahem na základě skupin pro spravovanou doménu Azure služba AD DS, proveďte následující kroky:

1. V Azure Portal vyberte svou instanci Azure služba AD DS, například *contoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Nastavte obor synchronizace z **oboru** na **vše**a pak vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že doména spravovaná službou Azure služba AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně služby Azure služba AD DS, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShellový skript pro vymezenou synchronizaci

Pokud chcete nakonfigurovat vymezenou synchronizaci pomocí PowerShellu, nejdřív uložte následující skript do souboru s `Select-GroupsToSync.ps1`názvem. Tento skript nakonfiguruje Azure služba AD DS k synchronizaci vybraných skupin z Azure AD. Všechny uživatelské účty, které jsou součástí zadaných skupin, se synchronizují do spravované domény Azure služba AD DS.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Povolit vymezenou synchronizaci pomocí PowerShellu

K dokončení této sady kroků použijte PowerShell. Informace o [povolení Azure Active Directory Domain Services pomocí PowerShellu](powershell-create-instance.md)najdete v pokynech. Několik kroků v tomto článku se mírně změnila a konfiguruje se synchronizace s vymezeným oborem.

1. Provedením následujících kroků v článku povolte službu Azure služba AD DS pomocí prostředí PowerShell. Zastavte si krok, ve kterém se vytvoří spravovaná doména. Nakonfigurujete synchronizaci s vymezeným oborem, kterou vytvoříte ve spravované doméně Azure služba AD DS.

   * [Nainstalujte požadované moduly prostředí PowerShell](powershell-create-instance.md#prerequisites).
   * [Vytvořte požadovaný instanční objekt a skupinu služby Azure AD pro přístup pro správu](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Vytvořte podpůrné prostředky Azure, jako jsou virtuální sítě a podsítě](powershell-create-instance.md#create-supporting-azure-resources).

1. Určete skupiny a uživatele, které obsahují, které chcete synchronizovat z Azure AD. Vytvořte seznam zobrazovaných názvů skupin, které se budou synchronizovat do Azure služba AD DS.

1. Spusťte [skript z předchozí části](#powershell-script-for-scoped-synchronization) a pomocí parametru *-groupsToAdd* předejte seznam skupin, které se mají synchronizovat.

   > [!WARNING]
   > V seznamu skupin pro účely vymezené synchronizace musíte zahrnout skupinu *správců řadiče domény AAD* . Pokud tuto skupinu nezadáte, je spravovaná doména Azure služba AD DS nepoužitelná.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Teď vytvořte spravovanou doménu Azure služba AD DS a povolte synchronizaci vymezenou na základě skupin. Do parametru *-Properties* zahrňte *"filteredSync" = "Enabled* ".

    Nastavte ID vašeho předplatného Azure a potom zadejte název spravované domény, třeba *contoso.com*. ID vašeho předplatného můžete získat pomocí rutiny [Get-AzSubscription][Get-AzSubscription] . Nastavte název skupiny prostředků, název virtuální sítě a oblast na hodnoty použité v předchozích krocích k vytvoření podpůrných prostředků Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
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

Vytvoření prostředku a vrácení řízení do příkazového řádku PowerShellu trvá několik minut. Spravovaná doména Azure služba AD DS je nadále zřízena na pozadí a může trvat až hodinu, než se nasazení dokončí. Na stránce s **přehledem** pro spravovanou doménu Azure služba AD DS se Azure Portal v rámci této fáze nasazení zobrazuje aktuální stav.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény Azure služba AD DS, je nutné provést následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte na portálu spravovanou doménu Azure služba AD DS. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="modify-scoped-synchronization-using-powershell"></a>Úprava oboru synchronizace pomocí PowerShellu

Pokud chcete upravit seznam skupin, jejichž uživatelé se mají synchronizovat do spravované domény Azure služba AD DS, spusťte znovu [skript PowerShellu](#powershell-script-for-scoped-synchronization) a zadejte nový seznam skupin. V následujícím příkladu skupiny pro synchronizaci již nezahrnují *GroupName2*a teď obsahují *GroupName3*.

> [!WARNING]
> V seznamu skupin pro účely vymezené synchronizace musíte zahrnout skupinu *správců řadiče domény AAD* . Pokud tuto skupinu nezadáte, je spravovaná doména Azure služba AD DS nepoužitelná.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Změna rozsahu synchronizace způsobí, že doména spravovaná službou Azure služba AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně služby Azure služba AD DS, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="disable-scoped-synchronization-using-powershell"></a>Zakázání synchronizace s vymezeným oborem pomocí PowerShellu

Pokud chcete zakázat synchronizaci s rozsahem na základě skupin pro spravovanou doménu Azure služba AD DS, nastavte v prostředku Azure služba AD DS *filteredSync = disabled* a pak aktualizujte spravovanou doménu. Po dokončení bude pro všechny uživatele a skupiny nastavená synchronizace z Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Změna rozsahu synchronizace způsobí, že doména spravovaná službou Azure služba AD DS znovu synchronizuje všechna data. Objekty, které již nejsou vyžadovány ve spravované doméně služby Azure služba AD DS, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="next-steps"></a>Další postup

Další informace o procesu synchronizace najdete [v tématu pochopení synchronizace v Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
