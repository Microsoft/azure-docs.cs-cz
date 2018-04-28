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
ms.date: 4/26/2017
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: e17fc85de3d11034889c39fd205b7ddc8cb344cc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurace prostředí PowerShell Azure zásobník uživatele

Jako uživatel Azure zásobníku můžete nakonfigurovat vaše Azure zásobníku Development Kit na prostředí PowerShell. Po dokončení konfigurace, můžete použít PowerShell ke správě prostředků, jako se přihlásit k odběru nabízí, zásobník Azure vytvářet virtuální počítače, nasazení šablony Azure Resource Manager, atd. Toto téma je vymezen na pomocí prostředí, pokud chcete pro nastavení prostředí PowerShell pro operátor cloudovém prostředí odkazovat pouze na uživatele [nakonfigurovat prostředí PowerShell Azure zásobníku operátor](../azure-stack-powershell-configure-admin.md) článku. 

## <a name="prerequisites"></a>Požadavky 

Spusťte následující předpoklady, některý z [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).  
* Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurace uživatelského prostředí a přihlaste se k Azure zásobníku

Na základě typu nasazení (Azure AD ani AD FS), spusťte jeden z následujících skriptů možné nakonfigurovat prostředí PowerShell pro Azure zásobníku (Nezapomeňte nahradit AAD tenantName, GraphAudience koncový bod a hodnoty ArmEndpoint podle konfiguraci prostředí):

### <a name="azure-active-directory-aad-based-deployments"></a>Nasazení na bázi Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

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

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

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

Při fungování v předplatném nově vytvořeného uživatele, který neobsahuje žádné prostředky nasazené prostřednictvím portálu, nejsou automaticky registrované poskytovatele prostředků. Měli je explicitně zaregistrovat pomocí následujícího skriptu:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Testovací připojení

Teď, když jste všechno My nastavení, umožňuje vytvářet prostředky v rámci zásobníku Azure pomocí prostředí PowerShell. Můžete například vytvořit skupinu prostředků pro aplikace a přidat virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup" použijte následující příkaz:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup
* [Vývoj šablon pro Azure zásobníku](azure-stack-develop-templates.md)
* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
