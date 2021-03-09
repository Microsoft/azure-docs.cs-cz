---
title: Šifrování disků pro Azure Scale Sets pomocí Azure PowerShell
description: Naučte se používat Azure PowerShell k šifrování instancí virtuálních počítačů a připojených disků v sadě škálování virtuálního počítače s Windows.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519567"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Šifrování operačních systémů a připojených datových disků v sadě škálování virtuálního počítače s Azure PowerShell

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech.  V tomto článku se dozvíte, jak pomocí Azure PowerShell vytvořit a zašifrovat sadu škálování virtuálního počítače. Další informace o použití Azure Disk Encryption pro sadu škálování virtuálního počítače najdete v článku [Azure Disk Encryption Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření Azure Key Vault s povoleným šifrováním disku

Azure Key Vault můžou ukládat klíče, tajné klíče nebo hesla, které vám umožní je bezpečně implementovat ve svých aplikacích a službách. Kryptografické klíče jsou uložené v Azure Key Vault pomocí ochrany softwaru nebo můžete klíče importovat nebo generovat v modulech hardwarového zabezpečení (HSM) certifikovaným pro standardy standardu FIPS 140-2 úrovně 2. Tyto kryptografické klíče slouží k šifrování a dešifrování virtuálních disků připojených k vašemu VIRTUÁLNÍmu počítači. Podržíte kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití.

Vytvořte Key Vault pomocí [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Pokud chcete Key Vault použít pro šifrování disku, nastavte parametr *EnabledForDiskEncryption* . Následující příklad také definuje proměnné pro název skupiny prostředků, Key Vault název a umístění. Zadejte vlastní jedinečný Key Vault název:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující Key Vault

Tento krok se vyžaduje jenom v případě, že máte existující Key Vault, kterou chcete používat s šifrováním disku. Tento krok přeskočte, pokud jste vytvořili Key Vault v předchozí části.

Existující Key Vault můžete povolit ve stejném předplatném a oblasti jako sadu škálování pro šifrování disků pomocí [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Zadejte název existující Key Vault v proměnné *$vaultName* následujícím způsobem:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Nejprve pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte sadu škálování virtuálního počítače pomocí [New-AzVmss](/powershell/module/az.compute/new-azvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Povolit šifrování

K šifrování instancí virtuálních počítačů v sadě škálování je třeba nejdřív získat nějaké informace o Key Vault identifikátor URI a ID prostředku pomocí [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Tyto proměnné se používají ke spuštění procesu šifrování pomocí [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Po zobrazení výzvy zadejte *y* a pokračujte v procesu šifrování disku na INSTANCÍCH virtuálních počítačů sady škálování.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Povolení šifrování pomocí KEK k zabalení klíče

Při šifrování sady škálování virtuálního počítače můžete také použít šifrovací klíč klíče pro zvýšení zabezpečení.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Syntaxe pro hodnotu parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:</br>
/Subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br></br>
> Syntaxe pro hodnotu parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v:</br>
https://[Trezor klíčů-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Průběh šifrování kontroly

Pokud chcete zjistit stav šifrování disku, použijte [příkaz Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Když jsou instance virtuálních počítačů šifrované, *EncryptionSummary* kód *ProvisioningState/uspěl* , jak je znázorněno v následujícím příkladu výstupu:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Zakázat šifrování

Pokud už nechcete disky s šifrovanými instancemi virtuálních počítačů používat, můžete zakázat šifrování pomocí příkazu [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) následujícím způsobem:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Další kroky

- V tomto článku jste použili Azure PowerShell k šifrování sady škálování virtuálních počítačů. Můžete také použít šablony [Azure CLI](disk-encryption-cli.md) nebo [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Pokud chcete po zřízení jiného rozšíření použít Azure Disk Encryption, můžete použít [sekvencování rozšíření](virtual-machine-scale-sets-extension-sequencing.md).
