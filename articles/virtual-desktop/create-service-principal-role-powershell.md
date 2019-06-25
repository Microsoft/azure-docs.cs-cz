---
title: Vytvoření instančních objektů Preview virtuální plochy Windows a přiřazení role pomocí prostředí PowerShell – Azure
description: Postup vytvoření instančních objektů a přiřadit role pomocí prostředí PowerShell ve verzi Preview virtuální pro plochu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 44c823653ecbad1c4dd1fd35b676c8a6d8bd1620
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206663"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Kurz: Vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell

Instanční objekty jsou identit, které vytvoříte v Azure Active Directory a přiřadit role a oprávnění pro konkrétní účel. Windows virtuální Desktop ve verzi Preview je můžete vytvořit instanční objekt pro:

- Automatizace úloh správy konkrétní virtuální plochy Windows.
- Použijte jako přihlašovací údaje místo vyžaduje vícefaktorové ověřování uživatelů při spuštění jakékoli šablony Azure Resource Manageru pro virtuální plochy Windows.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření instančního objektu v Azure Active Directory.
> * Vytvořte přiřazení role v virtuální plochy Windows.
> * Přihlaste se k virtuálnímu klientovi Windows s použitím instančního objektu.

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit instanční objekty a přiřazení rolí, je třeba provést tři věci:

1. Instalace modulu Azure AD. K instalaci modulu, spusťte PowerShell jako správce a spusťte následující rutinu:

    ```powershell
    Install-Module AzureAD
    ```

2. Spusťte následující rutiny s hodnotami v uvozovkách nahrazen hodnotami, které jsou relevantní pro vaši relaci.

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. Proveďte všechny pokyny v tomto článku v téže relaci prostředí PowerShell. Nemusí fungovat, pokud okno zavřete a vrátíte se k němu později.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Vytvoření instančního objektu v Azure Active Directory

Po splněny požadavky v relaci Powershellu spusťte následující rutiny Powershellu pro vytvoření objektu víceklientské služby na Azure.

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
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Přihlaste se pomocí instančního objektu služby

Po vytvoření přiřazení role pro službu objektu zabezpečení, ujistěte se, že instanční objekt můžete přihlásit k virtuálnímu klientovi Windows spuštěním následující rutiny:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po přihlášení, ujistěte se, že vše funguje tak, že testování několik rutin Windows Powershellu virtuální plochy se instanční objekt.

## <a name="view-your-credentials-in-powershell"></a>Zobrazit svoje přihlašovací údaje v prostředí PowerShell

Než ukončíte relaci Powershellu, zobrazit svoje přihlašovací údaje a zapište pro budoucí použití. Heslo je obzvláště důležité, protože nebude možné ji moct načíst i po zavření této relace Powershellu.

Tady jsou tři přihlašovací údaje, které si musí zapsat a rutiny, které potřebujete ke spuštění, aby je:

- Heslo:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID tenanta:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID aplikace:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Další postup

Po vytvoření instančního objektu a přiřazenou roli ve vašem tenantovi virtuální plochy Windows, můžete k vytvoření fondu hostitele. Další informace o fondech hostitele, pokračujte ke kurzu pro vytváření hostitele fondu ve virtuální plochy Windows.

 > [!div class="nextstepaction"]
 > [Kurz fondu hostitele virtuálního klienta Windows](./create-host-pools-azure-marketplace.md)
