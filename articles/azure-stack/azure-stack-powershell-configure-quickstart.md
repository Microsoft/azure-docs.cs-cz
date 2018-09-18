---
title: Instalace a konfigurace Powershellu pro rychlý start Azure Stack | Dokumentace Microsoftu
description: Další informace o instalaci a konfiguraci prostředí PowerShell pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: mabrigg
ms.openlocfilehash: 75b7f9c78418883344ce3c066135fe0847f649ac
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981956"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Uvedení do provozu pomocí prostředí PowerShell ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento rychlý start vám umožní nainstalovat a nakonfigurovat prostředí Azure Stack pomocí Powershellu. Skript, který zajišťuje v tomto článku je vymezen **operátory Azure stacku** pouze.

Tento článek je zkrácenou verzi kroky, které jsou popsány v [instalace Powershellu]( azure-stack-powershell-install.md), [stáhněte si nástroje]( azure-stack-powershell-download.md), a [konfigurace prostředí PowerShell operátory Azure stacku]( azure-stack-powershell-configure-admin.md) článků. Pomocí skriptů v tomto článku můžete nastavit prostředí PowerShell pro Azure Stack prostředí, které jsou nasazeny pomocí Azure Active Directory nebo Active Directory Federation Services (AD FS).  

## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Nastavení prostředí PowerShell pro nasazení na základě Azure Active Directory  

Pokud jste připojeni přes síť VPN, přihlaste se k Azure Stack Development Kit, nebo externí klienta se systémem Windows. Otevřete relaci Powershellu se zvýšenými oprávněními a spusťte tyto skripty.

Nezapomeňte aktualizovat **TenantName**, **ArmEndpoint**, a **GraphAudience** proměnné podle potřeby pro konfiguraci vašeho prostředí:

```PowerShell  
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following commands:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module
Get-Module Azs.* -ListAvailable | Uninstall-Module -force

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Načtěte modul profilu a správce rozhraní API pro vaši verzi sady Azure Stack.

  - Azure Stack 1808 nebo novější.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.5.0 -Force
  ```

  - Azure Stack 1807 nebo starší.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0 -Force
  ```

  - Azure Stack 1804 nebo starší.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force
  ```

Stáhněte si nástroje pro Azure Stack a připojení.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId -AADTenantName $TenantName -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Nastavení pro nasazení služby AD FS pomocí Powershellu

Pokud používáte Azure Stack při připojení k Internetu, můžete pomocí následujícího skriptu. Ale pokud používáte Azure Stack bez připojení k Internetu, použijte [odpojení způsob instalace Powershellu](azure-stack-powershell-install.md) a rutiny ke konfiguraci prostředí PowerShell, zůstanou stejné, jak je znázorněno v tomto skriptu. Pokud jste připojeni přes síť VPN, přihlaste se k Azure Stack Development Kit, nebo externí klienta se systémem Windows. Otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující skript. Nezapomeňte aktualizovat **ArmEndpoint** a **GraphAudience** proměnné podle potřeby pro konfiguraci vašeho prostředí:

```PowerShell  

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Načtěte modul profilu a správce rozhraní API pro vaši verzi sady Azure Stack.

  - Azure Stack 1808 nebo novější.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - Azure Stack 1807 nebo starší.

    > [!Note]  
    K upgradu portálu 1.2.11 verze, najdete v článku [Průvodce migrací](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - Azure Stack 1804 nebo starší.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

Stáhněte si nástroje pro Azure Stack a připojení.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Otestovat připojení

Teď, když jste nakonfigurovali prostředí PowerShell, můžete otestovat konfiguraci tak, že vytvoříte skupinu prostředků:

```PowerShell  
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Chcete-li určit skupinu prostředků, musíte ve svém předplatném máte skupinu prostředků. Další informace o předplatných najdete v tématu [přehled plánu, nabídky, kvót a předplatného](azure-stack-plan-offer-quota-overview.md)

Po vytvoření skupiny prostředků **Stav zřizování** je nastavena na **Succeeded**.

## <a name="next-steps"></a>Další postup

 - [Instalace a konfigurace rozhraní příkazového řádku](azure-stack-connect-cli.md)
 - [Vývoj šablon](user/azure-stack-develop-templates.md)
