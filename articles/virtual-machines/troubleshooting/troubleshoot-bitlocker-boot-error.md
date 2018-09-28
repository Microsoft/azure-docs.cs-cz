---
title: Řešení potíží s chybami spouštěcí nástroj BitLocker na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak řešit chyby spuštění nástroje BitLocker na Virtuálním počítači Azure
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: b5f851fe5c8aebba441903ccc004b7dbd0029ba3
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413272"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Chyby spuštění nástroje BitLocker na Virtuálním počítači Azure

 Tento článek popisuje BitLocker chyby, které můžete narazit při spuštění Windows virtuálního počítače (VM) v Microsoft Azure.

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manager. Doporučujeme použít tento model pro nových nasazení namísto pomocí modelu nasazení classic.

 ## <a name="symptom"></a>Příznak

 Virtuální počítač Windows nespustí. Když vrátíte se změnami na snímcích obrazovky [Diagnostika spouštění](../windows/boot-diagnostics.md) okně se zobrazí jedna z následujících chybových zpráv:

- Zapojte ovladač USB, který má klíč Bitlockeru

- Máte uzamčen! Zadejte obnovovací klíč do znovu (klávesnice: USA) nesprávné přihlašovací informace bylo zadáno příliš mnohokrát tak, že váš počítač byl uzamčen pro ochranu vašich osobních údajů. Pokud chcete načíst obnovovací klíč, přejděte na http://windows.microsoft.com/recoverykeyfaq z jiného počítače nebo mobilního zařízení. V případě, že ji klíč budete potřebovat ID je XXXXXXX. Nebo můžete resetovat vašemu počítači.

- Zadejte heslo k odemknutí tento disk [] č. stisknutím klávesy Insert zobrazíte heslo při psaní.
- Zadejte obnovovacímu klíči zatížení obnovovací klíč z USB zařízení.

## <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud virtuální počítač nelze najít soubor nástroje BitLocker pro obnovení klíče (klíče BEK) k dešifrování šifrovaného disku.

## <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém, zastavit a zrušit přidělení virtuálního počítače a pak ji znovu spusťte. Tato operace způsobí, že virtuální počítač načíst soubor klíče BEK z Azure Key Vault a vložit ho na šifrovaného disku. 

Pokud tato metoda nemá vyřešit problém, soubor klíče BEK obnovit ručně pomocí těchto kroků:

1. Pořízení snímku systémový disk ovlivněných virtuálních počítačů jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md) , který je zašifrováno Bitlockeru. To se vyžaduje pro spuštění [příkazu manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) příkaz, který je k dispozici pouze na virtuálním počítači šifrované nástrojem BitLocker.

    Při připojení spravovaného disku může zobrazit chybová zpráva "obsahuje nastavení šifrování a proto nemůže být použit jako datový disk". V takovém případě spusťte následující skript znovu připojit disk:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzureRmDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzureRMVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzureRmVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzureRMVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Spravovaný disk nelze připojit k virtuálnímu počítači, který byl obnoven z image objektů blob.

3. Po připojení disku nastavit připojení ke vzdálené ploše pro virtuální počítač pro obnovení, takže můžete spouštět některé skripty Azure Powershellu. Ujistěte se, že máte [nejnovější verzi Azure Powershellu](https://docs.microsoft.com/powershell/azure/overview) nainstalovat na virtuální počítač pro obnovení.

4. Spusťte relaci Azure Powershellu se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy pro přihlášení k předplatnému Azure:

    ```Powershell
    Add-AzureRMAccount -SubscriptionID [SubscriptionID]
    ```

5. Spusťte následující skript, který zkontrolujte název souboru klíče BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Tady je ukázkový výstup. Vyhledejte název souboru klíče BEK pro připojený disk. V takovém případě předpokládáme, že písmeno jednotky připojený disk je F, a soubor klíče BEK je EF7B2F5A - 50C 6-4637-9F13-7F599C12F85C. KLÍČE BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Pokud se zobrazí dva duplicitní svazky, svazek, který má novější časové razítko je aktuální soubor klíče BEK, který používá virtuální počítač pro obnovení.

    Pokud **typ obsahu** hodnotu **zabalené klíče BEK**, přejděte na stránku [klíč šifrování klíčů (KEK) scénáře](#key-encryption-key-scenario).

    Teď, když máte název souboru klíče BEK pro disk, budete muset vytvořit název souboru tajný klíč. Soubor klíče BEK pro odemknutí jednotky. 

6.  Stáhněte si soubor klíče BEK pro obnovení disku. Následující ukázka uloží soubor klíče BEK C:\BEK složky. Ujistěte se, že `C:\BEK\` cesta existuje, před spuštěním skriptů.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Odemknout pomocí souboru klíče BEK připojený disk, spusťte následující příkaz:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    V této ukázce připojeném disku s operačním systémem je disk F. Ujistěte se, že používáte správné písmeno jednotky. 

    - Pokud disk byl úspěšně odemknout pomocí klíče BEK klíče. považujeme BItLocker problém vyřešit. 

    - Pokud pomocí klíče BEK klíče není odemknutí disku, můžete pozastavit ochrany dočasně vypnout nástroj BitLocker spuštěním následujícího příkazu
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Pokud chcete znovu vytvořit virtuální počítač pomocí disku dytem, třeba jednotku plně dešifrovat. Chcete-li to provést, spusťte následující příkaz:

        ```powershell
        manage-bde -off F:
        ```
8.  Odpojit disk od virtuální počítač pro obnovení a znovu připojte disk k virtuálnímu počítači ovlivněné jako systémový disk. Další informace najdete v tématu [odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Scénář šifrovací klíč klíče

Pro scénář klíčového šifrovacího klíče postupujte takto:

1. Ujistěte se, že přihlášený uživatelský účet vyžaduje oprávnění "nezabalené" zásady přístupu trezoru klíč v **uživatele | Oprávnění klíče | Kryptografické operace | Rozbalit klíč**.
2. Uložit do následujících skriptů. PS1 souboru:

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
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
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
    $authResult = $authContext.AcquireToken($resourceAppIdURI, $clientId, $redirectUri, "Auto")
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
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
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
3. Nastavte parametry. Skript zpracovat KEK tajný kód k vytvoření klíče BEK klíče a uloží ho do místní složky na virtuální počítač pro obnovení.

4. Po zahájení skriptu, zobrazí se následující výstup:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...

    Po dokončení skriptu se zobrazí následující výstup:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Odemknout pomocí souboru klíče BEK připojený disk, spusťte následující příkaz:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    V této ukázce připojeném disku s operačním systémem je disk F. Ujistěte se, že používáte správné písmeno jednotky. 

    - Pokud disk byl úspěšně odemknout pomocí klíče BEK klíče. považujeme BItLocker problém vyřešit. 

    - Pokud pomocí klíče BEK klíče není odemknutí disku, můžete pozastavit ochrany dočasně vypnout nástroj BitLocker spuštěním následujícího příkazu
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Pokud chcete znovu vytvořit virtuální počítač pomocí disku dytem, třeba jednotku plně dešifrovat. Chcete-li to provést, spusťte následující příkaz:

        ```powershell
        manage-bde -off F:
        ```

6. Odpojit disk od virtuální počítač pro obnovení a znovu připojte disk k virtuálnímu počítači ovlivněné jako systémový disk. Další informace najdete v tématu [odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-windows.md).
