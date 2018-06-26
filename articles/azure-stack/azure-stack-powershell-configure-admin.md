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
ms.date: 06/22/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 74a5a9408a78dd0da12fb3f8ed721774030cc438
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749857"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Konfigurace prostředí Azure PowerShell zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete nakonfigurovat zásobník Azure pomocí prostředí PowerShell ke správě prostředků, jako je například vytváření nabídek, plánů, kvóty a upozornění. Toto téma vám pomůže nakonfigurovat prostředí operátor.

## <a name="prerequisites"></a>Požadavky

Spusťte následující předpoklady, některý z [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).  
 - Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurace prostředí operátor a přihlaste se k Azure zásobníku

Konfigurace prostředí Azure zásobníku operátor pomocí prostředí PowerShell. Spusťte jeden z následujících skriptů: tenantName Azure AD, koncový bod GraphAudience a ArmEndpoint hodnoty nahraďte konfiguraci prostředí.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Testovací připojení

Teď, když máte všechno nastavení, pomocí prostředí PowerShell vytvořit prostředky v rámci Azure zásobníku. Můžete například vytvořit skupinu prostředků pro aplikace a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

 - [Vývoj šablon pro Azure zásobníku](user/azure-stack-develop-templates.md)
 - [Nasazení šablon pomocí PowerShellu](user/azure-stack-deploy-template-powershell.md)
