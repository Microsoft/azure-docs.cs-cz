---
title: Řešení potíží způsobených aplikacemi, které nepodporují protokol TLS 1,2 | Microsoft Docs
description: Řešení potíží způsobených aplikacemi, které nepodporují protokol TLS 1,2
services: cloud-services
documentationcenter: ''
author: MicahMcKittrick-MSFT
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
ms.openlocfilehash: c4cbe2a99de97b2938d494e80d410a44c4143629
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026832"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Řešení potíží s aplikacemi, které nepodporují protokol TLS 1,2
Tento článek popisuje, jak povolit starší protokoly TLS (TLS 1,0 a 1,1) a jak používat starší šifrovací sady pro podporu dalších protokolů na webu a rolích pracovních procesů cloudové služby v systému Windows 2019 Server. 

Chápeme, že při provádění kroků k vyřazení TLS 1,0 a TLS 1,1 můžou naši zákazníci potřebovat podporu starších protokolů a šifrovacích sad, dokud je nemůžou naplánovat jejich vyřazení.  I když tyto starší hodnoty nedoporučujeme znovu povolit, poskytujeme pokyny pro pomoc zákazníkům. Zákazníkům doporučujeme, aby před implementací změn uvedených v tomto článku vyhodnotili riziko regrese. 

> [!NOTE]
> Verze 6 pro řady hostovaných operačních systémů vynutila TLS 1,2 zakázáním šifry 1.0/1.0. 

  
## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Vyřazení podpory pro TLS 1,0, TLS 1,1 a starší šifrovací sady 
V případě podpory našeho úsilí o použití vysoce integrovaného šifrování je Microsoft oznámil plán na zahájení migrace mimo TLS 1,0 a 1,1 v červnu 2017.   Od tohoto počátečního oznámení společnost Microsoft oznámila náš záměr zakázat zabezpečení TLS (Transport Layer Security) 1,0 a 1,1 ve výchozím nastavení v podporovaných verzích Microsoft Edge a Internet Exploreru 11 v první polovině sady 2020.  Podobná oznámení od společnosti Apple, Google a Mozilla označují směr, ve kterém se odvětví zaznamenalo.   

## <a name="tls-configuration"></a>Konfigurace TLS  
Bitová kopie Windows serveru 2019 cloudového serveru je nakonfigurovaná s TLS 1,0 a TLS 1,1 disabled na úrovni registru. To znamená, že aplikace nasazené do této verze systému Windows a použití služby Windows Stack pro vyjednávání TLS nebudou umožňovat komunikaci TLS 1,0 a TLS 1,1.   

Na serveru se nachází také omezená sada šifrovacích sad: 

```Powershell
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

Následující kód použijte jako příklad k vytvoření skriptu, který umožňuje použití starších protokolů a šifrovacích sad.   Pro účely této dokumentace bude tento skript název: TLSsettings. ps1. 
  
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
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384" 

# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA" 
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
For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) {  
   Write-Log -Message "Checking for existing of key: $($localRegistryPath [$i]) " -Logfile $logLocation  -Severity Information 
   If (!(Test-Path -Path $localRegistryPath [$i])) {  
        New-Item $localRegistryPath [$i] | Out-Null 
               Write-Log -Message "Creating key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information 
           } 
}  

#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 

For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) { 
    if ($localRegistryPath [$i].Contains("Client") -Or $localRegistryPath [$i].Contains("Server")) { 
        Write-Log -Message "Enabling this key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information  
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 

$cipherlist = @() 

# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$cipherorder = [System.String]::Join(",", $cipherlist) 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  

if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Log -Message "Creating key: $($CipherSuiteRegKey) "  -Logfile $logLocation -Severity Information 
}  

Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  

#********************************************* REBOOT ******************************************* 

Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4  
```
  
## <a name="step-2-create-a-command-file"></a>Krok 2: vytvoření souboru příkazů 

Vytvořte soubor CMD s následujícími informacemi. 

```
IF "%ComputeEmulatorRunning%" == "true" ( 
   ECHO Not launching the script, since is DEV Machine >> "Log.txt" 2>&1 
) ELSE ( 

PowerShell .\TLSsettings.ps1   
  
) 

REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 

EXIT /B 0   
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Krok 3: Přidejte úlohu po spuštění do definice služby role (csdef). 

Tady je příklad, který ukazuje roli pracovního procesu i webovou roli. 
  
```
<?xml version="1.0" encoding="utf-8"?> 
<ServiceDefinition name="NugetExampleCloudServices" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
  <WebRole name="WebRole1" vmsize="Standard_D1_v2"> 
    <Sites> 
      <Site name="Web"> 
        <Bindings> 
          <Binding name="Endpoint1" endpointName="Endpoint1" /> 
        </Bindings> 
      </Site> 
    </Sites> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
    <Endpoints> 
      <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
    </Endpoints> 
  </WebRole> 
  <WorkerRole name="WorkerRole1" vmsize="Standard_D1_v2"> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
  </WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-4-validation"></a>Krok 4: ověření 

[SSLLabs](https://www.ssllabs.com/) můžete použít k ověření stavu TLS koncových bodů. 

 
