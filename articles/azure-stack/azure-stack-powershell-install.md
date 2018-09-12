---
title: Instalace Powershellu pro Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat prostředí PowerShell pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/06/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 995a22b947756fd0fb297c395846df26de20bdd9
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391823"
---
# <a name="install-powershell-for-azure-stack"></a>Instalace Powershellu pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Pro práci s vaším cloudovým, je nutné nainstalovat kompatibilní moduly Powershellu služby Azure Stack. Kompatibilita je povolená díky funkci s názvem *profilů API*.

Profily rozhraní API poskytují způsob, jak spravovat verze rozdíly mezi Azure a Azure Stack. Profilu verze rozhraní API je sada modulů Azure Powershellu pro Resource Manager s konkrétní verzí rozhraní API. Každá Cloudová platforma obsahuje sadu podporovaných profilů verzí API. Například Azure Stack podporuje verzi konkrétní průkaz s datem profilu, jako **2017-03-09-profile**, a podporuje Azure **nejnovější** profilu verze rozhraní API. Při instalaci profilu jsou nainstalovány moduly Powershellu pro Azure Resource Manager, které odpovídají zadaný profil.  

Můžete nainstalovat kompatibilní moduly Powershellu v Internetu připojený, částečně připojeno nebo odpojených scénářů, služby Azure Stack. Tento článek vás provede podrobné pokyny k instalaci prostředí PowerShell pro Azure Stack pro tyto scénáře.

## <a name="1-verify-your-prerequisites"></a>1. Ověření vašich požadavků

Před zahájením práce s Azure Stack a prostředí PowerShell, musíte mít splněné následující požadavky:

- **Verze prostředí PowerShell 5.0**  
Zkontrolujte verzi spuštěním **$PSVersionTable.PSVersion** a porovnat **hlavní** verze. Pokud nemáte prostředí PowerShell 5.0, postupujte [odkaz](/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) upgradovat na PowerShell 5.0.

  > [!Note]  
  > PowerShell 5.0 vyžaduje počítač s Windows.

- **V příkazovém řádku se zvýšenými oprávněními spusťte prostředí Powershell**  
  Je nutné spustit PowerShell s oprávněními správce.

- **Přístup Galerie prostředí PowerShell**  
  Je nutné použít [Galerie prostředí PowerShell](https://www.powershellgallery.com). Galerie je centrální úložiště pro PowerShell obsah. **PowerShellGet** modul obsahuje rutiny pro zjišťování, instalaci, aktualizaci a publikováním artefaktů Powershellu, jako jsou moduly, prostředky DSC, funkce rolí a skripty z Galerie prostředí PowerShell a další privátní úložiště. Pokud používáte prostředí PowerShell v odpojeném scénáři, musí načíst prostředky z počítače s připojením k Internetu a uložit je do umístění přístupné pro odpojené počítače.

<!-- Nuget? -->

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Ověření přístupnosti Galerie prostředí PowerShell

Ověřte, jestli PSGallery se zaregistruje jako úložiště.

> [!Note]  
> Tento krok vyžaduje přístup k Internetu. 

Otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Pokud úložiště není zaregistrovaný, otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující příkaz:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Odinstalovat stávající verze modulů Azure Stack Powershellu

Než začnete instalovat na požadovanou verzi, ujistěte se, že odinstalovat všechny dříve nainstalované moduly Azure Stack AzureRM Powershellu. Můžete je odinstalovat pomocí jedné z těchto dvou metod:

1. Chcete-li odinstalovat existující moduly AzureRM Powershellu, zavřete všechny aktivní relace prostředí PowerShell a spusťte následující rutiny:

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ````

2. Odstranit všechny složky, které začínají `Azure` z `C:\Program Files\WindowsPowerShell\Modules` a `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` složek. Odstranění těchto složek odebere všechny existující moduly Powershellu.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Připojeno: Instalace Powershellu pro Azure Stack s připojením k Internetu

Vyžaduje Azure Stack **2017-03-09-profile** profilu verze rozhraní API, která je k dispozici nainstalováním **AzureRM.Bootstrapper** modulu. Kromě moduly AzureRM také byste měli nainstalovat moduly Azure Powershellu specifické pro zásobník. 

Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na pracovní stanici vývoje:

  - **Verze 1.4.0** (Azure Stack 1804 nebo vyšší)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.4.0 if Azure Stack is running 1804 at a minimum 
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

- **Verze 1.2.11** (před 1804)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

Potvrďte instalaci spuštěním následujícího příkazu:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Pokud je instalace úspěšná, zobrazí se moduly AzureRM a AzureStack ve výstupu.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Odpojeno: Instalace Powershellu bez připojení k Internetu

V případě odpojené v musíte nejprve stáhnout modulů prostředí PowerShell do počítače, který má připojení k Internetu a je přenést na Azure Stack Development Kit pro instalaci.

Přihlaste se k počítači s připojením k Internetu a stáhnout balíčky Azure Resource Manageru a AzureStack, v závislosti na vaší verzi služby Azure Stack pomocí těchto skriptů:


  - **Verze 1.3.0** (Azure Stack 1804 nebo vyšší)
  
    > [!Note]  
    K upgradu portálu 1.2.11 verze, najdete v článku [Průvodce migrací](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - **Verze 1.2.11** (před 1804)

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"

      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    ````

2. Zkopírujte stažený balíčky do zařízení USB.

3. Přihlaste se k pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění na pracovní stanici.

4. Nyní zaregistrovat toto umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Konfigurace Powershellu pro použití proxy serveru

Ve scénářích, které vyžadují přístup k Internetu proxy server je nutné nejprve nakonfigurovat prostředí PowerShell pro použití existujícího proxy serveru:

1. Otevřete řádku Powershellu se zvýšenými oprávněními.
2. Spusťte následující příkazy:

   ```PowerShell  
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Další postup

 - [Stáhněte si nástroje pro Azure Stack z Githubu](azure-stack-powershell-download.md)
 - [Konfigurace prostředí PowerShell uživatele Azure stacku](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurace prostředí PowerShell pro operátory Azure stacku](azure-stack-powershell-configure-admin.md) 
 - [Správa profilů verzí API ve službě Azure Stack](user/azure-stack-version-profiles.md)  
