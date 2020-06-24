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
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 5f2c823b0932db42876be6ab04ebcd82783729aa
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734417"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurace vymezené synchronizace z Azure AD na Azure Active Directory Domain Services

Pro poskytování ověřovacích služeb Azure Active Directory Domain Services (Azure služba AD DS) synchronizuje uživatele a skupiny z Azure AD. V hybridním prostředí je možné nejdřív synchronizovat uživatele a skupiny z místního prostředí Active Directory Domain Services (služba AD DS) do Azure AD pomocí Azure AD Connect a pak je synchronizovat s Azure služba AD DS.

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény Azure služba AD DS. Pokud máte konkrétní potřeby, můžete místo toho synchronizovat jenom definovanou skupinu uživatelů.

V tomto článku se dozvíte, jak vytvořit spravovanou doménu, která používá vymezenou synchronizaci, a pak změnit nebo zakázat sadu uživatelů s vymezeným oborem.

## <a name="scoped-synchronization-overview"></a>Přehled vymezené synchronizace

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény. Pokud k spravované doméně potřebuje jenom několik uživatelů, můžete synchronizovat jenom tyto uživatelské účty. Tato synchronizace s vymezeným oborem je založena na skupině. Když nakonfigurujete synchronizaci s rozsahem na základě skupin, budou se do spravované domény synchronizovat jenom uživatelské účty patřící do zadaných skupin.

Následující tabulka popisuje, jak použít vymezenou synchronizaci:

| Aktuální stav | Požadovaný stav | Požadovaná konfigurace |
| --- | --- | --- |
| Stávající spravovaná doména je nakonfigurovaná pro synchronizaci všech uživatelských účtů a skupin. | Chcete synchronizovat pouze uživatelské účty, které patří do konkrétních skupin. | Nemůžete změnit synchronizaci všech uživatelů, aby používali vymezenou synchronizaci. [Odstraňte existující spravovanou doménu](delete-aadds.md)a pak postupujte podle pokynů v tomto článku a vytvořte znovu spravovanou doménu s nakonfigurovanou synchronizací s vymezeným oborem. |
| Neexistuje žádná existující spravovaná doména. | Chcete vytvořit novou spravovanou doménu a synchronizovat jenom uživatelské účty patřící do konkrétních skupin. | Při vytváření spravované domény s nakonfigurovanou synchronizací s vymezeným oborem použijte postup v tomto článku. |
| Stávající spravovaná doména je nakonfigurovaná tak, aby synchronizoval jenom účty, které patří do konkrétních skupin. | Chcete upravit seznam skupin, jejichž uživatelé by se měli synchronizovat do spravované domény. | Podle kroků v tomto článku můžete upravit vymezenou synchronizaci. |

Pomocí Azure Portal nebo PowerShellu můžete nakonfigurovat vymezená nastavení synchronizace:

| Akce | | |
|--|--|--|
| Vytvoření spravované domény a konfigurace vymezené synchronizace | [portál Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Upravit vymezenou synchronizaci | [portál Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Zakázat synchronizaci s vymezeným oborem | [portál Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Platí následující důležité informace:
> 
>  * Když změníte rozsah synchronizace pro spravovanou doménu, dojde k úplné opakované synchronizaci.
>  * Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny. Ve spravované doméně se vytvoří nové objekty.
>  * Dokončení opakované synchronizace může trvat dlouhou dobu. Čas synchronizace závisí na počtu objektů, jako jsou uživatelé, skupiny a členství ve skupinách ve spravované doméně a adresáři služby Azure AD. Pro velké adresáře s mnoha stovkami tisíc objektů může opětovná synchronizace trvat několik dní.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Povolit vymezenou synchronizaci pomocí Azure Portal

Pokud chcete povolit vymezenou synchronizaci v Azure Portal, proveďte následující kroky:

1. Pokud [chcete vytvořit a nakonfigurovat spravovanou doménu](tutorial-create-instance-advanced.md), postupujte podle kurzu. Dokončete všechny požadavky a kroky nasazení kromě oboru synchronizace.
1. Zvolte **vymezený** v kroku synchronizace a pak vyberte skupiny Azure AD, které chcete synchronizovat do spravované domény.

Pro dokončení nasazení může trvat až hodinu, než se spravovaná doména dokončí. V Azure Portal zobrazuje stránka **Přehled** pro spravovanou doménu aktuální stav v rámci této fáze nasazení.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény, musí se dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte spravovanou doménu na portálu. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Úprava rozsahu synchronizace pomocí Azure Portal

Chcete-li upravit seznam skupin, jejichž uživatelé mají být synchronizováni do spravované domény, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Chcete-li přidat skupinu, zvolte možnost **+ Vybrat skupiny** v horní části a pak zvolte skupiny, které chcete přidat.
1. Chcete-li odebrat skupinu z oboru synchronizace, vyberte ji ze seznamu aktuálně synchronizovaných skupin a zvolte možnost **odebrat skupiny**.
1. Po provedení všech změn vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Zakázat vymezenou synchronizaci pomocí Azure Portal

Pokud chcete zakázat synchronizaci s rozsahem na základě skupin pro spravovanou doménu, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Nastavte obor synchronizace z **oboru** na **vše**a pak vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShellový skript pro vymezenou synchronizaci

Pokud chcete nakonfigurovat vymezenou synchronizaci pomocí PowerShellu, nejdřív uložte následující skript do souboru s názvem `Select-GroupsToSync.ps1` . Tento skript nakonfiguruje Azure služba AD DS k synchronizaci vybraných skupin z Azure AD. Všechny uživatelské účty, které jsou součástí zadaných skupin, se synchronizují se spravovanými doménami.

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

Pomocí PowerShellu proveďte následující kroky. Informace o [povolení Azure Active Directory Domain Services pomocí PowerShellu](powershell-create-instance.md)najdete v pokynech. Několik kroků v tomto článku se mírně změnila a konfiguruje se synchronizace s vymezeným oborem.

1. Provedením následujících kroků v článku povolte službu Azure služba AD DS pomocí prostředí PowerShell. Zastavte si krok, ve kterém se vytvoří spravovaná doména. Nakonfigurujete synchronizaci s vymezeným oborem a vytvoříte spravovanou doménu.

   * [Nainstalujte požadované moduly prostředí PowerShell](powershell-create-instance.md#prerequisites).
   * [Vytvořte požadovaný instanční objekt a skupinu služby Azure AD pro přístup pro správu](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Vytvořte podpůrné prostředky Azure, jako jsou virtuální sítě a podsítě](powershell-create-instance.md#create-supporting-azure-resources).

1. Určete skupiny a uživatele, které obsahují, které chcete synchronizovat z Azure AD. Vytvořte seznam zobrazovaných názvů skupin, které se budou synchronizovat do Azure služba AD DS.

1. Spusťte [skript z předchozí části](#powershell-script-for-scoped-synchronization) a pomocí parametru *-groupsToAdd* předejte seznam skupin, které se mají synchronizovat.

   > [!WARNING]
   > V seznamu skupin pro účely vymezené synchronizace musíte zahrnout skupinu *správců řadiče domény AAD* . Pokud tuto skupinu nezadáte, je spravovaná doména nepoužitelná.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Teď vytvořte spravovanou doménu a povolte synchronizaci s rozsahem na základě skupin. Do parametru *-Properties* zahrňte *"filteredSync" = "Enabled* ".

    Nastavte ID vašeho předplatného Azure a potom zadejte název spravované domény, třeba *aaddscontoso.com*. ID vašeho předplatného můžete získat pomocí rutiny [Get-AzSubscription][Get-AzSubscription] . Nastavte název skupiny prostředků, název virtuální sítě a oblast na hodnoty použité v předchozích krocích k vytvoření podpůrných prostředků Azure:

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

Vytvoření prostředku a vrácení řízení do příkazového řádku PowerShellu trvá několik minut. Spravovaná doména je nadále zřízena na pozadí a může trvat až hodinu, než se nasazení dokončí. V Azure Portal zobrazuje stránka **Přehled** pro spravovanou doménu aktuální stav v rámci této fáze nasazení.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény, musí se dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte spravovanou doménu na portálu. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* Pokud jste vytvořili spravovanou doménu v oblasti, která podporuje Zóny dostupnosti, vytvořte skupinu zabezpečení sítě, která bude omezovat provoz ve virtuální síti pro spravovanou doménu. Vytvoří se standardní nástroj pro vyrovnávání zatížení Azure, který vyžaduje, aby se tato pravidla mohla umístit. Tato skupina zabezpečení sítě zabezpečuje službu Azure služba AD DS a je potřeba, aby správně fungovala spravovaná doména.
    * Pokud chcete vytvořit skupinu zabezpečení sítě a požadovaná pravidla, vyberte spravovanou doménu na portálu. V okně **Přehled** se zobrazí výzva k automatickému vytvoření a konfiguraci skupiny zabezpečení sítě.
* [Povolte synchronizaci hesel Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="modify-scoped-synchronization-using-powershell"></a>Úprava oboru synchronizace pomocí PowerShellu

Chcete-li upravit seznam skupin, jejichž uživatelé mají být synchronizováni do spravované domény, spusťte znovu [skript prostředí PowerShell](#powershell-script-for-scoped-synchronization) a zadejte nový seznam skupin. V následujícím příkladu skupiny pro synchronizaci již nezahrnují *GroupName2*a teď obsahují *GroupName3*.

> [!WARNING]
> V seznamu skupin pro účely vymezené synchronizace musíte zahrnout skupinu *správců řadiče domény AAD* . Pokud tuto skupinu nezadáte, je spravovaná doména nepoužitelná.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat dlouhou dobu.

## <a name="disable-scoped-synchronization-using-powershell"></a>Zakázání synchronizace s vymezeným oborem pomocí PowerShellu

Pokud chcete zakázat synchronizaci s rozsahem na základě skupin pro spravovanou doménu, nastavte v prostředku Azure služba AD DS *filteredSync = disabled* a pak aktualizujte spravovanou doménu. Po dokončení bude pro všechny uživatele a skupiny nastavená synchronizace z Azure AD.

```powershell
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

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
