---
title: Řešení potíží způsobených aplikacemi, které nepodporují protokol TLS 1,2 | Microsoft Docs
description: Řešení potíží způsobených aplikacemi, které nepodporují protokol TLS 1,2
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270868"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Řešení potíží s aplikacemi, které nepodporují protokol TLS 1,2
Tento článek popisuje, jak povolit starší protokoly TLS (TLS 1,0 a 1,1) a jak používat starší šifrovací sady pro podporu dalších protokolů na webu a rolích pracovních procesů cloudové služby v systému Windows 2019 Server. 

Chápeme, že při provádění kroků k vyřazení TLS 1,0 a TLS 1,1 můžou naši zákazníci potřebovat podporu starších protokolů a šifrovacích sad, dokud je nemůžou naplánovat jejich vyřazení.  I když tyto starší hodnoty nedoporučujeme znovu povolit, poskytujeme pokyny pro pomoc zákazníkům. Zákazníkům doporučujeme, aby před implementací změn uvedených v tomto článku vyhodnotili riziko regrese. 

> [!NOTE]
> Hostované operační systémy řady 6 vysazují protokol TLS 1,2 zakázáním šifry 1.0/1.1. 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Vyřazení podpory pro TLS 1,0, TLS 1,1 a starší šifrovací sady 
V případě podpory našeho úsilí o použití vysoce integrovaného šifrování je Microsoft oznámil plán na zahájení migrace mimo TLS 1,0 a 1,1 v červnu 2017.   Od tohoto počátečního oznámení společnost Microsoft oznámila náš záměr zakázat zabezpečení TLS (Transport Layer Security) 1,0 a 1,1 ve výchozím nastavení v podporovaných verzích Microsoft Edge a Internet Exploreru 11 v první polovině sady 2020.  Podobná oznámení od společnosti Apple, Google a Mozilla označují směr, ve kterém se odvětví zaznamenalo.   

## <a name="tls-configuration"></a>Konfigurace TLS  
Bitová kopie Windows serveru 2019 cloudového serveru je nakonfigurovaná s TLS 1,0 a TLS 1,1 disabled na úrovni registru. To znamená, že aplikace nasazené do této verze systému Windows a použití služby Windows Stack pro vyjednávání TLS nebudou umožňovat komunikaci TLS 1,0 a TLS 1,1.   

Na serveru se nachází také omezená sada šifrovacích sad: 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>Krok 1: Vytvoření skriptu PowerShellu pro povolení TLS 1,0 a TLS 1,1 

Následující kód použijte jako příklad k vytvoření skriptu, který umožňuje použití starších protokolů a šifrovacích sad. Pro účely této dokumentace bude tento skript název: **TLSsettings. ps1**. Uložte tento skript na místní plochu, abyste měli snadný přístup v pozdějších krocích. 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>Krok 2: vytvoření souboru příkazů 

Pomocí níže uvedeného příkazu vytvořte soubor CMD s názvem **RunTLSSettings. cmd** . Uložte tento skript na místní plochu, abyste měli snadný přístup v pozdějších krocích. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Krok 3: Přidejte úlohu po spuštění do definice služby role (csdef). 

Do existujícího souboru definice služby přidejte následující fragment kódu. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Tady je příklad, který ukazuje roli pracovního procesu i webovou roli. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>Krok 5: Přidání skriptů do cloudové služby 

1) V aplikaci Visual Studio klikněte pravým tlačítkem na svoji webrole.
2) Vyberte **Přidat**.
3) Vybrat **existující položku**
4) V Průzkumníku souborů přejděte do počítače, kam jste uložili soubory **TLSsettings. ps1** a **RunTLSSettings. cmd.** 
5) Vyberte dva soubory, které chcete přidat do projektu Cloud Services

## <a name="step-6-enable-copy-to-output-directory"></a>Krok 6: povolení kopírování do výstupního adresáře

Chcete-li zajistit, aby se skripty nahrály pomocí každé aktualizace nabízené ze sady Visual Studio, je třeba nastavit *kopírování do výstupního adresáře* *vždy na Kopírovat* .

1) V části vaše webrole klikněte pravým tlačítkem na RunTLSSettings. cmd.
2) Vybrat **vlastnosti**
3) Na kartě Vlastnosti změňte u možnosti *Kopírovat do výstupního adresáře* možnost *Kopírovat vždy "*
4) Opakujte kroky pro **TLSsettings. ps1**

## <a name="step-7-publish--validate"></a>Krok 7: publikování & ověřování

Teď, když jste výše uvedené kroky dokončili, publikujte aktualizaci do stávající cloudové služby. 

[SSLLabs](https://www.ssllabs.com/) můžete použít k ověření stavu TLS koncových bodů. 

 
