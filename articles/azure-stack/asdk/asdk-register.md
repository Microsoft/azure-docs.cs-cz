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
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d8c4d28d6f5fdcc66e512375448f4b1d5fc9b8ed
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359166"
---
# <a name="azure-stack-registration"></a>Registrace Azure Stack
Instalaci sady Azure Stack Development Kit (ASDK) můžete zaregistrovat pomocí Azure pro stažení položek z marketplace z Azure a jak nastavit obchodní data hlášení zpět společnosti Microsoft. Chcete-li podporovat všechny funkce služby Azure Stack, včetně syndikace marketplace je nutná registrace. Umožňuje otestovat důležité funkce služby Azure Stack, jako jsou syndikace marketplace a generování sestav o využívání, je nutná registrace. Po dokončení registrace Azure Stack, využití se oznamuje službě Azure commerce. Zobrazí se v rámci předplatného, které jste použili k registraci. Však uživatelé ASDK neúčtují se za jakékoliv využití, které vykazují.

Pokud vaše ASDK nezaregistrujete, může se zobrazit **vyžadována aktivace** výstražné upozornění, s výzvou k registraci Azure Stack Development Kit. Jde o očekávané chování.

## <a name="prerequisites"></a>Požadavky
Před použitím těchto pokynů k registraci ASDK ve službě Azure, ujistěte se, že máte nainstalované Azure Stack Powershellu a stáhnout nástroje Azure Stack, jak je popsáno v [konfigurace po nasazení](asdk-post-deploy.md) článku.

Kromě toho režim jazyka PowerShell nastavené na **FullLanguageMode** v počítači používá k registraci ASDK v Azure. Pokud chcete ověřit, že aktuální režim jazyka nastaven na úplné, otevřete okno Powershellu se zvýšenými oprávněními a spusťte následující příkazy Powershellu:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Zkontrolujte výstup vrací **FullLanguageMode**. Pokud se vrátí další režim jazyka, registrace bude muset být spuštěn na jiném počítači nebo režim jazyka muset nastavit na **FullLanguageMode** než budete pokračovat.

Účet Azure AD pro registraci potřeby umožňuje mít přístup k předplatnému Azure a máte oprávnění k vytváření aplikací identity a instanční objekty v adresáři spojený s tímto předplatným. Doporučujeme registraci Azure Stack v Azure pomocí správy nejnižších možných oprávnění [vytvořit účet služby pro registraci](..\azure-stack-registration-role.md) místo použití přihlašovací údaje globálního správce.

## <a name="register-azure-stack-with-azure"></a>Registrace Azure Stack s Azure
Postupujte podle těchto kroků k registraci ASDK ve službě Azure.

> [!NOTE]
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod. Pro ASDK, který je hostitelský počítač development kit.

1. Otevřete konzolu Powershellu jako správce.  

2. Spusťte následující příkazy Powershellu registraci ASDK instalace v Azure. Musíte se přihlásit k vašemu předplatnému Azure a místní instalace ASDK. Pokud nemáte předplatné Azure, ale můžete [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack se neúčtují žádné poplatky na vaše předplatné Azure.<br><br>Nastavit jedinečný název pro registraci při spuštění **Set-AzsRegistration** rutiny. **RegistrationName** parametr má výchozí hodnotu **AzureStackRegistration**. Nicméně pokud použijete stejný název ve více než jednu instanci služby Azure Stack, skript se nezdaří.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Po dokončení skriptu, byste měli vidět tuto zprávu: **Prostředí je teď zaregistrované a aktivovat pomocí zadaných parametrů.**

    ![Prostředí je teď zaregistrovaný.](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Zaregistrovat v odpojených prostředích
Pokud při registraci služby Azure Stack v odpojeném prostředí (bez připojení k Internetu), musíte k získání tokenu registrace z prostředí Azure Stack a pak pomocí tohoto tokenu v počítači, který lze připojit k Azure a zaregistrujte a vytvořte aktivace prostředek pro vaše prostředí ASDK.
 
 > [!IMPORTANT]
 > Před použitím těchto pokynů k registraci Azure Stack, ujistěte se, že máte nainstalované prostředí PowerShell pro Azure Stack a stáhnout nástroje Azure Stack, jak je popsáno v [konfigurace po nasazení](asdk-post-deploy.md) článek věnovaný tomu ASDK hostitele počítače a počítače s přístupem k Internetu pro připojení k Azure a zaregistrujte.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure Stack
V hostitelském počítači ASDK spusťte PowerShell jako správce a přejděte **registrace** složky v **AzureStack-Tools-master** adresář vytvořený při stažení nástroje Azure Stack. Pomocí následujících příkazů prostředí PowerShell k importu **RegisterWithAzure.psm1** modul a poté **Get-AzsRegistrationToken** rutiny pro získání tokenu registrace:  

   ```PowerShell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Uložte tento registrační token pro použití na počítače připojené k Internetu. Ze souboru vytvořený parametr $FilePathForRegistrationToken můžete zkopírovat soubor nebo text.

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace
Na Internetu připojený počítač, použijte následující příkazy prostředí PowerShell k importu **RegisterWithAzure.psm1** modul a poté **Register-AzsEnvironment** rutiny k registraci ve službě Azure s využitím a jedinečným registračním název registračního tokenu, který jste právě vytvořili:  

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Alternativně můžete použít **Get-Content** rutiny pro odkazování na soubor, který obsahuje registrační token:

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Po dokončení registrace se zobrazí zpráva podobná **prostředí si Azure Stack je teď registrované v Azure.**

> [!IMPORTANT]
> Nezavírejte okno prostředí PowerShell. 

Uložte registrační token a registrace – název prostředku pro budoucí použití.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Načtení aktivační kód z prostředku registrace služby Azure

Pořád používá počítače připojeného k Internetu **a stejné okno konzole Powershellu**, načtěte aktivační kód z registrace prostředku vytvořen po registraci v Azure.

Chcete-li získat aktivační klíč, spusťte následující příkazy prostředí PowerShell, použijte stejnou hodnotu název jedinečný registrace, který jste zadali při registraci v Azure v předchozím kroku:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvoření prostředku Aktivace ve službě Azure Stack

Vraťte se do prostředí Azure Stack se souborem nebo text z aktivace klíče vytvořené z **Get-AzsActivationKey**. Spusťte následující příkazy prostředí PowerShell a vytvoří prostředek aktivace ve službě Azure Stack pomocí tohoto klíče aktivace:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Alternativně můžete použít **Get-Content** rutiny pro odkazování na soubor, který obsahuje registrační token:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Po dokončení aktivace, zobrazí se zpráva podobná této **prostředí dokončí proces registrace a aktivace.**

## <a name="verify-the-registration-was-successful"></a>Ověřte, že registrace byla úspěšná

Můžete použít **Správa oblastí** dlaždici a ověřte, že Azure Stack registrace byla úspěšná. Tato dlaždice není k dispozici na výchozí řídicí panel portálu správce.

1. Přihlaste se k [portál pro správu služby Azure Stack](https://adminportal.local.azurestack.external).

2. Z řídicího panelu, vyberte **Správa oblastí**.

    [ ![Dlaždice oblasti správy](media/asdk-register/admin1sm.png "dlaždice oblasti správy") ](media/asdk-register/admin1.png#lightbox)

3. Vyberte **vlastnosti**. Toto okno zobrazuje stav a podrobnosti o vašem prostředí. Stav může být **registrované** nebo **Neregistrovaný**. Pokud je zaregistrován, také ukazuje, ID předplatného Azure, který jste použili k registraci služby Azure Stack, spolu s registrace skupiny prostředků a názvem.

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace
Přesunutí prostředku registrace mezi skupinami prostředků ve stejném předplatném **je** podporována. Další informace o přesunutí prostředků do nové skupiny prostředků, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Další postup

- [Přidání položky marketplace služby Azure Stack](../azure-stack-marketplace.md)
