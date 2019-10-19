---
title: Šifrování disků na virtuálním počítači s Windows v Azure | Microsoft Docs
description: Šifrování virtuálních disků na virtuálním počítači s Windows kvůli zvýšení zabezpečení pomocí Azure PowerShell
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
ms.openlocfilehash: 87777d3a6abfeaeac74fd69126cc3e71e11be825
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597859"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Šifrování virtuálních disků na virtuálním počítači s Windows
Pro rozšířené zabezpečení virtuálních počítačů a dodržování předpisů můžete virtuální disky v Azure šifrovat. Disky se šifrují pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Můžete řídit tyto kryptografické klíče a auditovat jejich použití. Tento článek popisuje, jak pomocí Azure PowerShell šifrovat virtuální disky na virtuálním počítači s Windows. Můžete také [Šifrovat virtuální počítače se systémem Linux](../linux/disk-encryption-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuálních počítačích s Windows jsou v klidovém stavu šifrované pomocí nástroje BitLocker. Za šifrování virtuálních disků v Azure se neúčtují žádné poplatky. Kryptografické klíče jsou uložené v Azure Key Vault pomocí ochrany softwaru nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) s certifikací podle standardů FIPS 140-2 úrovně 2. Kryptografické klíče slouží k šifrování a dešifrování virtuálních disků připojených k VIRTUÁLNÍmu počítači. Řízení těchto kryptografických klíčů a jejich použití může být prováděno. 

Postup pro šifrování virtuálního počítače je následující:

1. Vytvoření kryptografického klíče v Azure Key Vault.
1. Nakonfigurujte kryptografický klíč, který bude použitelný pro šifrování disků.
1. Povolte šifrování disku pro virtuální disky.
1. Požadované kryptografické klíče jsou požadovány od Azure Key Vault.
1. Virtuální disky se šifrují pomocí zadaného kryptografického klíče.


## <a name="requirements-and-limitations"></a>Požadavky a omezení

Podporované scénáře a požadavky na šifrování disku:

* Povolení šifrování pro nové virtuální počítače s Windows z Azure Marketplace imagí nebo vlastních imagí VHD
* Povolení šifrování u stávajících virtuálních počítačů s Windows v Azure.
* Povolení šifrování pro virtuální počítače s Windows, které jsou nakonfigurované pomocí prostorů úložiště.
* Zakázání šifrování v operačním systému a datových jednotkách pro virtuální počítače s Windows
* Virtuální počítače úrovně Standard, jako jsou virtuální počítače, D, DS, G a GS řady GS.

    > [!NOTE]
    > Všechny prostředky (včetně Key Vault, účtu úložiště a virtuálního počítače) musí být ve stejné oblasti a předplatném Azure.

Šifrování disku se v současné době nepodporuje v následujících scénářích:

* Virtuální počítače úrovně Basic.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Aktualizují se kryptografické klíče na už šifrovaném virtuálním počítači.
* Integrace s místní službou správy klíčů.


## <a name="create-an-azure-key-vault-and-keys"></a>Vytvoření Azure Key Vault a klíčů
Než začnete, ujistěte se, že je nainstalovaná nejnovější verze modulu Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). V následujících příkladech příkazů nahraďte všechny ukázkové parametry vlastními názvy, umístěním a klíčovými hodnotami, například *myResourceGroup*, *myKeyVault*, *myVM*a tak dále.

Prvním krokem je vytvoření Azure Key Vault k uložení kryptografických klíčů. Trezory klíčů Azure můžou ukládat klíče, tajné klíče nebo hesla, které vám umožní je bezpečně implementovat do svých aplikací a služeb. Pro šifrování virtuálního disku vytvoříte Key Vault k uložení kryptografického klíče, který se používá k šifrování nebo dešifrování virtuálních disků. 

Povolte poskytovatele Azure Key Vault v rámci předplatného Azure pomocí [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)a pak vytvořte skupinu prostředků pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v umístění *východní USA* :

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Azure Key Vault držíte kryptografické klíče a přidružené výpočetní prostředky, jako je úložiště, a samotný virtuální počítač musí být ve stejné oblasti. Vytvořte Azure Key Vault pomocí [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) a povolte Key Vault pro použití s šifrováním disku. Zadejte jedinečný Key Vault název pro *trezor* klíčů následujícím způsobem:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Kryptografické klíče můžete ukládat pomocí ochrany softwaru nebo hardwarového zabezpečení (HSM).  Standardní Key Vault ukládá pouze klíče chráněné softwarem. Použití modulu hardwarového zabezpečení (HSM) vyžaduje prémiové Key Vault za další náklady. Pokud chcete vytvořit prémiovou Key Vault, v předchozím kroku přidejte parametr *-SKU "Premium"* . Následující příklad používá klíče chráněné softwarem, protože jsme vytvořili standardní Key Vault. 

U obou modelů ochrany musí mít platforma Azure udělený přístup, aby vyžádala kryptografické klíče, když se virtuální počítač spustí k dešifrování virtuálních disků. Pomocí [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey)vytvořte v Key Vault kryptografický klíč. Následující příklad vytvoří klíč s názvem *myKey*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
K otestování procesu šifrování vytvořte virtuální počítač pomocí [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* s použitím image *Windows serveru 2016 Datacenter* . Po zobrazení výzvy k zadání přihlašovacích údajů zadejte uživatelské jméno a heslo, které se má použít pro virtuální počítač:

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
Pomocí klíče Azure Key Vault Zašifrujte svůj virtuální počítač pomocí [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) . Následující příklad načte všechny klíčové informace a pak zašifruje virtuální počítač s názvem *myVM*:

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

Pokud chcete pokračovat v šifrování virtuálního počítače, potvrďte výzvu. Virtuální počítač se restartuje během procesu. Jakmile proces šifrování dokončí a virtuální počítač se restartuje, zkontrolujte stav šifrování pomocí [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

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
* Další informace o správě Azure Key Vault najdete v tématu [nastavení Key Vault pro virtuální počítače](key-vault-setup.md).
* Další informace o šifrování disku, jako je například příprava šifrovaného vlastního virtuálního počítače pro nahrání do Azure, najdete v tématu [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
