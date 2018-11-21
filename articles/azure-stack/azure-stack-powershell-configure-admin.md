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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 530b2a1909ec198ddff5abfe4fd5bb7c645f7582
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261578"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Připojení ke službě Azure Stack pomocí prostředí PowerShell jako operátor

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete nakonfigurovat služby Azure Stack pomocí Powershellu ke správě prostředků, jako je například vytváření nabídek, plánů, kvóty a výstrahy. Toto téma vám pomůže nakonfigurovat prostředí operátora.

## <a name="prerequisites"></a>Požadavky

Spuštění následujících požadovaných součástí, buď z [vývojová sada](.\asdk\asdk-connect.md#connect-with-rdp) nebo z Windows na základě externí klienta po [připojené k ASDK prostřednictvím sítě VPN](.\asdk\asdk-connect.md#connect-with-vpn). 

 - Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](azure-stack-powershell-install.md).  
 - Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurace prostředí operátor a přihlaste se ke službě Azure Stack

Konfigurace prostředí Azure Stack operátor pomocí Powershellu. Spusťte jeden z následujících skriptů: nahraďte tenantName Azure AD, koncový bod GraphAudience a hodnoty ArmEndpoint s konfigurací prostředí.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>Otestovat připojení

Teď, když máte všechno, co nastavení, pomocí prostředí PowerShell vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

 - [Vývoj šablon pro Azure Stack](user/azure-stack-develop-templates.md)
 - [Nasazení šablon pomocí PowerShellu](user/azure-stack-deploy-template-powershell.md)