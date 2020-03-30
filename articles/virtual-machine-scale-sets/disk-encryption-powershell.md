---
title: Šifrování disků pro škálovací sady Azure pomocí Azure PowerShellu
description: Zjistěte, jak pomocí Azure PowerShellu šifrovat instance virtuálních počítačů a připojené disky ve škálovací sadě virtuálních počítačů s Windows.
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: bd7f92c104e06896f4b3c8bb2adef45983cf5d4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278984"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Šifrování operačního systému a připojených datových disků ve škálovací sadě virtuálních počítačů pomocí Azure PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech.  Tento článek ukazuje, jak pomocí Azure PowerShellu vytvořit a šifrovat škálovací sadu virtuálních strojů. Další informace o použití Azure Disk Encryption na škálovací sadu virtuálních počítačů najdete v [tématu Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření trezoru klíčů Azure povoleného pro šifrování disku

Azure Key Vault můžete ukládat klíče, tajné klíče nebo hesla, které vám umožní bezpečně implementovat ve vašich aplikacích a službách. Kryptografické klíče jsou uloženy v trezoru klíčů Azure pomocí softwarové ochrany nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2. Tyto kryptografické klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Zachováte kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití.

Vytvořte trezor klíčů s [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Chcete-li povolit použití trezoru klíčů pro šifrování disku, nastavte parametr *EnabledForDiskEncryption.* Následující příklad také definuje proměnné pro název skupiny prostředků, název trezoru klíčů a umístění. Zadejte svůj vlastní jedinečný název trezoru klíčů:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Použití existujícího trezoru klíčů

Tento krok je vyžadován pouze v případě, že máte existující trezor klíčů, který chcete použít s šifrováním disku. Tento krok přeskočte, pokud jste v předchozí části vytvořili trezor klíčů.

Pomocí [zásad Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)můžete povolit existující trezor klíčů ve stejném předplatném a oblasti jako škálovací sadu pro šifrování disku . V proměnné *$vaultName* definujte název existujícího trezoru klíčů takto:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Nejprve pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní vytvořte škálovací sadu virtuálních strojů s [novou AzVmss](/powershell/module/az.compute/new-azvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

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

Chcete-li šifrovat instance virtuálních počítačích ve škálovací sadě, nejprve získejte některé informace o identifikátoru URI úložiště klíčů a ID prostředku pomocí [služby Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Tyto proměnné se používají k spuštění procesu šifrování pomocí [rozšíření Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Po zobrazení výzvy zadejte *y,* chcete-li pokračovat v procesu šifrování disku na instancích virtuálních počítačů škálovací sady.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Povolení šifrování pomocí kek zabalit klíč

Šifrovací klíč klíče můžete také použít pro zvýšení zabezpečení při šifrování škálovací sady virtuálních strojů.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxe pro hodnotu parametru klíč šifrování klíč je úplné URI k KEK jako v:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Když jsou instance virtuálních zařízení zašifrované, kód *EncryptionSummary* hlásí *ProvisioningState/succeed,* jak je znázorněno v následujícím příkladu výstupu:

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

Pokud již nechcete používat šifrované disky instancí virtuálních počítačů, můžete zakázat šifrování [pomocí disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) následujícím způsobem:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Další kroky

- V tomto článku jste použili Azure PowerShell k šifrování škálovací sady virtuálních strojů. Můžete taky použít azure [CLI](disk-encryption-cli.md) nebo [Azure Resource Manager šablony](disk-encryption-azure-resource-manager.md).
- Pokud chcete, aby bylo šifrování disku Azure použito po zřízení jiného rozšíření, můžete použít [řazení rozšíření](virtual-machine-scale-sets-extension-sequencing.md).
