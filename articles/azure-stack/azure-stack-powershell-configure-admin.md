---
title: Konfigurace prostředí Azure PowerShell zásobníku | Microsoft Docs
description: Postup konfigurace prostředí Azure PowerShell zásobníku.
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
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 48a765b4e613e45699099330e4ff5c3ce7512686
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300733"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Konfigurace prostředí Azure PowerShell zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete nakonfigurovat zásobník Azure pomocí prostředí PowerShell ke správě prostředků, jako je například vytváření nabídek, plánů, kvóty a upozornění. Toto téma vám pomůže nakonfigurovat prostředí operátor.

## <a name="prerequisites"></a>Požadavky

Spusťte následující předpoklady, některý z [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).  
 - Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurace prostředí operátor a přihlaste se k Azure zásobníku

Konfigurace prostředí Azure zásobníku operátor pomocí prostředí PowerShell. Na základě typu nasazení, Azure AD ani AD FS, spusťte jeden z následujících skriptů: tenantName Azure AD, koncový bod GraphAudience a ArmEndpoint hodnoty nahraďte konfiguraci prostředí.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD) na základě nasazení

````PowerShell  
# Set the Azure Stack Tools location
$AzSTools = "C:\AzureStack-Tools-master"

# Set the Azure Active Directory Tenant Name that is used to deploy Azure Stack
$AadTenantName = "<myDirectoryTenantName>.onmicrosoft.com"

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
# To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/.
# To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<Graph Audience endpoint for your environment>"

# Navigate to the Azure Stack Tools folder and import the **Connect** PowerShell module
cd $AzSTools
Set-ExecutionPolicy RemoteSigned
Import-Module .\Connect\AzureStack.Connect.psm1

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

# Get the Active Directory tenantId that is used to deploy Azure Stack
$TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $AadTenantName `
    -EnvironmentName "AzureStackAdmin"

# After signing in to your environment, Azure Stack cmdlets
# can be easily targeted at your Azure Stack instance.
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Nasazení na základě služby Active Directory Federation Services (AD FS)

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Testovací připojení

Teď, když máte všechno nastavení, umožňuje vytvářet prostředky v rámci zásobníku Azure pomocí prostředí PowerShell. Můžete například vytvořit skupinu prostředků pro aplikace a přidat virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup" použijte následující příkaz:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup
 - [Vývoj šablon pro Azure zásobníku](user/azure-stack-develop-templates.md)
 - [Nasazení šablon pomocí PowerShellu](user/azure-stack-deploy-template-powershell.md)
