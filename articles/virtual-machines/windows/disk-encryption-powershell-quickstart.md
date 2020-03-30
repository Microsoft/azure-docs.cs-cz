---
title: Vytvoření a šifrování virtuálního počítače s Windows s využitím Azure PowerShellu
description: V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShellu vytvořit a zašifrovat virtuální počítač s Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: b5e5b44742c85591b913b94e622c76a2aba111ce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72246078"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Úvodní příručka: Vytvoření a šifrování virtuálního počítače s Windows v Azure pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí modulu Azure PowerShell vytvořit virtuální počítač (VM) windows, vytvořit trezor klíčů pro ukládání šifrovacích klíčů a šifrovat virtuální počítač. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure s [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač Azure s [New-AzVM](/powershell/module/az.compute/new-azvm). Do rutiny je nutné zadat pověření. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Nasazení virtuálního počítače bude několik minut trvat. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvoření trezoru klíčů nakonfigurovaného pro šifrovací klíče

Šifrování disku Azure ukládá svůj šifrovací klíč v trezoru klíčů Azure. Vytvořte trezor klíčů s [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Chcete-li povolit trezor klíčů pro ukládání šifrovacích klíčů, použijte parametr -EnabledForDiskEncryption.

> [!Important]
> Každý trezor klíčů musí mít jedinečný název. Následující příklad vytvoří trezor klíčů s názvem *myKV*, ale musíte pojmenovat svůj něco jiného.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače

Šifrujte virtuální počítač pomocí [rozšíření Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension vyžaduje některé hodnoty z objektu trezoru klíčů. Tyto hodnoty můžete získat předáním jedinečný název trezoru klíčů [get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Po několika minutách se proces vrátí následující:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces šifrování můžete ověřit spuštěním příkazu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Pokud je šifrování povoleno, zobrazí se ve vráceném výstupu následující:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete pomocí rutiny [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků, virtuální hod a všechny související prostředky:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili trezor klíčů, který byl povolen pro šifrovací klíče, a zašifrovali virtuální počítač.  V dalším článku najdete další informace o požadavcích na službu Azure Disk Encryption pro virtuální počítače IaaS.

> [!div class="nextstepaction"]
> [Azure Disk Encryption – přehled](disk-encryption-overview.md)