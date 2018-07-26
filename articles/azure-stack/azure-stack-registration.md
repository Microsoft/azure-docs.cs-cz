---
title: Integrované systémy pro Azure registrace pro Azure Stack | Dokumentace Microsoftu
description: Popisuje proces registrace služby Azure pro nasazení na víc uzlů Azure stacku Azure připojené.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: a5e31df435d5e9af8543301e7a4540faa3d6410f
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258061"
---
# <a name="register-azure-stack-with-azure"></a>Registrace Azure Stack s Azure

Registrace [Azure Stack](azure-stack-poc.md) s Azure můžete k stažení položek z marketplace z Azure a jak nastavit obchodní data hlášení zpět společnosti Microsoft. Po dokončení registrace Azure Stack, využití se oznamuje službě Azure commerce a vy vidíte v rámci předplatného k registraci použít.

> [!IMPORTANT]  
> Chcete-li podporovat všechny funkce služby Azure Stack, včetně syndikace marketplace je nutná registrace. Kromě toho budete v rozporu se licenční podmínky, pokud nezaregistrujete při použití modelu fakturace platit jako využití služby Azure Stack. Další informace o službě Azure Stack licenční modely, najdete v tématu [jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Požadavky

Před zaregistrováním služby Azure Stack s Azure, musíte mít:

- ID předplatného pro předplatné Azure. K získání ID, přihlaste se k Azure, klikněte na tlačítko **další služby** > **předplatná**, klikněte na předplatné, které chcete použít, a v části **Essentials** najdete ID předplatného.

  > [!NOTE]
  > Německo a předplatné cloudu pro státní správu USA nejsou aktuálně podporovány.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného (účty MSA/2FA jsou podporovány).
- Zaregistrovat poskytovatele prostředků služby Azure Stack (viz zaregistrovat poskytovatele prostředků Azure Stack níže v části Podrobnosti).

Pokud nemáte předplatné Azure, které splňuje tyto požadavky, můžete si [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack se neúčtují žádné poplatky na vaše předplatné Azure.

### <a name="powershell-language-mode"></a>Režim jazyka prostředí PowerShell

Zajištění úspěšné registrace Azure Stack, musí být nastavena na režim jazyka PowerShell **FullLanguageMode**.  Pokud chcete ověřit, že aktuální režim jazyka nastaven na úplné, otevřete okno Powershellu se zvýšenými oprávněními a spusťte následující příkazy Powershellu:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Zkontrolujte výstup vrací **FullLanguageMode**. Pokud se vrátí další režim jazyka, registrace bude potřeba spustit na jiném počítači nebo režim jazyka muset nastavit na **FullLanguageMode** než budete pokračovat.

### <a name="bkmk_powershell"></a>Instalace Powershellu pro Azure Stack

Budete muset použít nejnovější PowerShell pro Azure Stack registraci v Azure.

Pokud ještě není nainstalované, [instalace Powershellu pro Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Stáhněte si nástroje Azure Stack

Úložiště GitHub Azure Stack nástroje obsahuje moduly Powershellu, které podporují funkce služby Azure Stack. včetně registrace funkcí. Během procesu registrace budete muset importovat a používat modul Powershellu RegisterWithAzure.psm1 nalezen v úložišti Azure Stack nástroje pro registraci vaší instance služby Azure Stack v Azure.

Pokud chcete mít jistotu, že používáte nejnovější verzi, byste měli odstranit všechny existující verze nástroje Azure Stack a [stáhnout nejnovější verzi z Githubu](azure-stack-powershell-download.md) před registrací s Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Zaregistrovat v připojených prostředích Azure Stack

Propojené prostředí můžete přístup k Internetu a z Azure. U těchto prostředí budete muset v Azure, zaregistrujte poskytovatele prostředků služby Azure Stack a potom nakonfigurujte váš model fakturace.

> [!NOTE]
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod.

### <a name="register-the-azure-stack-resource-provider"></a>Registrace poskytovatele prostředků služby Azure Stack

Registraci poskytovatele prostředků služby Azure Stack v Azure, spusťte prostředí PowerShell ISE jako správce a použijte následující příkazy prostředí PowerShell s **EnvironmentName** parametr nastaven na typ příslušné předplatné Azure (viz níže uvedené parametry).

1. Přidáte účet Azure, který používáte k registraci Azure Stack. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny. Zobrazí se výzva k zadání přihlašovacích údajů k účtu globálního správce služby Azure a budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí předplatného cloudu Azure. Názvy prostředí podporované jsou **AzureCloud** nebo pokud se používá k předplatnému Azure Čína **AzureChinaCloud**.  |
   |  |  |

2. Pokud máte více předplatných, spuštěním následujícího příkazu vyberte předplatné, že které chcete použít:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků služby Azure Stack ve vašem předplatném Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrace Azure Stack pomocí Azure s využitím modelu fakturace platit jako využití

Tyto kroky použijte k registraci ve službě Azure s využitím fakturace modelu plateb jako využití služby Azure Stack.

1. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack-Tools-master** vytvořen adresář, kdy jste [stáhnout nástroje Azure Stack](#bkmk_tools). Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. V dalším kroku ve stejné relaci Powershellu, ujistěte se, že jste přihlášeni k správný kontext Azure Powershellu. Toto je účet azure, který se použije k registraci poskytovatele prostředků služby Azure Stack výše. Prostředí PowerShell pro spuštění:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. Ve stejné relaci prostředí PowerShell, spusťte **Set-AzsRegistration** rutiny. Prostředí PowerShell pro spuštění:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
      -RegistrationName $RegistrationName
   ```

  |Parametr|Popis|
  |-----|-----|
  |PrivilegedEndpointCredential|Pověření používaná k [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Předem nakonfigurované vzdálenou konzolu Powershellu, který vám poskytne funkce, jako je shromažďování protokolů a další příspěvek úlohy nasazení. Další informace najdete [pomocí privilegovaných koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) článku.|
  |BillingModel|Model fakturace předplatného používá. Povolené jsou hodnoty pro tento parametr: kapacity, PayAsYouUse a vývoj.|
  | registrationName | Nastavit jedinečný název pro registraci, pokud používáte skript registrace na více než jednu instanci služby Azure Stack pomocí stejného předplatného Azure ID. Parametr má výchozí hodnotu **AzureStackRegistration**. Nicméně pokud použijete stejný název ve více než jednu instanci služby Azure Stack, skript se nezdaří. |

  Tento proces bude trvat 10 až 15 minut. Po dokončení příkazu se zobrazí zpráva **"prostředí je teď zaregistrované a aktivovat pomocí zadaných parametrů."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrace Azure Stack pomocí Azure s využitím modelu fakturace kapacity

Použijte stejné pokyny pro registraci pomocí fakturační model plateb jako využití, ale přidejte číslo smlouvy, pod kterým byl zakoupen kapacity a změňte **BillingModel** parametr **kapacity**. Všechny ostatní parametry jsou beze změny.

Prostředí PowerShell pro spuštění:

```PowerShell  
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
$RegistrationName = "<unique-registration-name>"
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
    -RegistrationName $RegistrationName
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrace Azure Stack v odpojeném prostředí
Pokud při registraci služby Azure Stack v odpojeném prostředí (bez připojení k Internetu), musíte k získání tokenu registrace z prostředí Azure Stack a pak pomocí tohoto tokenu v počítači, který se můžete připojit k Azure a má [prostředí PowerShell pro službu Azure Stack nainstalované](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure Stack

1. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack-Tools-master** vytvořen adresář, kdy jste [stáhnout nástroje Azure Stack](#bkmk_tools). Import **RegisterWithAzure.psm1** modul:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Pokud chcete získat registrační token, spusťte následující příkazy Powershellu:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > Registrační token se uloží do souboru určeného pro *$FilePathForRegistrationToken*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

3. Uložit tento registrační token pro použití v Azure připojené počítače. Z $FilePathForRegistrationToken můžete zkopírovat soubor nebo text.

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace

Na počítači, který je připojený k Internetu proveďte stejné kroky pro import modulu RegisterWithAzure.psm1 a přihlaste se k správný kontext Azure Powershellu. Potom zavolejte AzsEnvironment registru. Zadejte token registrace k registraci ve službě Azure. Pokud registrujete více než jednu instanci služby Azure Stack pomocí stejného ID předplatného Azure, zadejte název jedinečné registrace. Spusťte následující rutinu:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!NOTE]  
  > Uložte název prostředku registrace a registrační token pro budoucí použití.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Načtení aktivační kód z prostředku registrace služby Azure

Dále je třeba načíst z registrace prostředku v Azure vytvořit během registrace AzsEnvironment aktivační kód.

Pokud chcete získat aktivační klíč, spusťte následující příkazy Powershellu:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > Aktivační kód je uložen v souboru určeném pro *$KeyOutputFilePath*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvoření prostředku Aktivace ve službě Azure Stack

Vraťte se do prostředí Azure Stack pomocí souboru nebo text od aktivace klíče vytvořeného z Get-AzsActivationKey. Dále vytvoříte prostředek aktivace ve službě Azure Stack pomocí této aktivační kód. K vytvoření prostředku Aktivace, spusťte následující příkazy Powershellu:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Ověření registrace Azure Stack

Pomocí těchto kroků můžete ověřit, že Azure Stack je úspěšně zaregistrovaný s Azure.

1. Přihlaste se ke službě Azure Stack [portálu správce](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*.
2. Vyberte **další služby** > **Marketplace správu** > **z Azure**.

Pokud se zobrazí seznam položek, které jsou k dispozici od Azure (např. WordPress), s aktivací bylo úspěšné. V odpojených prostředích položky Azure marketplace v Tržišti Azure Stack neuvidíte.

> [!NOTE]
> Po dokončení registrace se už zobrazí aktivní upozornění pro registraci není.

## <a name="renew-or-change-registration"></a>Prodloužení nebo změnit registrace

### <a name="renew-or-change-registration-in-connected-environments"></a>Prodloužení nebo změnit registrace v připojených prostředích

Budete muset aktualizovat nebo obnovit vaše registrace v následujících případech:

- Po obnovení roční předplatné založená na kapacitě.
- Pokud změníte model fakturace.
- Při vertikálním navýšení změny (přidávat nebo odebírat uzly) pro fakturaci na základě kapacity.

#### <a name="change-the-subscription-you-use"></a>Změnit předplatné, které používáte

Pokud chcete změnit předplatné používáte, je třeba nejprve spustit **odebrat AzsRegistration** rutiny, zajistěte jste přihlášeni do správného kontextu prostředí Azure PowerShell a nakonec spusťte **Set AzsRegistration**  s žádným změnit parametry:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Změna funkcí fakturace modelu nebo syndikace

Pokud chcete změnit model fakturace nebo syndikace funkce pro vaši instalaci, můžete volat funkci registrace k nastavení nových hodnot. Není nutné nejprve odebrat aktuální registrace:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Prodloužení nebo změnit registrace v odpojených prostředích

Budete muset aktualizovat nebo obnovit vaše registrace v následujících případech:

- Po obnovení roční předplatné založená na kapacitě.
- Pokud změníte model fakturace.
- Při vertikálním navýšení změny (přidávat nebo odebírat uzly) pro fakturaci na základě kapacity.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Odebrání prostředku Aktivace služby Azure Stack

Nejdřív je potřeba odebrat prostředek aktivace z Azure Stack a potom prostředku registrace v Azure.  

Pokud chcete odebrat aktivace prostředků ve službě Azure Stack, spusťte následující příkazy prostředí PowerShell v prostředí Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

V dalším kroku se odebrat prostředek registrace v Azure, ujistěte se v Azure připojené počítače, přihlaste se k správný kontext Azure Powershellu a spusťte odpovídající příkazy Powershellu, jak je popsáno níže.

Registrační token použitý k vytvoření prostředku můžete použít:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Nebo můžete použít název registrace:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Proveďte registraci znovu pomocí odpojené kroky

Jste nyní zcela neregistrovaná v odpojeném scénáři a nutné opakovat kroky pro registraci prostředí Azure Stack v odpojeném scénář.

## <a name="next-steps"></a>Další postup

[Stažení položek z marketplace z Azure](azure-stack-download-azure-marketplace-item.md)
