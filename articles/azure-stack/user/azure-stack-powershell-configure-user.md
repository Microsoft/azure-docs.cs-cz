---
title: Konfigurace prostředí PowerShell Azure zásobník uživatele | Microsoft Docs
description: Konfigurace prostředí PowerShell Azure zásobník uživatele
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: bcd1c53221028a852550fa429abcb9f8e9523ed4
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752417"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurace prostředí PowerShell Azure zásobník uživatele

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Ke konfiguraci prostředí PowerShell pro Azure zásobník uživatele, postupujte podle pokynů v tomto článku.
Po dokončení konfigurace prostředí, můžete použít PowerShell ke správě prostředků Azure zásobníku. Můžete například použít PowerShell pro přihlášení k odběru do nabídky, vytváření virtuálních počítačů a nasazení šablony Azure Resource Manager.

>[!NOTE]
>Tento článek je určené pro Azure zásobníku uživatelské prostředí. Pokud chcete pro nastavení prostředí PowerShell pro cloudové prostředí operátor, podívejte se na [nakonfigurovat prostředí PowerShell Azure zásobníku operátor](../azure-stack-powershell-configure-admin.md) článku.

## <a name="prerequisites"></a>Požadavky

Můžete nakonfigurovat tyto předpoklady z [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).
* Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurace uživatelského prostředí a přihlaste se k Azure zásobníku

Na základě typu nasazení zásobník Azure (Azure AD ani AD FS), spusťte jeden z následujících skriptů možné nakonfigurovat prostředí PowerShell pro Azure zásobníku.

Ujistěte se, že nahradíte proměnné následujícího skriptu s hodnotami z vaší konfigurace protokolů Azure:

* AAD tenantName
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Nasazení na bázi Azure Active Directory (AAD)

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Nasazení na základě služby Active Directory Federation Services (AD FS)

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Registrace poskytovatele prostředků

Zprostředkovatelé prostředků nejsou pro nové uživatele odběry, které nemají žádné prostředky nasazené prostřednictvím portálu zaregistruje automaticky. Spuštěním následujícího skriptu můžete explicitně registrovat poskytovatele prostředků:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testovací připojení

Pokud máte k dispozici všechna nastavení, testovací připojení pomocí prostředí PowerShell k vytváření prostředků v Azure zásobníku. Jako testu vytvořte skupinu prostředků pro aplikaci a přidejte virtuální počítač. Spusťte následující příkaz pro vytvoření skupiny prostředků s názvem "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

* [Vývoj šablon pro Azure zásobníku](azure-stack-develop-templates.md)
* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
