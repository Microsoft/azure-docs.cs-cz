---
title: Instalace prostředí PowerShell pro Azure zásobníku | Microsoft Docs
description: Informace o instalaci prostředí PowerShell pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F8D99A91-15B5-4073-BE07-A43514A6D2CF
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: mabrigg
ms.openlocfilehash: 9d09fb60722865a75ea0825f5ca54f792642980a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="install-powershell-for-azure-stack"></a>Instalace prostředí PowerShell pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku kompatibilní prostředí Azure PowerShell moduly jsou nutné k práci s Azure zásobníku. Tento článek vás provede kroky potřebné k instalaci prostředí PowerShell pro Azure zásobníku. Můžete vytvořit podle pokynů popsaných v tomto článku z Development Kit zásobník Azure nebo z externí klienta se systémem Windows, pokud jsou připojené prostřednictvím sítě VPN.

Tento článek poskytuje podrobné pokyny k instalaci prostředí PowerShell. Pokud chcete nainstalovat a nakonfigurovat prostředí PowerShell, můžete však použít skript uvedený v článku "Zprovoznění pomocí prostředí PowerShell".

> [!NOTE]
> Následující kroky vyžadují prostředí PowerShell 5.0. Pokud chcete zkontrolovat vaší verzí, spusťte $PSVersionTable.PSVersion a porovnat "Hlavní" verzi.

Příkazy prostředí PowerShell pro Azure zásobníku jsou nainstalovány v galerii prostředí PowerShell. Pokud chcete zaregistrovat PSGallery úložiště, otevřete relaci prostředí PowerShell zvýšenými z sadě pro vývoj nebo z externí klienta se systémem Windows Pokud jsou připojené prostřednictvím sítě VPN a spusťte následující příkaz:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Odinstalujte stávající verze prostředí PowerShell

Před instalací požadovanou verzi, ujistěte se, abyste odinstalovali všech existujících modulů prostředí Azure PowerShell. Můžete je odinstalovat pomocí jedné z následujících dvou metod:

* Odinstalovat stávající moduly Powershellu, přihlaste se k sadě pro vývoj, nebo externí klienta se systémem Windows Pokud máte v úmyslu vytvořit připojení VPN. Zavřete všechny aktivní relace prostředí PowerShell a spusťte následující příkaz:

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Přihlaste se k sadě pro vývoj, nebo externí klienta se systémem Windows Pokud máte v úmyslu vytvořit připojení VPN. Odstranit všechny složky, které začínají "Azure" z `C:\Program Files (x86)\WindowsPowerShell\Modules` a `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` složek. Odstranění těchto složek odebere všechny existující moduly Powershellu z "AzureStackAdmin" a "globální" uživatele oborů.

Následující části popisují kroky potřebné k instalaci prostředí PowerShell pro Azure zásobníku. Prostředí PowerShell můžete nainstalovat v zásobníku Azure, která je provozována v připojené, částečně připojen nebo ve scénáři odpojené.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Instalace prostředí PowerShell ve scénáři připojené (s připojením k Internetu)

Prostřednictvím rozhraní API verze profily jsou nainstalované Azure AzureRM moduly kompatibilní zásobníku. Vyžaduje Azure zásobníku **2017-03-09profil** profil verze rozhraní API, který je k dispozici po instalaci modulu AzureRM.Bootstrapper. Další informace o rozhraní API verze profily a rutiny poskytované jejich, naleznete [spravovat profily verze rozhraní API](azure-stack-version-profiles-powershell.md). Kromě AzureRM moduly nainstalujete také moduly Azure PowerShell specifické pro zásobníku. Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na pracovní stanici:

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Pokud chcete potvrdit instalaci, spusťte následující příkaz:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```

  Pokud je instalace úspěšná, moduly Azure zásobníku a AzureRM se zobrazí ve výstupu.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Nainstalujte prostředí PowerShell odpojený nebo částečně připojené scénář (s omezenou připojení k Internetu)

Ve scénáři odpojených nebo připojených částečně musíte nejprve stáhnout moduly Powershellu pro počítač, který má připojení k Internetu a potom přenést do Azure zásobníku Development Kit pro instalaci.

1. Přihlaste se k počítači, kde máte připojení k Internetu a pomocí následujícího skriptu stahování AzureRM a AzureStack balíčky do svého místního počítače:

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11
   ```

2. Zkopírujte stažených balíčků přes USB zařízení.

3. Přihlaste se k sadě pro vývoj a zkopírujte balíčky ze zařízení USB do umístění v sadě pro vývoj.

4. Nyní je nutné zaregistrovat toto umístění jako výchozí úložiště a nainstalovat moduly AzureRM a AzureStack z tohoto úložiště:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName
   ```

## <a name="next-steps"></a>Další postup

* [Stažení nástroje Azure zásobníku z Githubu](azure-stack-powershell-download.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)
* [Správa profilů verze rozhraní API v Azure zásobníku](azure-stack-version-profiles-powershell.md)
