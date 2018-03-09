---
title: "Zaregistrovat Azure zásobníku | Microsoft Docs"
description: "Zaregistrujte Azure zásobníku ve vašem předplatném Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/27/2018
ms.author: jeffgilb
ms.openlocfilehash: 8d520e1917d63b56a2adf3823ddbb741721fe751
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registrace Azure zásobníku u vašeho předplatného Azure

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete zaregistrovat [zásobník Azure](azure-stack-poc.md) s Azure můžete stáhnout z Azure marketplace položky a nastavit obchodování při generování sestav dat zpět do společnosti Microsoft.

> [!NOTE]
>Registrace je doporučená, protože umožňuje otestovat důležité zásobník Azure funkcí, jako je syndikace marketplace a vytváření sestav využití. Po registraci Azure zásobníku využití oznamovány obchodu Spojených států v Azure. Zobrazí se v rámci předplatného, které jste použili pro registraci. Azure uživatelé Development Kit zásobníku nejsou účtovat žádné využití, které patří.


## <a name="get-azure-subscription"></a>Získání předplatného Azure

Před registrací zásobník Azure s Azure, musíte mít:

- ID předplatného pro předplatné Azure. Chcete-li získat ID, přihlaste se k Azure, klikněte na **další služby** > **odběry**, klikněte na předplatné, které chcete použít, a v části **Essentials** můžete najít **ID předplatného**. Čína, Německu a US government Cloudová předplatná nejsou aktuálně podporovány.
- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného (podporovány jsou účty MSA nebo 2FA).
- *Nepožadováno počínaje verzí aktualizace 1712 zásobník Azure (180106.1):* Azure Active Directory pro předplatné Azure. Tento adresář můžete najít v Azure podržením ukazatele nad miniatury v pravém horním rohu portálu Azure.

Pokud nemáte předplatné Azure, který splňuje tyto požadavky, můžete [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/en-us/free/?b=17.06). Registrace Azure zásobníku způsobuje bez nákladů na vaše předplatné Azure.

## <a name="register-azure-stack-with-azure"></a>Zaregistrovat Azure zásobník Azure  
> [!NOTE]
> Všechny tyto kroky musí spustit z počítače, který má přístup k privilegované koncový bod. Pro Azure zásobníku Development Kit je hostitelský počítač. Pokud používáte integrovaný systém, obraťte se na vaše operátor zásobník Azure.
>

1. Otevřete konzolu prostředí PowerShell jako správce a [nainstalujte prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).  

2. Přidáte účet Azure, který použijete k registraci Azure zásobníku. Chcete-li přidat účet, spusťte `Add-AzureRmAccount` rutiny s parametrem EnvironmentName nastavena na **AzureCloud**. Zobrazí se výzva k zadání přihlašovacích údajů účtu Azure a možná budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Pokud máte více předplatných, spusťte následující příkaz a vyberte ten, že který chcete použít:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spusťte následující příkaz pro registraci poskytovatele prostředků zásobník Azure ve vašem předplatném Azure:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Odstranit všechny existující verze moduly Powershellu, které odpovídají registrace a [stáhněte nejnovější verzi z webu GitHub](azure-stack-powershell-download.md).  

6. Z adresáře "AzureStack-nástroje master", který je vytvořen v předchozím kroku přejděte do složky "Registrace" a naimportujte modul ".\RegisterWithAzure.psm1":  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. Ve stejné relaci prostředí PowerShell, spusťte následující skript: zadejte po zobrazení výzvy k zadání pověření `azurestack\cloudadmin` jako uživatel a heslo je stejné jako to, co můžete používat pro místní správce během nasazení.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Parametr | Popis |  
   |--------|-------------|
   | CloudAdminCredential | Přihlašovací údaje domény cloudu, které slouží jako [přístup privilegované koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu  **\<zásobník Azure domény\>\cloudadmin**. Pro development kit, uživatelské jméno je nastavena na **azurestack\cloudadmin**. Pokud používáte integrovaný systém, obraťte se na vaše zásobník Azure operátor získat tuto hodnotu.|  
   | PrivilegedEndpoint | Předem nakonfigurovaná vzdáleného prostředí PowerShell konzoly, která poskytuje funkce jako je shromažďování protokolů a jiné post úlohy nasazení. Pro development kit privilegované koncový bod je hostován na virtuálním počítači "AzS-ERCS01". Pokud používáte integrovaný systém, obraťte se na vaše zásobník Azure operátor získat tuto hodnotu. Další informace naleznete [pomocí privilegované koncový bod](azure-stack-privileged-endpoint.md) článku.|  
   | BillingModel | Fakturační model, který používá vaše předplatné. Povolené hodnoty pro tento parametr se - **kapacity**, **PayAsYouUse**, a **vývoj**. Pro development kit, tato hodnota nastavena na **vývoj**. Pokud používáte integrovaný systém, obraťte se na vaše zásobník Azure operátor získat tuto hodnotu. |

8. Po dokončení skriptu, zobrazí se zpráva "aktivace Azure zásobníku (Tento krok může trvat až 10 minut)."




## <a name="verify-the-registration"></a>Ověření registrace  

1. Přihlaste se k portálu správce (https://adminportal.local.azurestack.external).

2. Klikněte na tlačítko **další služby** > **Marketplace správu** > **přidat z Azure**.

3. Pokud se zobrazí seznam položek, které jsou k dispozici prostřednictvím Azure (např. WordPress), vaše Aktivace proběhla úspěšně.

> [!NOTE]
> Po dokončení registrace se nebude zobrazovat aktivní upozornění pro není registraci.


## <a name="modify-the-registration"></a>Upravit registraci

### <a name="change-the-subscription-you-use"></a>Změnit předplatné, které můžete použít
Pokud chcete změnit předplatné, které používáte, musí nejdřív spustit odebrat AzsRegistration, ujistěte se, že jste se přihlásili na správný kontext prostředí Azure PowerShell a spusťte sadu AzsRegistration se všechny změněné parametry.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Změna funkce fakturační model nebo syndikace
Pokud chcete změnit model fakturace nebo syndikace funkcí pro instalaci, můžete volat funkci registrace nastavit nové hodnoty. Není nutné nejdříve odebrat aktuální registrace.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Odpojené registrace
*Informace v této části platí počínaje aktualizovanou verzi 1712 zásobník Azure (180106.1) a není podporován s předchozími verzemi.*

Pokud registrujete Azure zásobníku v odpojeném prostředí, budete muset získat token registrace z prostředí zásobníku Azure a pak použít tento token na počítači, který může připojit k Azure pro registraci.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure zásobníku
  1. Chcete-li získat registrační token, spusťte následující:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Token registrace je uložen v souboru určeném pro *$env:SystemDrive\RegistrationToken.txt*.

  2. Uložte tento token registrace pro použití v Azure připojený počítač.


### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace
Spusťte kroky pro tento postup na počítači, který může připojit k Azure.

  1. [Stáhněte si nejnovější moduly Powershellu, které odpovídají registrace z Githubu](azure-stack-powershell-download.md).  

  2. Z adresáře "AzureStack-nástroje master", který je vytvořen v předchozím kroku přejděte do složky "Registrace" a naimportujte modul ".\RegisterWithAzure.psm1":  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Kopírování [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) do složky, jako je *C:\Temp*.

  4. Spusťte prostředí PowerShell ISE jako správce a pak importovat modul RegisterWithAzure.  

  5. Ujistěte se, že jste přihlášeni ke správné prostředí Azure PowerShell kontext, který chcete použít k registraci prostředí Azure zásobníku:  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Zadejte registrační token pro registraci s Azure:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Uložte názvu prostředku registrace, nebo token registrace pro budoucí použití.

### <a name="remove-a-registered-resource"></a>Odeberte registrované prostředek
Pokud chcete odebrat registrace prostředků, musíte použít UnRegister-AzsEnvironment a předat název prostředku registrace nebo token registrace, které jste použili pro AzsEnvironment registrace.
- **Název prostředku registrace:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Registrační token:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>Další postup
[Připojení ke službě Azure Stack](azure-stack-connect-azure-stack.md)
