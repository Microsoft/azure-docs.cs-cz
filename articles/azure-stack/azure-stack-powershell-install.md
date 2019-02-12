---
title: Instalace Powershellu pro Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat prostředí PowerShell pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 72c4bfc4151e8120f8641c743c0da3896bd71fe1
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995026"
---
# <a name="install-powershell-for-azure-stack"></a>Instalace Powershellu pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Pro práci s vaším cloudovým, je nutné nainstalovat kompatibilní moduly Powershellu služby Azure Stack. Kompatibilita je povolená díky funkci s názvem *profilů API*.

Profily rozhraní API poskytují způsob, jak spravovat verze rozdíly mezi Azure a Azure Stack. Profilu verze rozhraní API je sada modulů Azure Powershellu pro Resource Manager s konkrétní verzí rozhraní API. Každá Cloudová platforma obsahuje sadu podporovaných profilů verzí API. Například Azure Stack jako podporuje verzi konkrétní profil **2018-03-01hybridní**. Při instalaci profilu jsou nainstalovány moduly Powershellu pro Azure Resource Manager, které odpovídají zadaný profil.

Můžete nainstalovat kompatibilní moduly Powershellu v Internetu připojený, částečně připojeno nebo odpojených scénářů, služby Azure Stack. Tento článek vás provede podrobné pokyny k instalaci prostředí PowerShell pro Azure Stack pro tyto scénáře.

## <a name="1-verify-your-prerequisites"></a>1. Ověření vašich požadavků

Před zahájením práce s Azure Stack a prostředí PowerShell, musíte mít splněné následující požadavky:

- **Prostředí PowerShell verze 5.0** zkontrolujte verzi spuštěním **$PSVersionTable.PSVersion** a porovnat **hlavní** verze. Pokud nemáte prostředí PowerShell 5.0, postupujte [instalace prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 vyžaduje počítač s Windows.

- **Spusťte prostředí Powershell v příkazovém řádku se zvýšenými oprávněními**.
  Je nutné spustit PowerShell s oprávněními správce.

- **Galerie prostředí PowerShell přístup** potřebují přístup k [Galerie prostředí PowerShell](https://www.powershellgallery.com). Galerie je centrální úložiště pro PowerShell obsah. **PowerShellGet** modul obsahuje rutiny pro zjišťování, instalaci, aktualizaci a publikováním artefaktů Powershellu, jako jsou moduly, prostředky DSC, funkce rolí a skripty z Galerie prostředí PowerShell a další privátní úložiště. Pokud používáte prostředí PowerShell v odpojeném scénáři, musí načíst prostředky z počítače s připojením k Internetu a uložit je do umístění přístupné pro odpojené počítače.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Ověření přístupnosti Galerie prostředí PowerShell

Ověřte, jestli PSGallery se zaregistruje jako úložiště.

> [!Note]  
> Tento krok vyžaduje přístup k Internetu.

Otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Pokud úložiště není zaregistrovaný, otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující příkaz:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Odinstalovat stávající verze modulů Azure Stack Powershellu

Než začnete instalovat na požadovanou verzi, ujistěte se, že odinstalovat všechny dříve nainstalované moduly Azure Stack AzureRM Powershellu. Můžete je odinstalovat pomocí jedné z těchto dvou metod:

1. Chcete-li odinstalovat existující moduly AzureRM Powershellu, zavřete všechny aktivní relace prostředí PowerShell a spusťte následující rutiny:

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    Pokud dojde k chybě, jako "modul je již používán., zavřete prosím relace Powershellu, které používají moduly a znovu spusťte skript výše.

2. Odstranit všechny složky, které začínají `Azure` nebo `Azs.` z `C:\Program Files\WindowsPowerShell\Modules` a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` složek. Odstranění těchto složek odebere všechny existující moduly Powershellu.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Připojeno: Instalace Powershellu pro Azure Stack s připojením k Internetu

Vyžaduje Azure Stack **2018-03-01hybridní** profilu verze rozhraní API pro Azure Stack verze 1808 nebo novější. Profil, který je k dispozici nainstalováním **AzureRM.Bootstrapper** modulu. Kromě toho moduly AzureRM také byste měli nainstalovat moduly Powershellu pro Azure Stack specifické. Profilu verze rozhraní API a moduly Azure Stack Powershellu budete potřebovat, bude záviset na verzi služby Azure Stack se systémem.

Instalace má tři kroky:

1. Instalace Azure Stack Powershellu v závislosti na vaší verzi služby Azure Stack
2. Povolení funkcí dalšího úložiště
3. Potvrzení instalace prostředí PowerShell

### <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na pracovní stanici vývoje:

- Azure Stack 1901 nebo novější.

    ```PowerShell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > Verze modulu Azure Stack 1.7.0 je zásadní změnu. Migrace ze služby Azure Stack 1.6.0 najdete [Průvodce migrací](https://aka.ms/azspshmigration170).

- Azure Stack 1811 nebo starší.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Azure Stack 1809 nebo starší.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Upgrade prostředí Azure PowerShell z **2017-03-09-profile** k **2018-03-01hybridní**, podrobnosti najdete [Průvodce migrací](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

### <a name="enable-additional-storage-features"></a>Povolení funkcí dalšího úložiště

Chcete-li využít funkce další úložiště (jak je uvedeno v části připojení), stahování a nainstalujte následující balíčky.

```PowerShell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Potvrzení instalace prostředí PowerShell

Potvrďte instalaci spuštěním následujícího příkazu:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Pokud je instalace úspěšná, zobrazí se moduly AzureRM a AzureStack ve výstupu.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Odpojeno: Instalace Powershellu bez připojení k Internetu

V případě odpojené v musíte nejprve stáhnout modulů prostředí PowerShell do počítače, který má připojení k Internetu a je přenést na Azure Stack Development Kit pro instalaci.

Přihlaste se k počítači s připojením k Internetu a stáhnout balíčky Azure Resource Manageru a AzureStack, v závislosti na vaší verzi služby Azure Stack pomocí těchto skriptů.

Instalace je třeba provést čtyři kroky:

1. Azure Stack Powershellu nainstalujte na počítač připojený
2. Povolení funkcí dalšího úložiště
3. Přenos balíčků prostředí PowerShell na odpojené pracovní stanice
4. Potvrzení instalace prostředí PowerShell


### <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

- Azure Stack 1901 nebo novější.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > Verze modulu Azure Stack 1.7.0 je zásadní změnu. Migrace z AzureStack 1.6.0 najdete [Průvodce migrací](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).


  - Azure Stack 1811 nebo starší.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 nebo starší.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

### <a name="enable-additional-storage-features"></a>Povolení funkcí dalšího úložiště

Chcete-li využít funkce další úložiště (jak je uvedeno v části připojení), stahování a nainstalujte následující balíčky.

```PowerShell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>Přidejte své balíčky do pracovní stanice

1. Zkopírujte stažený balíčky do zařízení USB.

2. Přihlaste se k odpojené pracovní stanice a zkopírujte balíčky ze zařízení USB do umístění na pracovní stanici.

3. Nyní zaregistrovat toto umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Potvrzení instalace prostředí PowerShell

Potvrďte instalaci spuštěním následujícího příkazu:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
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