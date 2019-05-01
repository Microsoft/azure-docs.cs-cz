---
title: Šifrování disků pro Azure škálovací sady pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí prostředí Azure PowerShell k šifrování instancí virtuálních počítačů a připojených disků ve škálovací sadě virtuálního počítače Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: 7ebb88317da45ff496385b72c603a44d628b0202
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869077"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Šifrování operačního systému a připojené datové disky ve virtuálním počítači škálovací sady pomocí Azure Powershellu

Pro ochranu dat v klidovém stavu pomocí standardní šifrovací technologie, podporují škálovací sady virtuálních počítačů Azure Disk Encryption (ADE). Šifrování je možné povolit pro Windows a Linux virtuálního počítače škálovací sady. Další informace najdete v tématu [Azure Disk Encryption pro Windows a Linuxem](../security/azure-security-disk-encryption.md).

Azure disk encryption je podporována:
- pro škálovací sady se spravovanými disky vytvořené a není podporováno pro nativní (nebo nespravované) disku škálovací sady.
- pro operační systém a datové svazky ve Windows škálovacích sadách. Zakázat šifrování je podporováno pro operační systém a datové svazky pro Windows škálovací sady.
- pro datové svazky v Linuxu škálovací sady. Šifrování disku operačního systému není podporován v současné době pro Linux škálovací sady.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření služby Azure Key Vault nepovoluje šifrování disku

Azure Key Vault umí ukládat klíče, tajné kódy nebo hesla, které umožňují bezpečný je implementovat ve svých aplikacích a službách. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití.

Vytvoření služby Key Vault pomocí [nové AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Chcete-li povolit služby Key Vault má být použit pro šifrování disků, nastavte *EnabledForDiskEncryption* parametru. Následující příklad také definuje proměnné pro název skupiny prostředků, název trezoru klíčů a umístění. Zadejte vlastním jedinečným názvem služby Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující služby Key Vault

Tento krok je jenom nutné, pokud máte existující Key Vault, kterou chcete použít šifrování disku. Tento krok přeskočte, pokud jste vytvořili službu Key Vault v předchozí části.

Můžete povolit existující službu Key Vault ve stejném předplatném a oblasti jako škálovací sady pro šifrování disků s [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Definovat název existující službu Key Vault v *$vaultName* proměnné následujícím způsobem:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Nejprve pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte virtuální počítač škálovací sadu s [New-AzVmss](/powershell/module/az.compute/new-azvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

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

K šifrování instancí virtuálních počítačů ve škálovací sadě, nejdřív získejte některé informace o ID identifikátor URI služby Key Vault a prostředek s [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Tyto proměnné se používají k spusťte proces šifrování s [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Po zobrazení výzvy zadejte *y* pokračujte v procesu šifrování disku škálovací nastavení virtuálního počítače instance.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Povolit šifrování pomocí KEK zabalit klíč

Můžete také použít klíčového šifrovacího klíče pro přidání zabezpečení při šifrování škálovací sadu virtuálních počítačů.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Když instance virtuálních počítačů jsou zašifrované, *EncryptionSummary* kódu sestavy *bylo úspěšné a Stav zřizování* jak je znázorněno v následujícím příkladu výstupu:

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

Pokud již nechcete používat šifrovanými disky instancí virtuálních počítačů, můžete zakázat šifrování s [zakázat AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) následujícím způsobem:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Další postup

- V tomto článku se používá prostředí Azure PowerShell k šifrování škálovací sadu virtuálních počítačů. Můžete také použít [rozhraní příkazového řádku Azure](virtual-machine-scale-sets-encrypt-disks-cli.md) nebo šablony pro [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Pokud chcete použít Azure Disk Encryption po zřízení jinou příponu, můžete použít [nastavení posloupnosti rozšíření](virtual-machine-scale-sets-extension-sequencing.md). Můžete použít [tyto ukázky](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) začít.
