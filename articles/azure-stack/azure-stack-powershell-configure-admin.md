---
title: Připojení ke službě Azure Stack pomocí prostředí PowerShell jako operátor | Dokumentace Microsoftu
description: Zjistěte, jak se připojit ke službě Azure Stack pomocí prostředí PowerShell jako operátor
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
ms.date: 12/07/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 1f9d5325522f8ec40af99059651a00f6cdc0e8e0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089612"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Připojení ke službě Azure Stack pomocí prostředí PowerShell jako operátor

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete nakonfigurovat služby Azure Stack pomocí Powershellu ke správě prostředků, jako je například vytváření nabídek, plánů, kvóty a výstrahy. Toto téma vám pomůže nakonfigurovat prostředí operátora.

## <a name="prerequisites"></a>Požadavky

Spuštění následujících požadovaných součástí, buď z [vývojová sada](./asdk/asdk-connect.md#connect-with-rdp) nebo z Windows na základě externí klienta po [připojené k ASDK prostřednictvím sítě VPN](./asdk/asdk-connect.md#connect-with-vpn). 

 - Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](azure-stack-powershell-install.md).  
 - Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .  

## <a name="connect-with-azure-ad"></a>Spojte se s Azure AD

Konfigurace prostředí Azure Stack operátor pomocí Powershellu. Spusťte jeden z následujících skriptů: nahradit tenantName Azure Active Directory (Azure AD) a hodnoty koncového bodu Azure Resource Manageru s konfigurací prostředí. <!-- GraphAudience endpoint -->

```PowerShell  
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Spojte se s AD FS

Připojte se k prostředí Azure Stack operátorem pomocí Powershellu s Azure Active Directory Federated Services (Azure AD FS). Pro Azure Stack development kit, tento koncový bod Azure Resource Manageru nastavená na `https://adminmanagement.local.azurestack.external`. Pro získání koncového bodu Azure Resource Manageru pro integrované systémy Azure Stack, obraťte se na svého poskytovatele služeb.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $tenantId `
    -Credential $cred
  ```



## <a name="test-the-connectivity"></a>Otestovat připojení

Teď, když máte všechno, co nastavení, pomocí prostředí PowerShell vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

 - [Vývoj šablon pro Azure Stack](user/azure-stack-develop-templates.md)
 - [Nasazení šablon pomocí PowerShellu](user/azure-stack-deploy-template-powershell.md)