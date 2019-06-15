---
title: Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Powershellu
description: V tomto rychlém startu se dozvíte, jak používat prostředí Azure PowerShell k vytvoření a šifrování virtuálního počítače s Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: c10c9c6bd757125fa1f91d4442fc1c8afd24d10e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081452"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Rychlý start: Vytvoření a šifrování virtuálního počítače s Windows v Azure pomocí Powershellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak používat modul Azure PowerShell k vytvoření virtuálního počítače s Windows (VM), vytvořte službu Key Vault pro ukládání šifrovacích klíčů a šifrování virtuálního počítače. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm). Musíte zadat přihlašovací údaje k rutině. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Nasazení virtuálního počítače bude několik minut trvat. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvořte službu Key Vault nakonfigurované pro šifrovací klíče

Azure disk encryption ukládá jeho šifrovacího klíče do služby Azure Key Vault. Vytvoření služby Key Vault pomocí [nové AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Pokud chcete povolit služby Key Vault pro ukládání šifrovacích klíčů, použijte parametr - EnabledForDiskEncryption.

> [!Important]
> Každá služba Key Vault musí mít jedinečný název. Následující příklad vytvoří trezor klíčů s názvem *myKV*, ale je nutné jenom pojmenovat něco jiného.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače

Šifrování virtuálního počítače s [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension vyžaduje některé hodnoty z objektu vaší služby Key Vault. Tyto hodnoty lze získat předáním jedinečný název trezoru klíčů na [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Po několika minutách proces vrátí následující hodnoty:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces šifrování můžete ověřit spuštěním [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Když je povoleno šifrování, zobrazí se následující ve vrácené výstupu:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) rutina pro odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili službu Key Vault, který byl povolit pro šifrovací klíče a šifrované virtuální počítač.  V dalším článku najdete další informace o požadavcích na službu Azure Disk Encryption pro virtuální počítače IaaS.

> [!div class="nextstepaction"]
> [Požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)