---
title: Vytvoření a šifrování virtuálního počítače s Linuxem pomocí Azure Powershellu
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Powershellu vytvořit a zašifrovat virtuální počítač SIP.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: a2cb8919ac0752c42f22e064d6201c7120fbc9b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970536"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Úvodní příručka: Vytvoření a šifrování virtuálního počítače s Linuxem v Azure pomocí Azure PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí modulu Azure PowerShell vytvořit virtuální počítač (VM) linux, vytvořit trezor klíčů pro ukládání šifrovacích klíčů a šifrovat virtuální počítač. Tento rychlý start používá image trhu Ubuntu 16.04 LTS od společnosti Canonical a Standard_D2S_V3 velikosti virtuálního počítače. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure s [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač Azure s [Novým AzVM](/powershell/module/az.compute/new-azvm)a předáte mu objekt konfigurace virtuálního počítače, který jste vytvořili výše.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

Nasazení virtuálního počítače bude několik minut trvat. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvoření trezoru klíčů nakonfigurovaného pro šifrovací klíče

Šifrování disku Azure ukládá svůj šifrovací klíč v trezoru klíčů Azure. Vytvořte trezor klíčů s [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Chcete-li povolit trezor klíčů pro ukládání šifrovacích klíčů, použijte parametr -EnabledForDiskEncryption.

> [!Important]
> Každý trezor klíčů musí mít název, který je jedinečný v rámci Azure. V níže uvedených příkladech nahraďte <> název trezoru jedinečný-klíč zvoleným názvem.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače

Šifrujte virtuální počítač pomocí [rozšíření Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension vyžaduje některé hodnoty z objektu trezoru klíčů. Tyto hodnoty můžete získat předáním jedinečný název trezoru klíčů [get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Po několika minutách se proces vrátí následující:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces šifrování můžete ověřit spuštěním příkazu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Pokud je šifrování povoleno, zobrazí se ve vráceném výstupu následující:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete pomocí rutiny [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků, virtuální hod a všechny související prostředky:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili trezor klíčů, který byl povolen pro šifrovací klíče, a zašifrovali virtuální počítač.  Přejdete k dalšímu článku, kde se dozvíte víc o Azure Disk Encryption pro virtuální počítače s Linuxem.

> [!div class="nextstepaction"]
> [Azure Disk Encryption – přehled](disk-encryption-overview.md)