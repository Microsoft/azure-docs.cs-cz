---
title: Přiřazení role instančního objektu virtuální plochy Windows (Classic) – Azure
description: Vytvoření instančních objektů a přiřazení rolí pomocí prostředí PowerShell ve virtuální ploše Windows (Classic).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: 91bb14a174d5bd5c16b38513825579097e1d6f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078524"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Kurz: vytvoření instančních objektů a přiřazení rolí pomocí PowerShellu ve virtuálním počítači s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Instanční objekty jsou identity, které můžete vytvořit v Azure Active Directory k přiřazení rolí a oprávnění pro konkrétní účel. Na virtuálním počítači s Windows můžete vytvořit instanční objekt pro:

- Automatizujte určité úlohy správy virtuálních počítačů s Windows.
- Použijte jako přihlašovací údaje místo uživatelů MFA – povinné při spuštění libovolné šablony Azure Resource Manager pro virtuální počítač s Windows.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte instanční objekt v Azure Active Directory.
> * Vytvořte přiřazení role na virtuálním počítači s Windows.
> * Přihlaste se k virtuálnímu počítači s Windows pomocí instančního objektu.

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit instanční objekty a přiřazení rolí, musíte udělat tři věci:

1. Nainstalujte modul AzureAD. Pokud chcete modul nainstalovat, spusťte PowerShell jako správce a spusťte následující rutinu:

    ```powershell
    Install-Module AzureAD
    ```

2. [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/).

3. Postupujte podle všech pokynů v tomto článku ve stejné relaci prostředí PowerShell. Proces nemusí fungovat, pokud ukončíte relaci PowerShellu tím, že okno zavřete a znovu otevřete později.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Vytvoření instančního objektu v Azure Active Directory

Po splnění požadavků v relaci PowerShellu spusťte následující rutiny PowerShellu pro vytvoření instančního objektu služby s více klienty v Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Zobrazení přihlašovacích údajů v PowerShellu

Než vytvoříte přiřazení role pro instanční objekt, zobrazte si přihlašovací údaje a zapište je pro budoucí použití. Heslo je obzvláště důležité, protože po zavření této relace PowerShellu ho nebudete moct načíst.

Tady jsou tři přihlašovací údaje, které byste měli napsat, a rutiny, které je potřeba spustit, abyste je získali:

- Password:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID klienta:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID aplikace:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Vytvoření přiřazení role na virtuálním počítači s Windows

Dále musíte vytvořit přiřazení role, aby se instanční objekt mohl přihlásit k virtuálnímu klientovi Windows. Ujistěte se, že se přihlásíte pomocí účtu, který má oprávnění k vytváření přiřazení rolí.

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali.

Spusťte následující rutiny PowerShellu, abyste se připojili k virtuální ploše Windows a zobrazili své klienty.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Když najdete název tenanta pro tenanta, pro který chcete vytvořit přiřazení role, použijte tento název v následující rutině:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Přihlaste se pomocí instančního objektu.

Po vytvoření přiřazení role pro instanční objekt se ujistěte, že se instanční objekt může přihlásit k virtuálnímu počítači s Windows spuštěním následující rutiny:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Až se přihlásíte, ujistěte se, že vše funguje tak, že otestuje několik rutin PowerShellu pro virtuální počítače s Windows s instančním objektem.

## <a name="next-steps"></a>Další kroky

Jakmile vytvoříte instanční objekt a přiřadíte mu roli v tenantovi virtuálních klientů s Windows, můžete ho použít k vytvoření fondu hostitelů. Další informace o fondech hostitelů najdete v kurzu vytváření fondu hostitelů na virtuálním počítači s Windows.

 > [!div class="nextstepaction"]
 > [Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
