---
title: Poradce při potížích s chybami spuštění nástroje BitLocker u virtuálního počítače Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit chyby při spuštění nástroje BitLocker ve virtuálním počítači Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.openlocfilehash: 80fd91106530c0150a85d508b24041b2263da925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250007"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Chyby spuštění nástroje BitLocker na virtuálním počítači Azure

 Tento článek popisuje chyby nástroje BitLocker, ke kterým může dojít při spuštění virtuálního počítače (VM) windows v Microsoft Azure.

 

## <a name="symptom"></a>Příznak

 Virtuální ho virtuálního mísa systému Windows se nespustí. Když zkontrolujete snímky obrazovky v okně [Diagnostika spouštění,](../windows/boot-diagnostics.md) zobrazí se jedna z následujících chybových zpráv:

- Připojte ovladač USB s klíčem BitLocker

- Jste zamčení! Zadejte obnovovací klíč, abyste mohli začít znovu (Rozložení klávesnice: USA) Nesprávné přihlašovací údaje byly zadány příliš mnohokrát, takže váš počítač byl uzamčen, aby bylo chráněno vaše soukromí. Chcete-li obnovit klíč, https://windows.microsoft.com/recoverykeyfaq přejděte na něj z jiného počítače nebo mobilního zařízení. V případě, že ji budete potřebovat, id klíče je XXXXXXX. Nebo můžete obnovit počítač.

- Zadejte heslo pro odemknutí této jednotky [ ] Stisknutím klávesy Insert Key zobrazíte heslo při psaní.
- Zadejte obnovovací klíč Načtení obnovovacího klíče ze zařízení USB.

## <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud virtuální modul nemůže najít soubor obnovovacího klíče nástroje BitLocker (BEK) k dešifrování šifrovaného disku.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, zastavte a navrátit virtuální počítač a restartujte jej. Tato operace vynutí virtuální počítač načíst soubor BEK z trezoru klíčů Azure a pak jej umístit na šifrovaný disk. 

Pokud tato metoda problém nevyřeší, postupujte ručně a obnovte soubor BEK:

1. Pořizovat snímek systémového disku ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte systémový disk k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md). Chcete-li spustit příkaz [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) v kroku 7, musí být funkce **Šifrování jednotky nástrojem BitLocker** povolena ve virtuálním virtuálním zařízení pro obnovení.

    Při připojení spravovaného disku se může zobrazit chybová zpráva "obsahuje nastavení šifrování, a proto jej nelze použít jako datový disk". V takovém případě spusťte následující skript a zkuste znovu připojit disk:

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
     Spravovaný disk nelze připojit k virtuálnímu virtuálnímu počítače, který byl obnoven z bitové kopie objektu blob.

3. Po připojení disku vytvořte připojení ke vzdálené ploše k virtuálnímu počítači pro obnovení, abyste mohli spustit některé skripty Azure PowerShellu. Ujistěte se, že máte nainstalovanou [nejnovější verzi Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview) na virtuálním počítači pro obnovení.

4. Otevřete relaci Azure PowerShellu se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy pro přihlášení k předplatnému Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Chcete-li zkontrolovat název souboru BEK, spusťte následující skript:

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

    Následuje ukázka výstupu. Vyhledejte název souboru BEK pro připojený disk. V tomto případě předpokládáme, že písmeno jednotky připojeného disku je F a soubor BEK je EF7B2F5A-50C6-4637-9F13-7F599C12F85C. BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Pokud se zobrazí dva duplicitní svazky, svazek, který má novější časové razítko je aktuální soubor BEK, který se používá pro obnovení virtuálního času.

    Pokud je hodnota **Typ obsahu** **Zabalená BEK**, přejděte do [scénáře kek (Key Encryption Key).](#key-encryption-key-scenario)

    Nyní, když máte název souboru BEK pro jednotku, musíte vytvořit tajný soubor-název. BEK pro odemknutí jednotky.

6.  Stáhněte soubor BEK na disk pro obnovení. Následující ukázka uloží soubor BEK do složky C:\BEK. Před spuštěním `C:\BEK\` skriptů se ujistěte, že cesta existuje.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Chcete-li připojený disk odemknout pomocí souboru BEK, spusťte následující příkaz.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    V této ukázce je připojený disk operačního systému Jednotka F. Ujistěte se, že používáte správné písmeno jednotky. 

8. Po úspěšném odemknutí disku pomocí klíče BEK odpojte disk od virtuálního počítače pro obnovení a potom znovu vytvořte virtuální modul pomocí tohoto nového disku operačního systému.

    > [!NOTE]
    > Prohození disku operačního systému není podporováno pro virtuální počítače pomocí šifrování disku.

9. Pokud se nový virtuální virtuální může normálně spustit, zkuste po odemknutí jednotky jeden z následujících kroků:

    - Pozastavit ochranu pro dočasné vypnutí nástroje BitLocker spuštěním následujícího spuštění:

                    manage-bde -protectors -disable F: -rc 0
           
    - Plně dešifrujte jednotku. Provedete to spuštěním následujícího příkazu:

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>Scénář šifrovacího klíče klíče

Pro scénář klíč šifrovací klíč postupujte takto:

1. Ujistěte se, že přihlášený uživatelský účet vyžaduje "rozbalené" oprávnění v zásadách přístupu k trezoru klíčů v **uživateli| Klíčová oprávnění| Kryptografické operace| Rozbalit klíč**.
2. Uložte následující skript do . Ps1 soubor:

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
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

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
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
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
    ```
3. Nastavte parametry. Skript zpracuje tajný klíč KEK k vytvoření klíče BEK a potom jej uloží do místní složky na virtuálním počítači pro obnovení. Pokud se při spuštění skriptu zobrazí chyby, přečtěte si část [řešení potíží se skripty.](#script-troubleshooting)

4. Při zahájení skriptu se zobrazí následující výstup:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Po dokončení skriptu se zobrazí následující výstup:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Chcete-li připojený disk odemknout pomocí souboru BEK, spusťte následující příkaz:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    V této ukázce je připojený disk operačního systému Jednotka F. Ujistěte se, že používáte správné písmeno jednotky. 

6. Po úspěšném odemknutí disku pomocí klíče BEK odpojte disk od virtuálního počítače pro obnovení a potom znovu vytvořte virtuální modul pomocí tohoto nového disku operačního systému. 

    > [!NOTE]
    > Prohození disku operačního systému není podporováno pro virtuální počítače pomocí šifrování disku.

7. Pokud se nový virtuální virtuální může normálně spustit, zkuste po odemknutí jednotky jeden z následujících kroků:

    - Pozastavit ochranu pro dočasné vypnutí nástroje BitLocker spuštěním následujícího příkazu:

             manage-bde -protectors -disable F: -rc 0
           
    - Plně dešifrujte jednotku. Provedete to spuštěním následujícího příkazu:

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>Řešení potíží se skripty

**Chyba: Nelze načíst soubor nebo sestavení.**

K této chybě dochází, protože cesty ADAL sestavení jsou nesprávné. Pokud je modul AZ nainstalován pouze pro aktuálního uživatele, budou `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>`sestavení ADAL umístěna v aplikaci .

Můžete také vyhledat `Az.Accounts` složku a najít správnou cestu.

**Chyba: Get-AzKeyVaultSecret nebo Get-AzKeyVaultSecret není rozpoznán jako název rutiny**

Pokud používáte starý modul AZ PowerShell, je nutné `Get-AzureKeyVaultSecret` změnit `Get-AzureKeyVaultSecret`dva příkazy na a .

**Vzorky parametrů**

| Parametry  | Ukázka hodnoty  |Komentáře   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Název trezoru klíčů, který ukládá klíč |
|$kekName   |mykey   | Název klíče, který se používá k šifrování virtuálního mísy|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Název tajného klíče virtuálního_kontví|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |Cesta pro zápis souboru BEK.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN nebo identifikátor GUID služby Azure Active Directory, který je hostitelem trezoru klíčů |
