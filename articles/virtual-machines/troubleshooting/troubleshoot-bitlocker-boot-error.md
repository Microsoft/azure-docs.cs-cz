---
title: Řešení chyb při spouštění BitLockeru na virtuálním počítači Azure | Microsoft Docs
description: Přečtěte si, jak řešit potíže se spouštěním nástroje BitLocker na virtuálním počítači Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/16/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 4891d01c59289afddb244879e042e45b7b7a1aa6
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695720"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Chyby spuštění nástroje BitLocker na virtuálním počítači Azure

 Tento článek popisuje chyby nástroje BitLocker, ke kterým může dojít při spuštění virtuálního počítače s Windows v Microsoft Azure.

 

## <a name="symptom"></a>Příznak

 Virtuální počítač s Windows se nespustí. Po kontrole snímků obrazovky v okně [diagnostiky spouštění](./boot-diagnostics.md) se zobrazí jedna z následujících chybových zpráv:

- Připojte ovladač USB s klíčem BitLockeru.

- Jste uzamčeni! Zadejte obnovovací klíč, který se má znovu zobrazit (rozložení klávesnice: US). nesprávné přihlašovací údaje byly zadány příliš mnohokrát, takže váš počítač byl uzamčen, aby chránil vaše osobní údaje. K načtení obnovovacího klíče použijte https://windows.microsoft.com/recoverykeyfaq jiný počítač nebo mobilní zařízení. V případě potřeby je ID klíče XXXXXXX. Nebo můžete resetovat počítač.

- Zadejte heslo k odemknutí této jednotky [] stisknutím klávesy INSERT zobrazíte heslo při psaní.
- Zadejte obnovovací klíč, který načte obnovovací klíč ze zařízení USB.

## <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud virtuální počítač nemůže najít soubor klíče bek (BitLocker Recovery Key) k dešifrování šifrovaného disku.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, zastavte a uvolněte virtuální počítač a potom ho spusťte. Tato operace vynutí, aby virtuální počítač načetl soubor klíče bek z Azure Key Vault a pak ho umístil na zašifrovaný disk. 

Pokud tato metoda problém nevyřeší, postupujte podle těchto kroků a obnovte soubor klíče bek ručně:

1. Pořídit snímek systémového disku ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte systémový disk k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md). Pokud chcete spustit příkaz [Manage-bde](/windows-server/administration/windows-commands/manage-bde) v kroku 7, musí být na virtuálním počítači pro obnovení povolená funkce **Nástroj BitLocker Drive Encryption** .

    Když připojíte spravovaný disk, může se zobrazit zpráva "obsahuje nastavení šifrování, a proto nemůže být použita jako chybová zpráva" datový disk ". V takovém případě spusťte následující skript a pokuste se znovu připojit disk:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Spravovaný disk nemůžete připojit k virtuálnímu počítači, který se obnovil z Image objektu BLOB.

3. Po připojení disku vytvořte připojení ke vzdálené ploše k virtuálnímu počítači pro obnovení, abyste mohli spouštět některé skripty Azure PowerShell. Ujistěte se, že máte na virtuálním počítači pro obnovení nainstalovanou [nejnovější verzi Azure PowerShell](/powershell/azure/) .

4. Otevřete relaci Azure PowerShell se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy, abyste se přihlásili k předplatnému Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Spuštěním následujícího skriptu zkontrolujete název souboru klíče bek:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Následuje ukázka výstupu. Vyhledejte název souboru klíče bek připojeného disku. V takovém případě předpokládáme, že písmeno připojené jednotky na disku je F a soubor klíče bek je EF7B2F5A-50C6-4637-9F13-7F599C12F85C. Klíče bek.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Pokud vidíte dva duplicitní svazky, svazek, který má novější časové razítko, je aktuální soubor klíče bek, který je používán virtuálním počítačem pro obnovení.

    Pokud je hodnota **typu obsahu** **zabalená klíče bek**, přečtěte si [scénáře použití klíčového šifrovacího klíče (KEK)](#key-encryption-key-scenario).

    Teď, když máte název souboru klíče bek pro jednotku, je nutné vytvořit název tajného souboru. KLÍČE bek soubor k odemknutí jednotky.

6.  Stáhněte si soubor klíče bek na disk pro obnovení. Následující příklad uloží soubor klíče bek do složky C:\BEK. Ujistěte se, že `C:\BEK\` cesta existuje před spuštěním skriptů.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  K odemknutí připojeného disku pomocí souboru klíče bek spusťte následující příkaz.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    V této ukázce je připojen disk s operačním systémem jednotka F. Ujistěte se, že používáte správné písmeno jednotky. 

8. Po úspěšném odemknutí disku pomocí klíče klíče bek odpojte disk od virtuálního počítače pro obnovení a pak znovu vytvořte virtuální počítač pomocí tohoto nového disku s operačním systémem.

    > [!NOTE]
    > Výměna disku s operačním systémem se pro virtuální počítače, které používají šifrování disků, nepodporuje.

9. Pokud se nový virtuální počítač pořád nemůže spustit normálně, zkuste po odemčení jednotky použít některý z následujících kroků:

    - Pozastavení ochrany pro dočasné zapnutí nástroje BitLocker spuštěním následujícího:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Jednotku úplně dešifrujte. Provedete to spuštěním následujícího příkazu:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Scénář šifrovacího klíče klíče

V případě scénáře použití klíčového šifrovacího klíče postupujte následovně:

1. Ujistěte se, že účet přihlášeného uživatele vyžaduje oprávnění "nezabaleno" v zásadách přístupu Key Vault v **uživateli | Oprávnění klíče | Kryptografické operace | Rozbalení klíče**
2. Uložte následující skript do souboru. Soubor PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Agit pgit ccounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Nastavte parametry. Skript zpracuje tajný klíč KEK, aby vytvořil klíč klíče bek, a pak ho uloží do místní složky na virtuálním počítači pro obnovení. Pokud při spuštění skriptu dojde k chybám, přečtěte si část [odstraňování potíží se skriptem](#script-troubleshooting) .

4. Po zahájení skriptu se zobrazí následující výstup:

    Umístění verze GAC                                                                              
    ---    -------        --------                                                                              
    False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    Po dokončení skriptu se zobrazí následující výstup:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. K odemknutí připojeného disku pomocí souboru klíče bek spusťte následující příkaz:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    V této ukázce je připojen disk s operačním systémem jednotka F. Ujistěte se, že používáte správné písmeno jednotky. 

6. Po úspěšném odemknutí disku pomocí klíče klíče bek odpojte disk od virtuálního počítače pro obnovení a pak znovu vytvořte virtuální počítač pomocí tohoto nového disku s operačním systémem. 

    > [!NOTE]
    > Výměna disku s operačním systémem se pro virtuální počítače, které používají šifrování disků, nepodporuje.

7. Pokud se nový virtuální počítač pořád nemůže spustit normálně, zkuste po odemčení jednotky použít některý z následujících kroků:

    - Pozastavení ochrany pro dočasné zapnutí nástroje BitLocker spuštěním následujícího příkazu:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Jednotku úplně dešifrujte. Provedete to spuštěním následujícího příkazu:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Odstraňování potíží se skripty

**Chyba: nepovedlo se načíst soubor nebo sestavení.**

K této chybě dochází, protože cesty k sestavení ADAL jsou chybné. Můžete vyhledat `Az.Accounts` složku a vyhledat správnou cestu.

**Chyba: Get-AzKeyVaultSecret nebo Get-AzKeyVaultSecret není rozpoznán jako název rutiny**

Pokud používáte starý modul AZ PowerShell, musíte změnit dva příkazy na `Get-AzureKeyVaultSecret` a `Get-AzureKeyVaultSecret` .

**Ukázky parametrů**

| Parametry  | Ukázka hodnoty  |Komentáře   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Název trezoru klíčů, ve kterém se klíč ukládá |
|$kekName   |mykey   | Název klíče, který se používá k šifrování virtuálního počítače.|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Název tajného klíče virtuálního počítače|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. KLÍČE bek |Cesta pro zápis souboru klíče bek|
|$adTenant  |contoso.onmicrosoft.com   | Plně kvalifikovaný název domény nebo identifikátor GUID vašeho Azure Active Directory, který hostuje Trezor klíčů |
