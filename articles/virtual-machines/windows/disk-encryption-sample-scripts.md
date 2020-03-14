---
title: Azure Disk Encryption ukázkové skripty
description: Tento článek je dodatek pro Microsoft Azure šifrování disku pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266725"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption ukázkové skripty 

Tento článek poskytuje ukázkové skripty pro přípravu předem šifrovaných virtuálních pevných disků a dalších úloh.

 

## <a name="list-vms-and-secrets"></a>Výpis virtuálních počítačů a tajných klíčů

Vypíše všechny šifrované virtuální počítače ve vašem předplatném:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Vypíše všechny tajné klíče pro šifrování disků používané k šifrování virtuálních počítačů v trezoru klíčů:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Skripty pro Azure Disk Encryption předpoklady
Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption, můžete použít [skript PowerShellu pro Azure Disk Encryption požadavků](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Příklad použití tohoto skriptu PowerShellu najdete v tématu [rychlý Start k šifrování virtuálního počítače](disk-encryption-powershell-quickstart.md). Komentáře můžete odebrat z část skriptu, počínaje řádkem 211, k šifrování všech disků pro stávající virtuální počítače v existující skupinu prostředků. 

V následující tabulce jsou uvedeny parametry, které lze použít ve skriptu prostředí PowerShell: 

|Parametr|Popis|Závaznou?|
|------|------|------|
|$resourceGroupName| Název skupiny prostředků, ke kterému patří trezoru klíčů.  Novou skupinu prostředků s tímto názvem bude vytvořen, pokud neexistuje.| True|
|$keyVaultName|Název trezoru klíčů, ve které šifrovací klíče jsou umístit. Nový trezor s tímto názvem bude vytvořen, pokud neexistuje.| True|
|$location|Umístění trezoru klíčů. Ujistěte se, že trezor klíčů a virtuální počítače k šifrování jsou ve stejném umístění. Seznam umístění získáte pomocí rutiny `Get-AzLocation`.|True|
|$subscriptionId|Identifikátor předplatného Azure, který se má použít.  Své ID předplatného můžete získat pomocí rutiny `Get-AzSubscription`.|True|
|$aadAppName|Název aplikace Azure AD, která se použije k zápisu tajných klíčů do trezoru klíčů. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud tato aplikace už existuje, předejte parametr aadClientSecret skriptu.|Nepravda|
|$aadClientSecret|Tajný kód klienta aplikace Azure AD, který jste vytvořili dříve.|Nepravda|
|$keyEncryptionKeyName|Název volitelné šifrovací klíč klíče v trezoru klíčů. Nový klíč s tímto názvem bude vytvořen, pokud neexistuje.|Nepravda|

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Šifrování nebo dešifrování virtuálních počítačů bez aplikace Azure AD

- [Povolení šifrování disku na existujícím nebo spuštěném virtuálním počítači s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Zakázání šifrování na běžícím virtuálním počítači s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Šifrování nebo dešifrování virtuálních počítačů pomocí aplikace Azure AD (předchozí verze) 
 
- [Povolení šifrování disku na existujícím nebo spuštěném virtuálním počítači s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Zakázání šifrování na běžícím virtuálním počítači s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Vytvoření nového šifrovaného spravovaného disku z předem šifrovaného objektu VHD nebo úložiště objektů BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Vytvoří nové šifrované spravovaného disku k dispozici předem šifrované virtuální pevný disk a odpovídajících nastavení šifrování

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Příprava předem zašifrovaného virtuálního pevného disku s Windows
Následující části jsou nezbytné pro přípravu předem šifrované virtuální pevný disk Windows pro nasazení jako šifrovaný virtuální pevný disk v Azure IaaS. Použijte informace k přípravě a spuštění virtuálního počítače čerstvé Windows (VHD) na Azure Site Recovery a Azure. Další informace o tom, jak připravit a nahrát VHD, najdete v tématu [nahrání zobecněného virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů v Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizace zásad skupiny umožňující bez TPM pro ochranu operačního systému
Nakonfigurujte nastavení Zásady skupiny BitLockeru **Nástroj BitLocker Drive Encryption**, které najdete v části **zásady místního počítače** > **Konfigurace počítače** > **šablony pro správu** > **součásti systému Windows**. Změňte toto nastavení na **jednotky operačního systému** > **při spuštění vyžadovat další ověřování** > **Nástroj BitLocker povolí bez kompatibilního čipu TPM**, jak je znázorněno na následujícím obrázku:

![Microsoft Antimalware v Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalace součásti funkcí nástroje BitLocker
Pro Windows Server 2012 a novější použijte následující příkaz:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 použijte následující příkaz:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Příprava svazku operačního systému pro nástroj BitLocker pomocí `bdehdcfg`
Pokud chcete zkomprimovat oddíl s operačním systémem a připravit počítač na BitLocker, spusťte v případě potřeby [BdeHdCfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) :

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrana svazku operačního systému pomocí nástroje BitLocker
Pomocí příkazu [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) Povolte šifrování na spouštěcím svazku pomocí ochrany externích klíčů. Externí klíče (soubor .bek) můžete také umístíte na externím disku nebo svazku. Po dalším restartování je povolené šifrování v systému a spouštěcí svazek.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Příprava virtuálního počítače pomocí samostatných/prostředku datového virtuálního pevného disku pro získání externího klíče pomocí nástroje BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Nahrání šifrovaného virtuálního pevného disku do účtu služby Azure Storage
Po povolení šifrování DM-crypt je potřeba odeslat místní zašifrovaný virtuální pevný disk do svého účtu úložiště.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Nahrajte tajný klíč pro předem zašifrovaný virtuální počítač do trezoru klíčů.
Tajný klíč šifrování disku, který jste dříve získali, se musí nahrát jako tajný klíč do trezoru klíčů.  To vyžaduje udělení oprávnění pro nastavení tajného klíče a oprávnění wrapkey k účtu, který odešle tajné klíče.

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

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Tajný klíč šifrování disku není zašifrovaný pomocí KEK.
K nastavení tajného klíče v trezoru klíčů použijte [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Přístupové heslo se zakóduje jako řetězec Base64 a pak se nahraje do trezoru klíčů. Kromě toho Ujistěte se, že následující značky jsou nastaveny při vytvoření tajného klíče v trezoru klíčů.

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


K [připojení disku s operačním systémem bez použití KEK](#without-using-a-kek)použijte `$secretUrl` v dalším kroku.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Tajný kód šifrování disku zašifrovaný pomocí KEK
Před odesláním do služby key vault tajný klíč, které můžete volitelně šifrovat pomocí šifrovací klíč klíče. Použijte [rozhraní API](https://msdn.microsoft.com/library/azure/dn878066.aspx) pro zabalení k prvnímu šifrování tajného klíče pomocí klíčového šifrovacího klíče. Výstupem této operace zalamování je řetězec kódovaný v adrese URL Base64, který lze poté odeslat jako tajný klíč pomocí rutiny [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

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

V dalším kroku použijte `$KeyEncryptionKey` a `$secretUrl` [Připojte disk s operačním systémem pomocí KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Zadejte adresu URL tajného kódu při připojení disku s operačním systémem.

###  <a name="without-using-a-kek"></a>Bez použití KEK
Když připojujete disk s operačním systémem, musíte předat `$secretUrl`. Adresa URL vytvořená v části "šifrování disku tajný klíč není šifrován KEK".
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
Když připojíte disk s operačním systémem, předejte `$KeyEncryptionKey` a `$secretUrl`. Adresa URL vytvořená v části "Tajný kód Disk encryption šifrován KEK".
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
