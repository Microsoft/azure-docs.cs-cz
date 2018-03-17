---
title: Zaregistrovat ASDK Azure | Microsoft Docs
description: "Popisuje postup registrace zásobník Azure pomocí Azure marketplace syndikace a vytváření sestav využití povolit."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Zaregistrovat Azure zásobník Azure
Instalace služby Azure zásobníku Development Kit (ASDK) můžete zaregistrovat s Azure můžete stáhnout z Azure marketplace položky a nastavit obchodování při generování sestav dat zpět do společnosti Microsoft. Registrace je doporučená, protože umožňuje otestovat důležité funkce Azure zásobníku jako syndikace marketplace a vytváření sestav využití. Po registraci Azure zásobníku využití oznamovány obchodu Spojených států v Azure. Zobrazí se v rámci předplatného, které jste použili pro registraci. Uživatelé ASDK však nejsou účtovat žádné využití, které patří.


## <a name="register-azure-stack-with-azure"></a>Zaregistrovat Azure zásobník Azure 
Postupujte podle těchto kroků k registraci ASDK s Azure.

> [!NOTE]
> Všechny tyto kroky nutné spustit z počítače, který má přístup k privilegované koncový bod. Pro ASDK, která se development kit hostitelský počítač. 

Před použitím těchto kroků k registraci ASDK s Azure, ujistěte se, že máte nainstalovaný Azure PowerShell zásobníku a nástroje Azure zásobníku stáhnout, jak je popsáno v [konfiguraci po nasazení](asdk-post-deploy.md) článku. 

1. Otevřete konzolu prostředí PowerShell jako správce.  

2. Spusťte následující příkazy prostředí PowerShell při registraci vaší instalace ASDK s Azure, (budete potřebovat k přihlášení do vašeho předplatného Azure a místní instalaci ASDK):

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)