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
ms.date: 11/02/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 4128f5e389ac6e35f863f02ff5b09ff96fadc527
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962482"
---
# <a name="register-azure-stack-with-azure"></a>Registrace Azure Stack s Azure

Registrace Azure Stack s Azure můžete k stažení položek z marketplace z Azure a jak nastavit obchodní data hlášení zpět společnosti Microsoft. Po dokončení registrace Azure Stack, využití se oznamuje službě Azure commerce a vy vidíte v rámci předplatného k registraci použít.

Informace v tomto článku popisuje registrace integrované systémy Azure Stack s Azure. Informace o registraci ASDK s využitím Azure najdete v tématu [registrace Azure Stack](.\asdk\asdk-register.md) v dokumentaci k ASDK.

> [!IMPORTANT]  
> Chcete-li podporovat všechny funkce služby Azure Stack, včetně nabídka položek na webu Marketplace je nutná registrace. Kromě toho budete v rozporu se licenční podmínky, pokud nezaregistrujete při použití modelu fakturace platit jako využití služby Azure Stack. Další informace o službě Azure Stack licenční modely, najdete v tématu [jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Požadavky

Předtím, než zaregistrujete, budete potřebovat následující na místě:

 - Ověřit vaše přihlašovací údaje
 - Nastavte jazykový režim prostředí PowerShell
 - Instalace Powershellu pro Azure Stack
 - Stáhněte si nástroje Azure Stack
 - Určit váš scénář registrace

### <a name="verify-your-credentials"></a>Ověřit vaše přihlašovací údaje

Před zaregistrováním služby Azure Stack s Azure, musíte mít:

- ID předplatného pro předplatné Azure. Pouze EA, CSP nebo CSP sdílené služby, který odběry podporují registrace. Poskytovatelé CSP se muset rozhodnout, jestli se má [použít jiné předplatné, CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>K získání ID, přihlaste se k Azure, klikněte na tlačítko **všechny služby**. Potom v části **Obecné** vyberte **předplatná**, klikněte na předplatné, které chcete použít, a v části **Essentials** najdete ID předplatného.

  > [!Note]  
  > Německo Cloudová předplatná se momentálně nepodporují.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného.

- Uživatelský účet musí mít přístup k předplatnému Azure a máte oprávnění k vytváření aplikací identity a instanční objekty v adresáři spojený s tímto předplatným.

- Zaregistrovat poskytovatele prostředků služby Azure Stack (viz zaregistrovat poskytovatele prostředků Azure Stack níže v části Podrobnosti).

Po registraci není potřeba oprávnění globálního správce Azure Active Directory. Některé operace však může vyžadovat přihlašovací údaje globálního správce. Například skript instalační program zprostředkovatele prostředků nebo nová funkce vyžaduje oprávnění bylo uděleno. Můžete dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatné globální správce účtu, který je vlastníkem *výchozí předplatné poskytovatele*.

Pokud nemáte předplatné Azure, které splňuje tyto požadavky, můžete si [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack se neúčtují žádné poplatky na vaše předplatné Azure.

### <a name="powershell-language-mode"></a>Režim jazyka prostředí PowerShell

Zajištění úspěšné registrace Azure Stack, musí být nastavena na režim jazyka PowerShell **FullLanguageMode**.  Pokud chcete ověřit, že aktuální režim jazyka nastaven na úplné, otevřete okno Powershellu se zvýšenými oprávněními a spusťte následující rutiny Powershellu:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Zkontrolujte výstup vrací **FullLanguageMode**. Pokud se vrátí další režim jazyka, registrace bude potřeba spustit na jiném počítači nebo režim jazyka muset nastavit na **FullLanguageMode** než budete pokračovat.

### <a name="install-powershell-for-azure-stack"></a>Instalace Powershellu pro Azure Stack

Budete muset použít nejnovější PowerShell pro Azure Stack registraci v Azure.

Pokud ještě není nainstalovaný na nejnovější verzi, přečtěte si téma [instalace Powershellu pro Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Stáhněte si nástroje Azure Stack

Úložiště GitHub Azure Stack nástroje obsahuje moduly Powershellu, které podporují funkce služby Azure Stack. včetně registrace funkcí. Během procesu registrace budete muset importovat a používat **RegisterWithAzure.psm1** modul prostředí PowerShell, nalezen v úložišti Azure Stack nástroje pro registraci vaší instance služby Azure Stack v Azure.

Pokud chcete mít jistotu, že používáte nejnovější verzi, byste měli odstranit všechny existující verze nástroje Azure Stack a [stáhnout nejnovější verzi z Githubu](azure-stack-powershell-download.md) před registrací s Azure.

### <a name="determine-your-registration-scenario"></a>Určit váš scénář registrace

Nasazení Azure Stack může být *připojené* nebo *odpojení*.

 - **Připojení**  
 Připojení znamená, že jste nasadili Azure Stack, tak, aby se může připojit k Internetu a do Azure. Buď je Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) pro úložiště identit. U připojených nasazení, můžete si vybrat z dva modely fakturace: platit jako využití nebo na základě kapacity.
    - [Zaregistrovat Azure s využitím propojené služby Azure Stack **platit jako využití** model fakturace](#register-connected-with-pay-as-you-go-billing)
    - [Zaregistrovat Azure s využitím propojené služby Azure Stack **kapacity** model fakturace](#register-connected-with-capacity-billing)

 - **Odpojení**  
 S odpojené od možnost nasazení v Azure, můžete nasadit a používat Azure Stack bez připojení k Internetu. Odpojené nasazení jste ale omezeni na úložiště identity služby AD FS a model fakturace založená na kapacitě.
    - [Registrace odpojeného pomocí služby Azure Stack **kapacity** model fakturace ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Určení názvu jedinečným registračním použití 
Při registraci služby Azure Stack v Azure, je nutné zadat název jedinečný registrace. Snadný způsob, jak přidružit Azure registraci předplatného Azure Stack je použití služby Azure Stack **ID cloudu**. 

> [!NOTE]
> Azure Stack registrace pomocí modelu fakturace na základě kapacity bude nutné změnit jedinečný název, při registraci znovu po vypršení platnosti těchto ročních předplatných, pokud jste [odstranit vypršela platnost registrace](azure-stack-registration.md#change-the-subscription-you-use) a znovu zaregistrovat. Azure.

Pokud chcete zjistit ID cloudu pro vaše nasazení Azure Stack, otevřete PowerShell jako správce v počítači, než můžete přístup k privilegovaným koncový bod, spusťte následující příkazy a zaznamenejte **CloudID** hodnotu: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registrace připojených s průběžnými platbami fakturace

Tyto kroky použijte k registraci ve službě Azure s využitím fakturace modelu plateb jako využití služby Azure Stack.

> [!Note]  
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod (období). Podrobnosti o období najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

Propojené prostředí můžete přístup k Internetu a z Azure. U těchto prostředí budete muset v Azure, zaregistrujte poskytovatele prostředků služby Azure Stack a potom nakonfigurujte váš model fakturace.

1. Registraci poskytovatele prostředků služby Azure Stack v Azure, spusťte prostředí PowerShell ISE jako správce a pomocí následujících rutin prostředí PowerShell s **EnvironmentName** parametr nastaven na typ příslušné předplatné Azure (viz níže uvedené parametry).

2. Přidáte účet Azure, který používáte k registraci Azure Stack. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí předplatného cloudu Azure. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud se používá k předplatnému Azure Čína **AzureChinaCloud**.  |

3. Pokud máte více předplatných, spuštěním následujícího příkazu vyberte předplatné, že které chcete použít:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků služby Azure Stack ve vašem předplatném Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack-Tools-master** vytvořen adresář, kdy jste [stáhnout nástroje Azure Stack](#bkmk_tools). Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. V dalším kroku ve stejné relaci Powershellu, ujistěte se, že jste přihlášeni k správný kontext Azure Powershellu. Toto je účet azure, který se použije k registraci poskytovatele prostředků služby Azure Stack výše. Prostředí PowerShell pro spuštění:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

7. Ve stejné relaci prostředí PowerShell, spusťte **Set-AzsRegistration** rutiny. Prostředí PowerShell pro spuštění:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Další informace o rutině Set-AzsRegistration najdete v tématu [Reference k registraci](#registration-reference).

  Tento proces bude trvat 10 až 15 minut. Po dokončení příkazu se zobrazí zpráva **"prostředí je teď zaregistrované a aktivovat pomocí zadaných parametrů."**

## <a name="register-connected-with-capacity-billing"></a>Registrace připojených s využitím fakturace kapacity

Tyto kroky použijte k registraci ve službě Azure s využitím fakturace modelu plateb jako využití služby Azure Stack.

> [!Note]  
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod (období). Podrobnosti o období najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

Propojené prostředí můžete přístup k Internetu a z Azure. U těchto prostředí budete muset v Azure, zaregistrujte poskytovatele prostředků služby Azure Stack a potom nakonfigurujte váš model fakturace.

1. Registraci poskytovatele prostředků služby Azure Stack v Azure, spusťte prostředí PowerShell ISE jako správce a pomocí následujících rutin prostředí PowerShell s **EnvironmentName** parametr nastaven na typ příslušné předplatné Azure (viz níže uvedené parametry).

2. Přidáte účet Azure, který používáte k registraci Azure Stack. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí předplatného cloudu Azure. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud se používá k předplatnému Azure Čína **AzureChinaCloud**.  |

3. Pokud máte více předplatných, spuštěním následujícího příkazu vyberte předplatné, že které chcete použít:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků služby Azure Stack ve vašem předplatném Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack-Tools-master** vytvořen adresář, kdy jste [stáhnout nástroje Azure Stack](#bkmk_tools). Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > Zakážete generování sestav s parametrem UsageReportingEnabled pro použití **Set-AzsRegistration** rutiny nastavením parametru na hodnotu false. 
   
  Další informace o rutině Set-AzsRegistration najdete v tématu [Reference k registraci](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrace odpojeného s využitím fakturace kapacity

Pokud při registraci služby Azure Stack v odpojeném prostředí (bez připojení k Internetu), musíte k získání tokenu registrace z prostředí Azure Stack a pak pomocí tohoto tokenu v počítači, který se můžete připojit k Azure a má [prostředí PowerShell pro službu Azure Stack nainstalované](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure Stack

1. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack-Tools-master** vytvořen adresář, kdy jste [stáhnout nástroje Azure Stack](#bkmk_tools). Import **RegisterWithAzure.psm1** modul:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Registrační token získáte spuštěním následující rutiny prostředí PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Další informace o rutině Get-AzsRegistrationToken najdete v tématu [Reference k registraci](#registration-reference).

   > [!Tip]  
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

  > [!Note]  
  > Uložte název prostředku registrace a registrační token pro budoucí použití.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Načtení aktivační kód z prostředku registrace služby Azure

Dále je třeba načíst z registrace prostředku v Azure vytvořit během registrace AzsEnvironment aktivační kód.

Aktivační klíč získáte spuštěním následující rutiny prostředí PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Aktivační kód je uložen v souboru určeném pro *$KeyOutputFilePath*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvoření prostředku Aktivace ve službě Azure Stack

Vraťte se do prostředí Azure Stack pomocí souboru nebo text od aktivace klíče vytvořeného z Get-AzsActivationKey. Dále vytvoříte prostředek aktivace ve službě Azure Stack pomocí této aktivační kód. K aktivaci prostředků vytvoříte spuštěním následující rutiny prostředí PowerShell:  

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
2. Vyberte **všechny služby**a potom v části **správu** vyberte **Marketplace správu** > **přidat z Azure**.

Pokud se zobrazí seznam položek, které jsou k dispozici od Azure (např. WordPress), s aktivací bylo úspěšné. V odpojených prostředích položky Azure marketplace v Tržišti Azure Stack neuvidíte.

> [!Note]  
> Po dokončení registrace se už zobrazí aktivní upozornění pro registraci není.

## <a name="renew-or-change-registration"></a>Prodloužení nebo změnit registrace

### <a name="renew-or-change-registration-in-connected-environments"></a>Prodloužení nebo změnit registrace v připojených prostředích

Budete muset aktualizovat nebo obnovit vaše registrace v následujících případech:

- Po obnovení roční předplatné založená na kapacitě.
- Pokud změníte model fakturace.
- Při vertikálním navýšení změny (přidávat nebo odebírat uzly) pro fakturaci na základě kapacity.

#### <a name="change-the-subscription-you-use"></a>Změnit předplatné, které používáte

Pokud chcete změnit předplatné používáte, je třeba nejprve spustit **odebrat AzsRegistration** rutiny, zajistěte jste přihlášeni do správného kontextu prostředí Azure PowerShell a nakonec spusťte **Set AzsRegistration**  se všechny změněné parametry, včetně \<model fakturace\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Změňte model fakturace nebo jak nabídnout funkce

Pokud chcete změnit model fakturace nebo jak nabízí funkce pro vaši instalaci, můžete volat funkci registrace k nastavení nových hodnot. Není nutné nejprve odebrat aktuální registrace:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Prodloužení nebo změnit registrace v odpojených prostředích

Budete muset aktualizovat nebo obnovit vaše registrace v následujících případech:

- Po obnovení roční předplatné založená na kapacitě.
- Pokud změníte model fakturace.
- Při vertikálním navýšení změny (přidávat nebo odebírat uzly) pro fakturaci na základě kapacity.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Odebrání prostředku Aktivace služby Azure Stack

Nejdřív je potřeba odebrat prostředek aktivace z Azure Stack a potom prostředku registrace v Azure.  

Pokud chcete odebrat aktivace prostředků ve službě Azure Stack, spusťte následující rutiny prostředí PowerShell v prostředí Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

V dalším kroku se odebrat prostředek registrace v Azure, ujistěte se v Azure připojené počítače, přihlaste se k správný kontext Azure Powershellu a spusťte odpovídající rutiny prostředí PowerShell, jak je popsáno níže.

Registrační token použitý k vytvoření prostředku můžete použít:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Nebo můžete použít název registrace:

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Proveďte registraci znovu pomocí odpojené kroky

Jste nyní zcela neregistrovaná v odpojeném scénáři a nutné opakovat kroky pro registraci prostředí Azure Stack v odpojeném scénář.

### <a name="disable-or-enable-usage-reporting"></a>Zakázání nebo povolení generování sestav o využívání

Pro prostředí Azure Stack, které používají model fakturace kapacity vypnout používání vytváření sestav pomocí **UsageReportingEnabled** buď pomocí parametru **Set-AzsRegistration** nebo  **Get-AzsRegistrationToken** rutiny. Azure Stack sestavy metriky využití ve výchozím nastavení. Operátory se používá kapacity nebo podpora odpojené prostředí se musí si vypnout generování sestav o využívání.

#### <a name="with-a-connected-azure-stack"></a>Pomocí připojené služby Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Odpojené Azure Stack

1. Chcete-li změnit registračního tokenu, spusťte následující rutiny prostředí PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Registrační token se uloží do souboru určeného pro *$FilePathForRegistrationToken*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

2. Uložit tento registrační token pro použití v Azure připojené počítače. Z $FilePathForRegistrationToken můžete zkopírovat soubor nebo text.

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace
Přesunutí prostředku registrace mezi skupinami prostředků ve stejném předplatném **je** podporovány pro všechna prostředí. Ale přesunutí prostředku registrace mezi předplatnými je podporováno pouze pro zprostředkovatele kryptografických služeb pokud obě předplatná přeložit na stejné ID partnera. Další informace o přesunutí prostředků do nové skupiny prostředků, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Reference k registraci

### <a name="set-azsregistration"></a>Set-AzsRegistration

Set-AzsRegistration můžete použít k registraci Azure Stack v Azure a povolení nebo zakázání nabídka položek v marketplace a generování sestav o využívání.

Pokud chcete spustit rutinu, budete potřebovat:
- Předplatné Azure globální libovolného typu.
- Musíte také být přihlášeni k Azure Powershellu pomocí účtu, který je vlastníkem nebo přispěvatelem předplatného.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Parametr | Typ | Popis |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Pověření používaná k [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Řetězec | Předem nakonfigurované vzdálenou konzolu Powershellu, který vám poskytne funkce, jako je shromažďování protokolů a další příspěvek úlohy nasazení. Další informace najdete [pomocí privilegovaných koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) článku. |
| AzureContext | PSObject |  |
| ResourceGroupName | Řetězec |  |
| ResourceGroupLocation | Řetězec |  |
| BillingModel | Řetězec | Model fakturace předplatného používá. Povolené jsou hodnoty pro tento parametr: kapacity, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled |  |  |
| UsageReportingEnabled | True nebo False | Azure Stack sestavy metriky využití ve výchozím nastavení. Operátory se používá kapacity nebo podpora odpojené prostředí se musí si vypnout generování sestav o využívání. Povolené jsou hodnoty pro tento parametr: hodnotu True, False. |
| AgreementNumber | Řetězec |  |
| registrationName | Řetězec | Nastavit jedinečný název pro registraci, pokud používáte skript registrace na více než jednu instanci služby Azure Stack pomocí stejného předplatného Azure ID. Parametr má výchozí hodnotu **AzureStackRegistration**. Nicméně pokud použijete stejný název ve více než jednu instanci služby Azure Stack, skript se nezdaří. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken vygeneruje registrační token ze vstupních parametrů.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametr | Typ | Popis |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Pověření používaná k [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Řetězec |  Předem nakonfigurované vzdálenou konzolu Powershellu, který vám poskytne funkce, jako je shromažďování protokolů a další příspěvek úlohy nasazení. Další informace najdete [pomocí privilegovaných koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) článku. |
| AzureContext | PSObject |  |
| ResourceGroupName | Řetězec |  |
| ResourceGroupLocation | Řetězec |  |
| BillingModel | Řetězec | Model fakturace předplatného používá. Povolené jsou hodnoty pro tento parametr: kapacity, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | True nebo False |  |
| UsageReportingEnabled | True nebo False | Azure Stack sestavy metriky využití ve výchozím nastavení. Operátory se používá kapacity nebo podpora odpojené prostředí se musí si vypnout generování sestav o využívání. Povolené jsou hodnoty pro tento parametr: hodnotu True, False. |
| AgreementNumber | Řetězec |  |


## <a name="next-steps"></a>Další postup

[Stažení položek z marketplace z Azure](azure-stack-download-azure-marketplace-item.md)
