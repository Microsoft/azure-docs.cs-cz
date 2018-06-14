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
ms.openlocfilehash: 86608ef8b3623682bd10498605f8b7b62c377ff1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076466"
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
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
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
