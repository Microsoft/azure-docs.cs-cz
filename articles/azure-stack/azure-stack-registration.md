---
title: Registrace Azure pro Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje proces registrace Azure pro nasazení na víc uzlů připojená k Azure zásobník Azure.
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
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 0d8c1ebe9688f32c460ef689119313b0682a0a68
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="register-azure-stack-with-azure"></a>Zaregistrovat Azure zásobník Azure
Registrace [zásobník Azure](azure-stack-poc.md) s Azure vám umožní stáhnout položky marketplace z Azure a nastavit obchodování při generování sestav dat zpět do společnosti Microsoft. Po registraci Azure zásobníku využití údajně Azure obchodování a zobrazí se v rámci předplatného použít pro registraci. 

> [!IMPORTANT]
> Registrace je povinný, pokud se rozhodnete fakturační model platím jako--používání. Jinak bude v porušení licenční podmínky pro nasazení Azure zásobníku jako využití, nebudou ohlášena.

## <a name="prerequisites"></a>Požadavky
Před registrací zásobník Azure s Azure, musíte mít:

- ID předplatného pro předplatné Azure. Chcete-li získat ID, přihlaste se k Azure, klikněte na **další služby** > **odběry**, klikněte na předplatné, které chcete použít, a v části **Essentials** můžete najít ID předplatného. 

  > [!NOTE]
  > Německo a US Government Cloudová předplatná nejsou aktuálně podporovány.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného (podporovány jsou účty MSA nebo 2FA).
- Registrovaný poskytovatel prostředků Azure zásobníku (viz část registrace poskytovatele prostředků Azure zásobníku níže podrobnosti).

Pokud nemáte předplatné Azure, který splňuje tyto požadavky, můžete [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure zásobníku způsobuje bez nákladů na vaše předplatné Azure.

### <a name="bkmk_powershell"></a>Instalace prostředí PowerShell pro Azure zásobníku
Budete muset použít nejnovější prostředí PowerShell pro Azure zásobníku při registraci s Azure.

Pokud ještě není nainstalován, [nainstalujte prostředí PowerShell pro Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Stažení nástroje Azure zásobníku
Úložiště GitHub nástroje Azure zásobníku obsahuje moduly Powershellu, které podporují funkce Azure zásobníku; včetně registrace funkcí. Během procesu registrace, budete muset importovat a používat modul RegisterWithAzure.psm1 PowerShell nalezen v úložišti Azure zásobníku nástroje pro registraci vaší instance zásobník Azure s Azure. 

Aby používáte nejnovější verzi, byste měli odstranit všechny existující verze nástroje Azure zásobníku a [stáhnout nejnovější verzi z webu GitHub](azure-stack-powershell-download.md) před registrací v Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Zaregistrovat Azure zásobníku v připojených prostředích
Připojených prostředích získat přístup k Internetu a Azure. U těchto prostředí musíte registrovat poskytovatele prostředků zásobník Azure s Azure a pak nakonfigurujte model fakturace.

> [!NOTE]
> Všechny tyto kroky nutné spustit z počítače, který má přístup k privilegované koncový bod. 

### <a name="register-the-azure-stack-resource-provider"></a>Registrace poskytovatele prostředků Azure zásobníku
Registrace poskytovatele prostředků zásobník Azure s Azure, spusťte prostředí PowerShell ISE jako správce a použijte následující příkazy prostředí PowerShell s **EnvironmentName** parametr nastaven na typ příslušné předplatné Azure (viz níže uvedených parametrů). 

1. Přidáte účet Azure, který použijete k registraci Azure zásobníku. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny. Zobrazí se výzva k zadání přihlašovacích údajů účtu globálního správce služby Azure a možná budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název odběru prostředí cloudu Azure. Názvy podporovaných prostředí jsou **AzureCloud** nebo, pokud používáte Číně předplatné Azure, **AzureChinaCloud**.  |
   |  |  |

2. Pokud máte více předplatných, spusťte následující příkaz a vyberte ten, že který chcete použít:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Spusťte následující příkaz pro registraci poskytovatele prostředků zásobník Azure ve vašem předplatném Azure:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Zaregistrovat Azure zásobník Azure pomocí fakturační model platím jako jste – použití
Tyto kroky použijte k registraci zásobník Azure s Azure pomocí fakturační model platím jako--používání.

1. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack. nástroje pro hlavní** adresář vytvořen, když jste [stažení nástroje Azure zásobníku](#bkmk_tools). Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell: 

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. V dalším kroku ve stejné relaci prostředí PowerShell, ujistěte se, že jste se přihlásili na správný kontext Azure PowerShell. Toto je účet azure, která byla použita k registraci poskytovatele prostředků Azure zásobníku výše. Spusťte prostředí PowerShell: 

  ```powershell 
  Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>" 
  ``` 

3. Ve stejné relaci prostředí PowerShell, spusťte **Set-AzsRegistration** rutiny. Spusťte prostředí PowerShell:  

  ```powershell
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
  ```

  |Parametr|Popis|
  |-----|-----|
  |PrivilegedEndpointCredential|Pověření použitá k [přístup privilegované koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Předem nakonfigurovaná vzdáleného prostředí PowerShell konzoly, která poskytuje funkce jako je shromažďování protokolů a jiné post úlohy nasazení. Další informace naleznete [pomocí privilegované koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) článku.|
  |BillingModel|Fakturační model, který používá vaše předplatné. Povolené hodnoty tohoto parametru jsou: kapacitu, PayAsYouUse a vývoj.|
  |  |  |

  Proces bude trvat 10 až 15 minut. Po dokončení příkazu, zobrazí se zpráva **"prostředí je nyní zaregistrovaná a aktivována pomocí zadané parametry."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Zaregistrovat Azure zásobník Azure pomocí fakturační model kapacity
Postupujte podle stejných pokynů používaná pro registraci pomocí platím jako--používání fakturační model, ale přidat číslo smlouvy, pod kterým byl zakoupen kapacitu a změnit **BillingModel** parametru **kapacity**. Všechny ostatní parametry jsou stejné.

Spusťte prostředí PowerShell:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Zaregistrovat Azure zásobníku v odpojených prostředích 
*Informace v této části platí počínaje aktualizovanou verzi 1712 zásobník Azure (180106.1) a není podporován s předchozími verzemi.*

Pokud registrujete v odpojeném prostředí Azure zásobníku (s bez připojení k Internetu), budete muset získat token registrace z prostředí zásobníku Azure a pak použít tento token v počítači, který může připojit k Azure a má [prostředí PowerShell pro nainstalována sada Azure](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure zásobníku

1. Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack. nástroje pro hlavní** adresář vytvořen, když jste [stažení nástroje Azure zásobníku](#bkmk_tools). Import **RegisterWithAzure.psm1** modul:  

  ```powershell 
  Import-Module .\RegisterWithAzure.psm1 
  ``` 

2. Chcete-li získat token registrace, spusťte následující příkazy prostředí PowerShell:  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```
  
  > [!TIP]  
  > Token registrace je uložen v souboru určeném pro *$FilePathForRegistrationToken*. Cesta k souboru nebo názvu souboru můžete změnit svého uvážení. 

3. Uložte tento token registrace pro použití v Azure připojený počítač. Soubor nebo text můžete zkopírovat z $FilePathForRegistrationToken.


### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace
V počítači, který je internetové připojení proveďte stejný postup při importu modulu RegisterWithAzure.psm1 a přihlaste se na správný kontext prostředí Azure Powershell. Potom volání Register-AzsEnvironment a zadejte registrační token pro registraci s Azure:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
  > [!NOTE]  
  > Uložte názvu prostředku registrace a registrační token pro budoucí použití.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Načíst aktivační klíč z prostředku registrace Azure 
Dále musíte získat aktivační klíč z prostředku registrace během registrace AzsEnvironment vytvořen v Azure. 
 
Chcete-li získat aktivační klíč, spusťte následující příkazy prostředí PowerShell:  

  ```Powershell 
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>" 
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt" 
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath 
  ``` 
  > [!TIP]   
  > Aktivační klíč je uložen v souboru určeném pro *$KeyOutputFilePath*. Cesta k souboru nebo názvu souboru můžete změnit svého uvážení. 

### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvořte prostředek aktivace v Azure zásobníku 
Vrátit do prostředí Azure zásobníku se soubory nebo text z vytvořené z Get-AzsActivationKey aktivační klíč. Dále vytvoříte prostředek aktivace v zásobníku Azure pomocí této aktivační klíč. Pokud chcete vytvořit prostředek aktivace, spusťte následující příkazy prostředí PowerShell:  

  ```Powershell 
  $ActivationKey = "<activation key>" 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token: 

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>' 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

## <a name="verify-azure-stack-registration"></a>Ověření registrace Azure zásobníku
Tyto kroky použijte k ověření, že Azure zásobníku úspěšně zaregistrovala s Azure.
1. Přihlaste se k Azure zásobníku [portálu správce](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*.
2. Klikněte na tlačítko **další služby** > **Marketplace správu** > **přidat z Azure**.

Pokud se zobrazí seznam položek, které jsou k dispozici prostřednictvím Azure (např. WordPress), vaše Aktivace proběhla úspěšně. V odpojených prostředích položky Azure marketplace v zásobníku Azure marketplace neuvidíte.

> [!NOTE]
> Po dokončení registrace se nebude zobrazovat aktivní upozornění pro není registraci.

## <a name="renew-or-change-registration"></a>Obnovení nebo změňte registrace
### <a name="renew-or-change-registration-in-connected-environments"></a>Obnovení nebo změňte registrace v připojených prostředích
Budete muset aktualizovat nebo obnovení registrace v následujících případech:
- Po obnovení roční předplatné na základě kapacity.
- Pokud změníte model fakturace.
- Při změně měřítka změny (Přidání nebo odebrání uzlů) pro fakturaci na základě kapacity.

#### <a name="change-the-subscription-you-use"></a>Změnit předplatné, které můžete použít
Pokud chcete změnit předplatné použijete, musíte nejprve spustit **odebrat AzsRegistration** rutiny, pak se ujistěte, jste se přihlásili na správný kontext prostředí Azure PowerShell a nakonec spusťte **Set-AzsRegistration**  s žádným změnit parametry:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Změna funkce fakturační model nebo syndikace
Pokud chcete změnit model fakturace nebo syndikace funkcí pro instalaci, můžete volat funkci registrace nastavit nové hodnoty. Není nutné nejdříve odebrat aktuální registrace: 

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Obnovení nebo změňte registrace v odpojených prostředích 
Budete muset aktualizovat nebo obnovení registrace v následujících případech: 
- Po obnovení roční předplatné na základě kapacity. 
- Pokud změníte model fakturace. 
- Při změně měřítka změny (Přidání nebo odebrání uzlů) pro fakturaci na základě kapacity. 

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Odebrat prostředek aktivace z Azure zásobníku 
Musíte se nejdřív odeberte aktivace prostředků ze zásobníku Azure a pak registrace prostředků v Azure.  

Odebrat prostředek aktivace v zásobníku Azure, spusťte následující příkazy prostředí PowerShell v prostředí Azure zásobníku:  

  ```Powershell 
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint 
  ``` 

V dalším kroku odebrat prostředek registrace v Azure, zkontrolujte je v Azure připojené počítače, přihlaste se na správný kontext prostředí Azure PowerShell a spusťte příslušnými příkazy prostředí PowerShell, jak je popsáno níže.

Můžete použít k vytvoření prostředku tokenu registrace:  

  ```Powershell 
  $registrationToken = "<registration token>" 
  Unregister-AzsEnvironment -RegistrationToken $registrationToken 
  ``` 
  
Nebo můžete použít název registrace: 

  ```Powershell 
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>" 
  Unregister-AzsEnvironment -RegistrationName $registrationName 
  ``` 

### <a name="re-register-using-disconnected-steps"></a>Znovu zaregistrovat pomocí odpojené kroky 
Máte nyní zcela neregistrovaná v odpojeném scénáři a musí zopakujte kroky pro registraci ve scénáři odpojené prostředí Azure zásobníku. 

## <a name="next-steps"></a>Další postup

[Stažení položky marketplace z Azure](azure-stack-download-azure-marketplace-item.md)
