---
title: Vytvoření a šifrování virtuálního počítače s Linuxem pomocí Azure Powershellu
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Powershellu k vytvoření a šifrování virtuálního počítače s Linuxem
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 7c44ffefccd4d78713d29aa2f9ab6006b8ba819e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081480"
---
# <a name="quickstart-create-and-encrypt-a-linux-virtual-machine-in-azure-with-powershell"></a>Rychlý start: Vytvoření a šifrování virtuálního počítače s Linuxem v Azure pomocí Powershellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak používat modul Azure PowerShell k vytvoření virtuálního počítače s Linuxem (VM), vytvořte službu Key Vault pro ukládání šifrovacích klíčů a šifrování virtuálního počítače. Tento rychlý start využívá image marketplace Ubuntu 16.04 LTS od firmy Canonical a velikost virtuálního počítače Standard_D2S_V3. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm), předáním konfigurace virtuálního počítače objektu, kterou jste vytvořili výše.

```powershell
$securePassword = ConvertTo-SecureString 'AZUREuserPA$$W0RD' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest  -Size Standard_D2S_V3
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

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
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
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
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