---
title: "Registrace Azure pro Azure zásobníku integrované systémy | Microsoft Docs"
description: "Popisuje proces registrace Azure pro nasazení na víc uzlů připojená k Azure zásobník Azure."
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
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 27bd44f936e19890526c0834e14084647dcec086
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="register-azure-stack-with-azure"></a>Zaregistrovat Azure zásobník Azure
Zásobník Azure můžete zaregistrovat s Azure můžete stáhnout z Azure marketplace položky a nastavit obchodování při generování sestav dat zpět do společnosti Microsoft. Po registraci Azure zásobníku využití oznamovány obchodu Spojených států v Azure. Zobrazí se v rámci předplatného, které jste použili pro registraci.

> [!IMPORTANT]
> Registrace je povinný, pokud se rozhodnete fakturační model platím jako--používání. Jinak bude narušuje licenční podmínky pro nasazení Azure zásobníku jako využití nebudou oznámeny v opačném případě.

## <a name="before-you-register-azure-stack-with-azure"></a>Předtím, než zaregistrujete zásobník Azure s Azure
Před registrací zásobník Azure s Azure, musíte mít:

- ID předplatného pro předplatné Azure. Chcete-li získat ID, přihlaste se k Azure, klikněte na **další služby** > **odběry**, klikněte na předplatné, které chcete použít, a v části **Essentials** můžete najít ID předplatného. 

  > [!NOTE]
  > Čína, Německu a US government Cloudová předplatná nejsou aktuálně podporovány. 

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného (podporovány jsou účty MSA nebo 2FA)
- *Nepožadováno počínaje verzí aktualizace 1712 zásobník Azure (180106.1)*: Azure AD pro předplatné Azure. Tento adresář můžete najít v Azure podržením ukazatele nad miniatury v pravém horním rohu portálu Azure. 
- Registrovaný poskytovatel prostředků Azure zásobníku (viz část registrace poskytovatele prostředků Azure zásobníku níže podrobnosti)

Pokud nemáte předplatné Azure, který splňuje tyto požadavky, můžete [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure zásobníku způsobuje bez nákladů na vaše předplatné Azure.

### <a name="bkmk_powershell"></a>Instalace prostředí PowerShell pro Azure zásobníku
Budete muset použít nejnovější prostředí PowerShell pro Azure zásobníku při registraci v systému s Azure.

Pokud ještě není nainstalován, [nainstalujte prostředí PowerShell pro Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Stažení nástroje Azure zásobníku
Úložiště GitHub nástroje Azure zásobníku obsahuje moduly Powershellu, které podporují funkce Azure zásobníku; včetně registrace funkcí. Během registrace proces, který budete muset importovat a používat modul RegisterWithAzure.psm1 PowerShell nalezen v úložišti Azure zásobníku nástroje pro registraci vaší instance zásobník Azure s Azure. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Zaregistrovat Azure zásobníku v připojených prostředích
Připojených prostředích získat přístup k Internetu a Azure. U těchto prostředí musíte registrovat poskytovatele prostředků zásobník Azure s Azure a pak nakonfigurujte model fakturace.

### <a name="register-the-azure-stack-resource-provider"></a>Registrace poskytovatele prostředků Azure zásobníku
Registrace poskytovatele prostředků zásobník Azure s Azure, spusťte prostředí Powershell ISE jako správce a použijte následující příkazy prostředí PowerShell. Budou tyto příkazy:
- Vyzvat vás k přihlášení jako vlastníka předplatného Azure k použití a nastavit `EnvironmentName` parametru **AzureCloud**.
- Registrace zprostředkovatele prostředků Azure **Microsoft.AzureStack**.

Spusťte prostředí PowerShell:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Zaregistrovat Azure zásobník Azure pomocí fakturační model platím jako jste – použití
Použijte tyto kroky pro registraci zásobník Azure s Azure pomocí fakturační model platím jako--používání.

Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack. nástroje pro hlavní** adresář vytvořen, když jste [stažení nástroje Azure zásobníku](#bkmk_tools). Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell: 

Spusťte prostředí PowerShell:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
V dalším kroku ve stejné relaci prostředí PowerShell, spusťte **Set-AzsRegistration** rutiny. Po zobrazení výzvy k zadání pověření zadejte vlastníka předplatného Azure.  

Spusťte prostředí PowerShell:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Parametr|Popis|
|-----|-----|
|CloudAdminCredential|Objekt prostředí PowerShell, který obsahuje informace o pověření (uživatelské jméno a heslo) pro vlastníka předplatného Azure.|
|PrivilegedEndpoint|Předem nakonfigurovaná vzdáleného prostředí PowerShell konzoly, která poskytuje funkce jako je shromažďování protokolů a jiné post úlohy nasazení. Další informace naleznete [pomocí privilegované koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) článku.|
|BillingModel|Fakturační model, který používá vaše předplatné. Povolené hodnoty tohoto parametru jsou: kapacitu, PayAsYouUse a vývoj.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Zaregistrovat Azure zásobník Azure pomocí fakturační model kapacity
Postupujte podle stejných pokynů používaná pro registraci pomocí platím jako--používání fakturační model, ale přidat číslo smlouvy, pod kterým byl zakoupen kapacitu a změnit `BillingModel` parametru **kapacity**. Všechny ostatní parametry jsou stejné.

Spusťte prostředí PowerShell:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Zaregistrovat Azure zásobníku v odpojených prostředích 
*Informace v této části platí počínaje aktualizovanou verzi 1712 zásobník Azure (180106.1) a není podporován s předchozími verzemi.*

Pokud registrujete v odpojeném prostředí Azure zásobníku (s bez připojení k Internetu), budete muset získat token registrace z prostředí zásobníku Azure a pak použít tento token v počítači, který může připojit k Azure a má [prostředí PowerShell pro nainstalována sada Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure zásobníku
  1. Chcete-li získat token registrace, spusťte následující příkazy prostředí PowerShell:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Token registrace je uložen v souboru určeném pro *$env:SystemDrive\RegistrationToken.txt*.

  2. Uložte tento token registrace pro použití v Azure připojený počítač.


### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace
Spusťte prostředí PowerShell ISE jako správce a přejděte do **registrace** složky v **AzureStack. nástroje pro hlavní** adresář vytvořen, když jste [stažení nástroje Azure zásobníku](#bkmk_tools). Import **RegisterWithAzure.psm1** modul: 

Spusťte prostředí PowerShell:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
V dalším kroku ve stejné relaci prostředí PowerShell, zadejte registrační token pro registraci s Azure:

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
> Uložte názvu prostředku registrace, nebo token registrace pro budoucí použití.

## <a name="verify-azure-stack-registration"></a>Ověření registrace Azure zásobníku
Tyto kroky použijte k ověření, že Azure zásobníku úspěšně zaregistrovala s Azure.
1. Přihlaste se k Azure zásobníku [portálu správce](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*.
2. Klikněte na tlačítko **další služby** > **Marketplace správu** > **přidat z Azure**.

Pokud se zobrazí seznam položek, které jsou k dispozici prostřednictvím Azure (např. WordPress), vaše Aktivace proběhla úspěšně.

> [!NOTE]
> Po dokončení registrace se nebude zobrazovat aktivní upozornění pro není registraci.

## <a name="renew-or-change-registration"></a>Obnovení nebo změňte registrace
Budete muset aktualizovat nebo obnovení registrace v následujících případech:
- Po obnovení roční předplatné na základě kapacity.
- Pokud změníte model fakturace.
- Při změně měřítka změny (Přidání nebo odebrání uzlů) pro fakturaci na základě kapacity.

### <a name="change-the-subscription-you-use"></a>Změnit předplatné, které můžete použít
Pokud chcete změnit předplatné použijete, musíte nejprve spustit **odebrat AzsRegistration** rutiny, pak se ujistěte, jste se přihlásili na správný kontext prostředí Azure PowerShell a nakonec spusťte **Set-AzsRegistration**  s žádným změnit parametry:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Změna funkce fakturační model nebo syndikace
Pokud chcete změnit model fakturace nebo syndikace funkcí pro instalaci, můžete volat funkci registrace nastavit nové hodnoty. Není nutné nejdříve odebrat aktuální registrace: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Odeberte registrované prostředek
Pokud chcete odebrat registrace, pak musíte použít **UnRegister-AzsEnvironment** rutiny a předejte jí název prostředku registraci nebo registraci token jste použili pro **Register-AzsEnvironment**.

Postup odebrání registrace pomocí názvu prostředku:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Postup odebrání registrace pomocí tokenu registrace:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>Další postup

[Externí monitorování integrace](azure-stack-integrate-monitor.md)