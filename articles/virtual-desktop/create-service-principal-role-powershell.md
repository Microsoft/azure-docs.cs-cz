---
title: Přiřazení hlavní role služby Virtuální plochy Windows – Azure
description: Jak vytvořit instanční objekty a přiřadit role pomocí prostředí PowerShell ve virtuální ploše windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 322ff2be4b90a945305915432a8191db9f4efee2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252553"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Kurz: Vytvoření instančních objektů a přiřazení rolí pomocí Prostředí PowerShell

Instanční objekty jsou identity, které můžete vytvořit ve službě Azure Active Directory a přiřadit role a oprávnění pro konkrétní účel. Ve službě Windows Virtual Desktop můžete vytvořit instanční objekt pro:

- Automatizujte konkrétní úlohy správy virtuální plochy systému Windows.
- Místo uživatelů požadovaných mfa se používají jako přihlašovací údaje, když spouštějíte libovolnou šablonu Azure Resource Manageru pro virtuální plochu Windows.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte instanční objekt ve službě Azure Active Directory.
> * Vytvořte přiřazení role ve virtuální ploše windows.
> * Přihlaste se k virtuální ploše Windows pomocí instančního objektu.

## <a name="prerequisites"></a>Požadavky

Před vytvořením instančních objektů a přiřazení rolí je třeba provést tři věci:

1. Nainstalujte modul AzureAD. Chcete-li modul nainstalovat, spusťte prostředí PowerShell jako správce a spusťte následující rutinu:

    ```powershell
    Install-Module AzureAD
    ```

2. [Stáhněte a importujte modul Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/).

3. Postupujte podle všech pokynů v tomto článku ve stejné relaci Prostředí PowerShell. Tento proces nemusí fungovat, pokud přerušíte relaci prostředí PowerShell zavřením okna a jeho pozdějším otevřením.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Vytvoření instančního objektu v Azure Active Directory

Po splnění požadavků v relaci PowerShellu spusťte následující rutiny Prostředí PowerShell a vytvořte víceklientský instanční objekt v Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Zobrazení přihlašovacích údajů v PowerShellu

Před vytvořením přiřazení role pro instanční objekt zobrazte svá pověření a poznamenejte si je pro budoucí použití. Heslo je obzvláště důležité, protože po ukončení této relace prostředí PowerShell jej nebudete moct načíst.

Zde jsou tři pověření, které byste měli zapsat, a rutiny, které potřebujete spustit, abyste je získali:

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

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Vytvoření přiřazení role ve virtuální ploše windows

Dále je třeba vytvořit přiřazení role, aby se instanční objekt mohl přihlásit k virtuální ploše systému Windows. Nezapomeňte se přihlásit pomocí účtu, který má oprávnění k vytváření přiřazení rolí.

Nejprve [si stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili.

Spusťte následující rutiny prostředí PowerShell pro připojení k virtuální ploše Windows a zobrazení vašich klientů.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Když najdete název klienta pro klienta, pro kterého chcete vytvořit přiřazení role, použijte tento název v následující rutině:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Přihlášení pomocí instančního objektu

Po vytvoření přiřazení role pro instanční objekt se ujistěte, že se instanční objekt může přihlásit k virtuální ploše systému Windows spuštěním následující rutiny:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po přihlášení se ujistěte, že vše funguje testováním několika rutin prostředí Windows Virtual Desktop PowerShell s instančním objektem.

## <a name="next-steps"></a>Další kroky

Po vytvoření instančního objektu a přiřazení role v tenantovi virtuální plochy Windows můžete ji použít k vytvoření fondu hostitelů. Další informace o fondech hostitelů najdete v kurzu pro vytvoření fondu hostitelů ve virtuální ploše windows.

 > [!div class="nextstepaction"]
 > [Vytvoření fondu hostitelů pomocí Azure Marketplace](./create-host-pools-azure-marketplace.md)
