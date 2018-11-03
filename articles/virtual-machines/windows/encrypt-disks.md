---
title: Šifrování disků na virtuálním počítači s Windows v Azure | Dokumentace Microsoftu
description: Šifrování virtuálních disků na virtuálním počítači s Windows pro zvýšení zabezpečení pomocí Azure Powershellu
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 48eb76e7e076b8496b32878b2292447b1ccbf7f6
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977119"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Šifrování virtuálních disků na virtuálním počítači s Windows
Vylepšené virtuálních počítačů (VM) zabezpečení a dodržování předpisů mohou být šifrována virtuálních disků v Azure. Disky jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografických klíčů a auditovat jejich použití. Tento článek popisuje, jak šifrování virtuálních disků na virtuálním počítači s Windows pomocí Azure Powershellu. Můžete také [šifrování virtuálního počítače s Linuxem pomocí Azure CLI](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuálních počítačích s Windows se šifrují při nečinnosti pomocí Bitlockeru. Neplatí žádné poplatky pro šifrování virtuálních disků v Azure. Kryptografické klíče ukládají do služby Azure Key Vault s použitím ochrany před softwarem, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Kryptografické klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Zachovat kontrolu jak z těchto kryptografických klíčů a auditovat jejich použití. 

Proces šifrování virtuálního počítače je následujícím způsobem:

1. Vytvoření kryptografického klíče do služby Azure Key Vault.
1. Nakonfigurujte kryptografického klíče má být použitelná pro šifrování disků.
1. Povolte disk encryption pro virtuální disky.
1. Vyžaduje kryptografické klíče jsou požadovány ze služby Azure Key Vault.
1. Virtuální disky jsou šifrované pomocí zadaného kryptografického klíče.


## <a name="requirements-and-limitations"></a>Požadavky a omezení

Podporované scénáře a požadavky na šifrování disku:

* Povolení šifrování na nové virtuální počítače Windows z vlastní Image virtuálního pevného disku nebo Image Azure Marketplace.
* Povolení šifrování na stávajících virtuálních počítačích s Windows v Azure.
* Povolení šifrování na virtuálních počítačích s Windows, které jsou nakonfigurované pomocí prostorů úložiště.
* Zakázáním šifrování u operačního systému a datové disky pro virtuální počítače s Windows.
* Úroveň Standard virtuálních počítačů, jako je například A, D, DS, G a GS řady virtuálních počítačů.

    > [!NOTE]
    > Všechny prostředky (včetně služby Key Vault, účet úložiště a virtuálních počítačů) musí být ve stejné oblasti Azure a předplatné.

Šifrování disku není aktuálně podporována v následujících scénářích:

* Úroveň Basic virtuálních počítačů.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Aktualizuje se kryptografické klíče na Virtuálním počítači již šifrované.
* Integrace se službou správy klíčů místní.


## <a name="create-an-azure-key-vault-and-keys"></a>Vytvoření služby Azure Key Vault a klíči
Než začnete, ujistěte se, že byla nainstalována nejnovější verze modulu Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). V následujících příkladech nahraďte veškeré ukázkovými parametry s názvy, umístění a hodnoty klíče, jako například *myResourceGroup*, *myKeyVault*, *myVM*, a atd.

Prvním krokem je vytvoření služby Azure Key Vault pro ukládání kryptografických klíčů. Azure Key Vault můžete ukládat klíče, tajné kódy nebo hesla, které umožňují bezpečný je implementovat ve svých aplikacích a službách. Šifrování virtuálních disků vytvoříte trezor klíčů k uložení kryptografického klíče, který slouží k šifrování nebo dešifrování virtuálních disků. 

Povolit zprostředkovatele služby Azure Key Vault v rámci vašeho předplatného Azure s [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), vytvořte skupinu prostředků pomocí [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v *USA – východ* umístění:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Azure Key Vault, že kryptografické klíče a přidružených výpočetních prostředků, jako jsou úložiště a virtuální počítač musí být ve stejné oblasti. Vytvoření služby Azure Key Vault s [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) a povolte službě Key Vault pro použití s šifrování disku. Zadejte jedinečný název služby Key Vault pro *keyVaultName* následujícím způsobem:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Uložení šifrovacích klíčů s použitím softwaru nebo ochrana modelů hardwarového zabezpečení (HSM).  Standardní služby Key Vault se uchovávají pouze softwarově chráněné klíče. Použití modulu hardwarového zabezpečení vyžaduje na úrovni premium služby Key Vault za další poplatek. K vytvoření služby Key Vault na úrovni premium, v předchozím kroku přidejte *- Sku "Premium"* parametru. Následující příklad používá softwarově chráněné klíče, protože jsme vytvořili standardní služby Key Vault. 

Pro oba modely ochrany platformy Azure musí mít udělen přístup k vyžádání kryptografických klíčů při spuštění virtuálního počítače k dešifrování virtuálních disků. Vytvoření kryptografické klíče v Key Vault s [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). Následující příklad vytvoří klíč s názvem *myKey*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Pokud chcete otestovat proces šifrování, vytvořte virtuální počítač s [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* pomocí *systému Windows Server 2016 Datacenter* bitové kopie. Po zobrazení výzvy k zadání pověření zadejte uživatelské jméno a heslo, které se použije pro váš virtuální počítač:

```azurepowershell-interactive
$cred = Get-Credential

New-AzureRmVm `
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
Šifrování virtuálního počítače s [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) pomocí klíče Azure Key Vault. Následující příklad načte všechny informace o klíči pak zašifruje virtuální počítač s názvem *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Přijetí výzvy a pokračujte šifrování virtuálního počítače. Během procesu restartování virtuálního počítače. Jakmile se dokončí proces šifrování a virtuální počítač byl restartován, zkontrolujte stav šifrování s [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Výstup se podobá následujícímu příkladu:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Další postup
* Další informace o správě služby Azure Key Vault najdete v tématu [nastavení služby Key Vault pro virtuální počítače](key-vault-setup.md).
* Další informace o šifrování disku, například příprava šifrované vlastního virtuálního počítače k nahrání do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
