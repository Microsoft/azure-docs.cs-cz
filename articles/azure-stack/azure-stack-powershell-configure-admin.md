---
title: Konfigurace prostředí PowerShell Azure zásobníku operátor | Microsoft Docs
description: Informace o konfiguraci Azure zásobníku operátor prostředí PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: a8ab52de6c57e84bb2c90ce6bcf53ef1b92e30af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Konfigurace prostředí PowerShell Azure zásobníku operátor

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete nakonfigurovat zásobník Azure pomocí prostředí PowerShell ke správě prostředků, jako je například vytváření nabídek, plánů, kvóty a upozornění. Toto téma vám pomůže nakonfigurovat prostředí operátor. Pokud chcete nakonfigurovat prostředí PowerShell pro uživatelského prostředí, najdete v sekci [nakonfigurovat prostředí PowerShell Azure zásobník uživatele](user/azure-stack-powershell-configure-user.md) článku.

## <a name="prerequisites"></a>Požadavky

Spusťte následující předpoklady, některý z [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).  
* Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurace prostředí operátor a přihlaste se k Azure zásobníku

Konfigurace prostředí Azure zásobníku operátor pomocí prostředí PowerShell. Na základě typu nasazení, Azure AD ani AD FS, spusťte jeden z následujících skriptů: tenantName Azure AD, koncový bod GraphAudience a ArmEndpoint hodnoty nahraďte konfiguraci prostředí.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD) na základě nasazení

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Nasazení na základě služby Active Directory Federation Services (AD FS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

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
* [Vývoj šablon pro Azure zásobníku](user/azure-stack-develop-templates.md)
* [Nasazení šablon pomocí PowerShellu](user/azure-stack-deploy-template-powershell.md)
