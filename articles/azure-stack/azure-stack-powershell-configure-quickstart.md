---
title: Instalace a konfigurace prostředí PowerShell pro rychlý start Azure zásobníku | Microsoft Docs
description: Další informace o instalaci a konfiguraci prostředí PowerShell pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: 66598bda7ca1fcf5c6e05ab47232236b740177a6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075234"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>A její spuštění pomocí prostředí PowerShell v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Tento rychlý Start umožňuje instalace a konfigurace prostředí Azure zásobníku pomocí prostředí PowerShell. Skript, který nabízíme v tomto článku je vymezen na **zásobník Azure operátor** pouze.

Tento článek je zhuštěný verzi kroky, které jsou popsané v [instalaci prostředí PowerShell]( azure-stack-powershell-install.md), [stáhnout nástroje]( azure-stack-powershell-download.md), a [nakonfigurovat prostředí PowerShell Azure zásobníku operátor]( azure-stack-powershell-configure-admin.md) články. Pomocí skriptů v tomto tématu můžete nastavit prostředí PowerShell pro Azure zásobníku prostředí, které se nasadí s Azure Active Directory nebo Active Directory Federation Services (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Nastavení prostředí PowerShell pro nasazení založené na Azure Active Directory

Pokud jste připojení prostřednictvím sítě VPN, přihlaste se k vaší Azure zásobníku Development Kit nebo externí klienta se systémem Windows. Otevřete relaci prostředí PowerShell ISE zvýšenými oprávněními a spusťte následující skript. Nezapomeňte aktualizovat **TenantName**, **ArmEndpoint**, a **GraphAudience** proměnné v případě potřeby pro konfiguraci prostředí:

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Nastavení prostředí PowerShell pro nasazení služby AD FS na základě

Pokud jsou v provozu Azure zásobníku při připojení k Internetu, můžete použít následující skript. Ale pokud pracujete zásobník Azure bez připojení k Internetu, použijte [odpojení způsob instalace prostředí PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) a zůstane stejný, jak ukazuje tento skript rutiny ke konfiguraci prostředí PowerShell. Pokud jste připojení prostřednictvím sítě VPN, přihlaste se k vaší Azure zásobníku Development Kit nebo externí klienta se systémem Windows. Otevřete relaci prostředí PowerShell ISE zvýšenými oprávněními a spusťte následující skript. Nezapomeňte aktualizovat **ArmEndpoint** a **GraphAudience** proměnné v případě potřeby pro konfiguraci prostředí:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testovací připojení

Nyní, když jste nakonfigurovali prostředí PowerShell, můžete otestovat konfiguraci tak, že vytvoříte skupinu prostředků:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Pokud chcete zadat skupinu prostředků, musíte mít skupiny prostředků v rámci vašeho předplatného. Další informace o předplatných najdete v tématu [přehled plán, nabídka, kvóty a předplatného](azure-stack-plan-offer-quota-overview.md)

Po vytvoření skupiny prostředků **Stav zřizování** je nastavena na **úspěšné**.

## <a name="next-steps"></a>Další postup

* [Instalace a konfigurace rozhraní příkazového řádku](azure-stack-connect-cli.md)

* [Vývoj šablon](user/azure-stack-develop-templates.md)
