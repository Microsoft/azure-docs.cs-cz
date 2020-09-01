---
title: Přesun virtuálních počítačů Azure do nového předplatného nebo skupiny prostředků
description: K přesunutí virtuálních počítačů do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230955"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny pro přesunutí virtuálních počítačů

Tento článek popisuje scénáře, které aktuálně nejsou podporované, a Postup přesunutí virtuálních počítačů se zálohou.

## <a name="scenarios-not-supported"></a>Nepodporované scénáře

Následující scénáře se zatím nepodporují:

* Virtual Machine Scale Sets se standardní SKU Load Balancer nebo veřejnou IP adresou standardní SKU nejde přesunout.
* Virtuální počítače vytvořené z prostředků z Marketplace s připojenými plány se nedají přesunout mezi předplatnými. Zrušte zřízení virtuálního počítače v aktuálním předplatném a znovu ho nasaďte do nového předplatného.
* Virtuální počítače ve stávající virtuální síti nejde přesunout do nového předplatného, když nepřesouváte všechny prostředky ve virtuální síti.
* Virtuální počítače s nízkou prioritou a virtuální počítače s nízkou prioritou se nedají přesouvat mezi skupinami prostředků nebo předplatnými.
* Virtuální počítače ve skupině dostupnosti se nedají přesunout jednotlivě.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Virtuální počítač, který je integrovaný s trezorem klíčů, nemůžete přesunout do implementace [Azure Disk Encryption pro virtuální](../../../virtual-machines/linux/disk-encryption-overview.md) počítače se systémem Linux nebo [Azure Disk Encryption pro virtuální počítače s Windows](../../../virtual-machines/windows/disk-encryption-overview.md). Chcete-li virtuální počítač přesunout, je nutné zakázat šifrování.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače s Azure Backup

Chcete-li přesunout virtuální počítače nakonfigurované s Azure Backup, je nutné odstranit body obnovení z trezoru.

Pokud je pro virtuální počítač povolené [obnovitelné odstranění](../../../backup/backup-azure-security-feature-cloud.md) , nemůžete virtuální počítač přesunout, pokud jsou tyto body obnovení zachované. Buď [zakažte obnovitelné odstranění](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) nebo počkejte 14 dní po odstranění bodů obnovení.

### <a name="portal"></a>Portál

1. Dočasné zastavení zálohování a uchování zálohovaných dat.
2. Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, proveďte následující kroky:

   1. Najděte umístění virtuálního počítače.
   2. Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<VM location>_1` . Název je například ve formátu *AzureBackupRG_westus2_1*.
   3. V Azure Portal zaškrtnout **Zobrazit skryté typy**.
   4. Vyhledejte prostředek typu **Microsoft. COMPUTE/restorePointCollections** , který má vzor pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
   6. Po dokončení operace odstranění můžete virtuální počítač přesunout.

3. Přesuňte virtuální počítač do cílové skupiny prostředků.
4. Obnovte zálohu.

### <a name="powershell"></a>PowerShell

1. Najděte umístění virtuálního počítače.

1. Vyhledejte skupinu prostředků se vzorem pojmenování `AzureBackupRG_<VM location>_1` . Název může být například `AzureBackupRG_westus2_1` .

1. Pro získání kolekce bodů obnovení použijte následující příkaz.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. Najděte umístění virtuálního počítače.

1. Vyhledejte skupinu prostředků se vzorem pojmenování `AzureBackupRG_<VM location>_1` . Název může být například `AzureBackupRG_westus2_1` .

1. K získání kolekce bodů obnovení použijte následující příkaz.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. Vyhledejte ID prostředku pro prostředek se vzorem pojmenování. `AzureBackup_<VM name>_###########`

1. Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>Další kroky

* Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).

* Informace o přesunutí trezorů služby Recovery Services pro zálohování najdete v tématu [omezení Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
