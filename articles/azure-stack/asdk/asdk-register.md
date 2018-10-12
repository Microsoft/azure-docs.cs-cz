---
title: Zaregistrujte ASDK v Azure | Dokumentace Microsoftu
description: Popisuje postup registrace Azure Stack s Azure a umožňuje syndikace marketplace a generování sestav o využívání.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 16d775b15c9e4ec8898d5c7e498299288f1c37c2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113428"
---
# <a name="azure-stack-registration"></a>Registrace Azure Stack
Instalaci sady Azure Stack Development Kit (ASDK) můžete zaregistrovat pomocí Azure pro stažení položek z marketplace z Azure a jak nastavit obchodní data hlášení zpět společnosti Microsoft. Chcete-li podporovat všechny funkce služby Azure Stack, včetně syndikace marketplace je nutná registrace. Registrace se doporučuje, protože umožňuje testovat důležité funkce služby Azure Stack, jako jsou syndikace marketplace a generování sestav o využívání. Po dokončení registrace Azure Stack, využití se oznamuje službě Azure commerce. Zobrazí se v rámci předplatného, které jste použili k registraci. Však uživatelé ASDK neúčtují se za jakékoliv využití, které vykazují.

Pokud vaše ASDK nezaregistrujete, může se zobrazit **vyžadována aktivace** výstražné upozornění, s výzvou k registraci Azure Stack Development Kit. Jde o očekávané chování.

## <a name="prerequisites"></a>Požadavky
Před použitím těchto pokynů k registraci ASDK ve službě Azure, ujistěte se, že máte nainstalované Azure Stack Powershellu a stáhnout nástroje Azure Stack, jak je popsáno v [konfigurace po nasazení](asdk-post-deploy.md) článku.

Kromě toho režim jazyka PowerShell nastavené na **FullLanguageMode** v počítači používá k registraci ASDK v Azure. Pokud chcete ověřit, že aktuální režim jazyka nastaven na úplné, otevřete okno Powershellu se zvýšenými oprávněními a spusťte následující příkazy Powershellu:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Zkontrolujte výstup vrací **FullLanguageMode**. Pokud se vrátí další režim jazyka, registrace bude muset být spuštěn na jiném počítači nebo režim jazyka muset nastavit na **FullLanguageMode** než budete pokračovat.

## <a name="register-azure-stack-with-azure"></a>Registrace Azure Stack s Azure
Postupujte podle těchto kroků k registraci ASDK ve službě Azure.

> [!NOTE]
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod. Pro ASDK, který je hostitelský počítač development kit.

1. Otevřete konzolu Powershellu jako správce.  

2. Spusťte následující příkazy Powershellu registraci ASDK instalace v Azure. Musíte se přihlásit k vašemu předplatnému Azure a místní instalace ASDK. Pokud nemáte předplatné Azure, ale můžete [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack se neúčtují žádné poplatky na vaše předplatné Azure.<br><br>Pokud spustíte skript registrace na více než jednu instanci služby Azure Stack pomocí stejného ID předplatného Azure, nastavit jedinečný název pro registraci při spuštění **Set-AzsRegistration** rutiny. **RegistrationName** parametr má výchozí hodnotu **AzureStackRegistration**. Nicméně pokud použijete stejný název ve více než jednu instanci služby Azure Stack, skript se nezdaří.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    $UsageReporting = $true # Set to $false if using the Capacity Billing model
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -EnableUsageReporting:$UsageReporting
    ```
3. Po dokončení skriptu by se zobrazit tato zpráva: **prostředí je teď zaregistrované a aktivovat pomocí zadaných parametrů.**

    ![Prostředí je teď zaregistrovaný.](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Ověřte, že registrace byla úspěšná
Postupujte podle těchto kroků zkontrolujte, že proběhla úspěšně ASDK registrace v Azure.

1. Přihlaste se k [portál pro správu služby Azure Stack](https://adminportal.local.azurestack.external).

2. Klikněte na tlačítko **Marketplace správu** > **z Azure**.

    ![](media/asdk-register/2.PNG)

3. Pokud se zobrazí seznam položek, které jsou k dispozici v Azure, s aktivací bylo úspěšné.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace
Přesunutí prostředku registrace mezi skupinami prostředků ve stejném předplatném **je** podporována. Další informace o přesunutí prostředků do nové skupiny prostředků, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Další postup
[Přidání položky marketplace služby Azure Stack](.\.\azure-stack-marketplace.md)
