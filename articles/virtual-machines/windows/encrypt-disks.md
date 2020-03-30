---
title: Šifrování disků na virtuálním počítači s Windows v Azure
description: Šifrování virtuálních disků na virtuálním počítači s Windows pro lepší zabezpečení pomocí Azure PowerShellu
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033529"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Šifrování virtuálních disků na virtuálním počítači s Windows
Pro rozšířené zabezpečení virtuálních počítačů (VM) a dodržování předpisů, virtuální disky v Azure lze šifrovat. Disky jsou šifrovány pomocí kryptografických klíčů, které jsou zabezpečené v trezoru klíčů Azure. Řídíte tyto kryptografické klíče a můžete auditovat jejich použití. Tento článek popisuje, jak šifrovat virtuální disky na virtuálním počítači s Windows pomocí Azure PowerShellu. Můžete také [šifrovat linuxové virtuální počítače](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuálních počítačích se systémem Windows jsou šifrovány v klidovém stavu pomocí nástroje BitLocker. Šifrování virtuálních disků v Azure se neúčtuje. Kryptografické klíče jsou uloženy v trezoru klíčů Azure pomocí ochrany softwaru nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2. Kryptografické klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Můžete mít kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití. 

Proces šifrování virtuálního virtuálního montu je následující:

1. Vytvořte kryptografický klíč v trezoru klíčů Azure.
1. Nakonfigurujte kryptografický klíč tak, aby byl použitelný pro šifrování disků.
1. Povolte šifrování disku pro virtuální disky.
1. Požadované kryptografické klíče jsou požadovány z trezoru klíčů Azure.
1. Virtuální disky jsou šifrovány pomocí poskytnutého kryptografického klíče.


## <a name="requirements-and-limitations"></a>Požadavky a omezení

Podporované scénáře a požadavky na šifrování disku:

* Povolení šifrování na nových virtuálních počítačích s Windows z ibi na webu Azure Marketplace nebo vlastních ibivk ových hd.
* Povolení šifrování na existujících virtuálních počítačích s Windows v Azure.
* Povolení šifrování na virtuálních počítačích se systémem Windows, které jsou nakonfigurovány pomocí prostorů úložiště.
* Zakázání šifrování na operačních jednotkách a datových jednotkách pro virtuální počítačwindows Windows.
* Virtuální virtuální chod standardní úrovně, například virtuální chod řady A, D, DS, G a GS.

    > [!NOTE]
    > Všechny prostředky (včetně trezoru klíčů, účtu úložiště a virtuálního počítače) musí být ve stejné oblasti Azure a předplatné.

Šifrování disku není aktuálně podporováno v následujících scénářích:

* Virtuální virtuální chod základní úrovně.
* Virtuální počítače vytvořené pomocí modelu klasického nasazení.
* Aktualizace kryptografických klíčů na již šifrovaném virtuálním počítači.
* Integrace s místní službou správy klíčů.


## <a name="create-an-azure-key-vault-and-keys"></a>Vytvoření trezoru klíčů azure a klíčů
Než začnete, ujistěte se, že byla nainstalovaná nejnovější verze modulu Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). V následujících příkladech příkazů nahraďte všechny ukázkové parametry vlastními názvy, umístěním a hodnotami klíčů, například *myResourceGroup*, *myKeyVault*, *myVM*a tak dále.

Prvním krokem je vytvoření trezoru klíčů Azure pro ukládání kryptografických klíčů. Azure Key Vaults můžete ukládat klíče, tajné klíče nebo hesla, které vám umožní bezpečně implementovat ve vašich aplikacích a službách. Pro šifrování virtuálních disků vytvoříte trezor klíčů pro uložení kryptografického klíče, který se používá k šifrování nebo dešifrování virtuálních disků. 

Povolte zprostředkovatele Azure Key Vault v rámci předplatného Azure pomocí [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)a pak vytvořte skupinu prostředků s [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v umístění *Ve východní části USA:*

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Trezor klíčů Azure, který obsahuje kryptografické klíče a přidružené výpočetní prostředky, jako je úložiště a samotný virtuální počítač, musí být ve stejné oblasti. Vytvořte azure key vault s [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) a povolte trezor klíčů pro použití s šifrováním disku. Zadejte jedinečný název trezoru klíčů pro *keyVaultName* následujícím způsobem:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Kryptografické klíče můžete ukládat pomocí softwaru nebo hardwarového modelu zabezpečení (HSM).  Standardní trezor klíčů ukládá pouze klíče chráněné softwarem. Použití objektu hsm vyžaduje prémiový trezor klíčů za příplatek. Chcete-li vytvořit prémiový trezor klíčů, přidejte v předchozím kroku parametr *-Sku "Premium".* Následující příklad používá od vytvoření standardního trezoru klíčů klíče chráněné softwarem. 

Pro oba modely ochrany platformy Azure musí být udělen přístup k vyžádání kryptografických klíčů při spuštění virtuálního počítače k dešifrování virtuálních disků. Vytvořte kryptografický klíč v trezoru klíčů pomocí [klávesy Add-AzureVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). Následující příklad vytvoří klíč s názvem *myKey*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Chcete-li otestovat proces šifrování, vytvořte virtuální virtuální město s [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální hosti s názvem *myVM* pomocí bitové kopie *datového centra Windows Server 2016.* Po zobrazení výzvy k zadání přihlašovacích údajů zadejte uživatelské jméno a heslo, které se má použít pro virtuální počítač:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Šifrování virtuálního počítače
Šifrujte svůj virtuální počítač pomocí [rozšíření Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) pomocí klíče Azure Key Vault. Následující příklad načte všechny informace o klíči a pak zašifruje virtuální ho virtuálního virtuálního ma s názvem *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Přijměte výzvu k pokračování šifrování virtuálního zařízení. Virtuální počítač se restartuje během procesu. Po dokončení procesu šifrování a restartování virtuálního počítače zkontrolujte stav šifrování pomocí [funkce Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Výstup se podobá následujícímu příkladu:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Další kroky
* Další informace o správě trezoru klíčů Azure najdete v [tématu Nastavení trezoru klíčů pro virtuální počítače](key-vault-setup.md).
* Další informace o šifrování disku, jako je například příprava šifrovaného vlastního virtuálního počítače k nahrání do Azure, najdete v [tématu Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
