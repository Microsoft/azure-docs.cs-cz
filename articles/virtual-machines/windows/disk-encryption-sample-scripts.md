---
title: Ukázkové skripty pro službu Azure Disk Encryption
description: Tento článek je přílohou pro šifrování disku Microsoft Azure pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266725"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Ukázkové skripty pro službu Azure Disk Encryption 

Tento článek obsahuje ukázkové skripty pro přípravu předem šifrované virtuální chodů a další chod.

 

## <a name="list-vms-and-secrets"></a>Seznam virtuálních aplikací a tajných kódů

Seznam všech šifrovaných virtuálních počítačů ve vašem předplatném:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Seznam všech tajných klíčů šifrování disku používaných k šifrování virtuálních počítačů v trezoru klíčů:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Skripty požadavků azure diskové šifrování
Pokud už jste obeznámeni s požadavky pro šifrování disku Azure, můžete použít [skript PowerShell u azure diskového šifrování](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Příklad použití tohoto skriptu prostředí PowerShell najdete v tématu [Šifrování rychlého startu virtuálního virtuálního soudu](disk-encryption-powershell-quickstart.md). Komentáře můžete odebrat z části skriptu, počínaje řádkem 211, abyste zašifrovat všechny disky pro existující virtuální počítače v existující skupině prostředků. 

V následující tabulce jsou uvedeny parametry, které lze použít ve skriptu prostředí PowerShell: 

|Parametr|Popis|Povinné?|
|------|------|------|
|$resourceGroupName| Název skupiny prostředků, do které keyvault patří.  Pokud neexistuje, bude vytvořena nová skupina prostředků s tímto názvem.| True|
|$keyVaultName|Název keyvault, ve kterém mají být umístěny šifrovací klíče. Pokud trezor s tímto názvem neexistuje, vytvoří se nový trezor s tímto názvem.| True|
|$location|Umístění KeyVault. Ujistěte se, že KeyVault a virtuální chod, které mají být šifrovány jsou ve stejném umístění. Seznam umístění získáte pomocí rutiny `Get-AzLocation`.|True|
|$subscriptionId|Identifikátor předplatného Azure, které má být použito.  Své ID předplatného můžete získat pomocí rutiny `Get-AzSubscription`.|True|
|$aadAppName|Název aplikace Azure AD, která se bude používat k zápisu tajných kódů do KeyVault. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud tato aplikace již existuje, předajte skriptu parametr aadClientSecret.|False|
|$aadClientSecret|Tajný klíč klienta aplikace Azure AD, která byla vytvořena dříve.|False|
|$keyEncryptionKeyName|Název volitelného šifrovacího klíče v úložišti KeyVault. Nový klíč s tímto názvem bude vytvořen, pokud neexistuje.|False|

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Šifrování nebo dešifrování virtuálních počítačů bez aplikace Azure AD

- [Povolení šifrování disku na existujícím nebo spuštěném virtuálním počítači se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Zakázání šifrování na spuštěném virtuálním počítači se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Šifrování nebo dešifrování virtuálních počítačů pomocí aplikace Azure AD (předchozí verze) 
 
- [Povolení šifrování disku na existujícím nebo spuštěném virtuálním počítači se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Zakázání šifrování na spuštěném virtuálním počítači se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Vytvoření nového šifrovaného spravovaného disku z předšifrovaného objektu blob virtuálního pevného disku nebo úložiště](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Vytvoří nový šifrovaný spravovaný disk za předpokladu, že předem zašifrovaný virtuální disk a odpovídající nastavení šifrování

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Příprava předem šifrovaného virtuálního pevného disku systému Windows
Následující části jsou nezbytné k přípravě předem šifrovaného virtuálního pevného disku systému Windows pro nasazení jako šifrovaného virtuálního pevného disku v Azure IaaS. Pomocí těchto informací můžete připravit a spustit nový virtuální počítač s Windows (VHD) v Azure Site Recovery nebo Azure. Další informace o tom, jak připravit a nahrát virtuální pevný disk, najdete v [článku Nahrání generalizovaného virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů v Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizovat zásady skupiny tak, aby bylo možné povolit ochranu operačního systému bez čipu TPM
Nakonfigurujte nastavení zásad skupiny **BitLocker,** které najdete v části Šablony**pro správu** > **konfigurace** > počítače >  **v místním počítači.****Windows Components** Toto nastavení změňte na >  **Jednotky operačního systému****Vyžadovat při spuštění povolit** > **nástroj BitLocker bez kompatibilního čipu TPM**další ověřování, jak je znázorněno na následujícím obrázku:

![Microsoft Antimalware v Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalace součástí prvků nástroje BitLocker
Pro Windows Server 2012 a novější použijte následující příkaz:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

V systému Windows Server 2008 R2 použijte následující příkaz:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Příprava svazku operačního e-sanace pro nástroj BitLocker pomocí`bdehdcfg`
Chcete-li komprimovat oddíl operačního systému a připravit počítač pro nástroj BitLocker, proveďte v případě potřeby [bdehdcfg:](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment)

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrana svazku operačního spoje pomocí nástroje BitLocker
Pomocí [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) příkazu povolte šifrování na spouštěcím svazku pomocí ochrany externího klíče. Umístěte také externí klíč (soubor.bek) na externí jednotku nebo svazek. Šifrování je povoleno na systémovém/spouštěcím svazku po příštím restartování.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Připravte virtuální počítač se samostatným virtuálním pevným diskem pro data/prostředek pro získání externího klíče pomocí nástroje BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Nahrání šifrovaného virtuálního pevného disku do účtu úložiště Azure
Po povolení šifrování DM-Crypt je třeba místní šifrovaný virtuální pevný disk nahrát do vašeho účtu úložiště.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Nahrání tajného klíče pro předem zašifrovaný virtuální počítač do trezoru klíčů
Tajný klíč šifrování disku, který jste získali dříve, musí být odeslán jako tajný klíč do trezoru klíčů.  To vyžaduje udělení oprávnění set tajný chutnic a oprávnění wrapkey k účtu, který bude nahrát tajné klíče.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Tajný klíč šifrování disku není šifrován pomocí KEK
Chcete-li nastavit tajný klíč v trezoru klíčů, použijte [set-azKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Přístupové heslo je kódováno jako řetězec base64 a poté odesláno do trezoru klíčů. Kromě toho se ujistěte, že jsou při vytváření tajného klíče v trezoru klíčů nastaveny následující značky.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Použijte `$secretUrl` v dalším kroku pro [připojení disku operačního systému bez použití KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Tajný klíč šifrování disku zašifrovaný pomocí kek
Před nahráním tajného klíče do trezoru klíčů jej můžete volitelně zašifrovat pomocí šifrovacího klíče klíče. Pomocí rozhraní [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) pro zalamování nejprve zašifrujte tajný klíč pomocí šifrovacího klíče klíče. Výstupem této operace obtékání je řetězec kódovaný adresou URL base64, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) který pak můžete nahrát jako tajný klíč pomocí rutiny.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Použití `$KeyEncryptionKey` `$secretUrl` a v dalším kroku pro [připojení disku operačního systému pomocí KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Zadání tajné adresy URL při připojení disku operačního systému

###  <a name="without-using-a-kek"></a>Bez použití KEK
Při připojování disku operačního systému `$secretUrl`je třeba předat . Adresa URL byla vygenerována v části "Tajný klíč šifrování disku není šifrován pomocí KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Použití KEK
Když připojíte disk operačního `$KeyEncryptionKey` `$secretUrl`systému, předaj a . Adresa URL byla vygenerována v části "Tajný klíč šifrování disku zašifrovaný pomocí KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
