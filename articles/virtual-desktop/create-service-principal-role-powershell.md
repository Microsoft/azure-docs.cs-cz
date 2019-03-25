---
title: Vytvoření instančních objektů Preview virtuální plochy Windows a přiřazení role pomocí prostředí PowerShell – Azure
description: Postup vytvoření instančních objektů a přiřadit role pomocí prostředí PowerShell ve Windows virtuální plochy, ve verzi Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1bbe89484d72a21c4432d452d4ddae83ea2d2553
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400033"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Kurz: Vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell

Instanční objekty jsou identit, které vytvoříte v Azure Active Directory a přiřadit role a oprávnění pro konkrétní účel. Windows virtuální Desktop ve verzi Preview je můžete vytvořit instanční objekt pro:

- Automatizace úloh správy konkrétní virtuální plochy Windows
- Použijte jako přihlašovací údaje místo vyžaduje vícefaktorové ověřování uživatelů při spuštění libovolné šabloně Windows virtuální plochy Azure Resource Manageru

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření instančního objektu v Azure Active Directory
> * Vytvořit přiřazení role v virtuální plochy Windows
> * Přihlaste se k virtuálnímu klientovi Windows pomocí instančního objektu služby

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit instanční objekty a přiřazení rolí, bude potřeba provést tři věci:

1. Instalace modulu Azure AD. K instalaci modulu, spusťte PowerShell jako správce a spusťte následující rutinu:

    ```powershell
    Install-Module AzureAD
    ```

2. Spusťte následující rutiny s hodnotami v uvozovkách nahrazen hodnotami, které jsou relevantní pro vaši relaci.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. Proveďte všechny pokyny v tomto článku v téže relaci prostředí PowerShell. Nemusí fungovat, pokud okno zavřete a vrátíte se k němu později.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Vytvoření instančního objektu v Azure Active Directory

Po splněny požadavky v relaci prostředí PowerShell spuštěním následující rutiny Powershellu pro vytvoření objektu víceklientské služby na Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Vytvořit přiřazení role v náhledu virtuální plochy Windows

Teď, když jste vytvořili službu objektu zabezpečení, můžete ho použít pro přihlášení k virtuálnímu klientovi Windows. Ujistěte se, že se přihlásit pomocí účtu, který má oprávnění k vytvoření přiřazení role.

Nejprve je potřeba [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

Spusťte následující rutiny Powershellu pro připojení k virtuálnímu klientovi Windows a přiřazení rolí pro službu vytvořit instanční objekt.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Přihlaste se pomocí instančního objektu služby

Po vytvoření přiřazení role pro službu objektu zabezpečení, které by měl nyní Ujistěte se, že instanční objekt můžete přihlásit k virtuálnímu klientovi Windows spuštěním následující rutiny:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Jednou přihlásíte, ujistěte se, že vše funguje tak, že testování si několik rutin Windows Powershellu virtuální plochy se instanční objekt.

## <a name="view-your-credentials-in-powershell"></a>Zobrazit svoje přihlašovací údaje v prostředí PowerShell

Než ukončíte relaci Powershellu, by měl zobrazit svoje přihlašovací údaje a zapište pro budoucí použití. Heslo je obzvláště důležité, protože nebude možné ji moct načíst po zavření této relace Powershellu.

Tady jsou tři přihlašovací údaje, které si musí zapsat a rutiny, které potřebujete ke spuštění, aby je:

- Heslo:

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit instanční objekt a přihlaste se k virtuálnímu klientovi Windows s ním. Další informace o tom, jak se přihlásit k virtuálnímu klientovi Windows, i nadále připojit k virtuální ploše postupy Windows.

- [Připojte se ke klientovi vzdálené plochy na Windows 7 a Windows 10](connect-windows-7-and-10.md)
- [Připojte se k Windows Virtual Desktop Preview webového klienta](connect-web.md)
