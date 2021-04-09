---
title: Přesun virtuálních počítačů Azure do nového předplatného nebo skupiny prostředků
description: K přesunutí virtuálních počítačů do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ad7023f309f1ca948711eaa9bdf3867d2ef7a6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104905"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny pro přesunutí virtuálních počítačů

Tento článek popisuje scénáře, které aktuálně nejsou podporované, a Postup přesunutí virtuálních počítačů se zálohou.

## <a name="scenarios-not-supported"></a>Nepodporované scénáře

Následující scénáře se zatím nepodporují:

* Virtual Machine Scale Sets se standardní SKU Load Balancer nebo veřejnou IP adresou standardní SKU nejde přesunout.
* Virtuální počítače ve stávající virtuální síti nejde přesunout do nového předplatného, když nepřesouváte všechny prostředky ve virtuální síti.
* Virtuální počítače vytvořené z prostředků z Marketplace s připojenými plány se nedají přesunout mezi předplatnými. Potenciální řešení najdete v tématu [virtuální počítače s plány na webu Marketplace](#virtual-machines-with-marketplace-plans).
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

## <a name="virtual-machines-with-marketplace-plans"></a>Virtuální počítače s plány Marketplace

Virtuální počítače vytvořené z prostředků z Marketplace s připojenými plány se nedají přesunout mezi předplatnými. Pokud chcete toto omezení obejít, můžete zrušit zřízení virtuálního počítače v aktuálním předplatném a znovu ho nasadit do nového předplatného. Následující kroky vám pomůžou znovu vytvořit virtuální počítač v novém předplatném. Nemusí ale fungovat pro všechny scénáře. Pokud plán již není na webu Marketplace k dispozici, tyto kroky nebudou fungovat.

1. Zkopírujte informace o plánu.

1. Buď naklonujte disk s operačním systémem do cílového předplatného, nebo po odstranění virtuálního počítače ze zdrojového předplatného přesuňte původní disk.

1. V části cílové předplatné přijměte podmínky pro Marketplace vašeho plánu. Podmínky můžete přijmout spuštěním následujícího příkazu PowerShellu:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   Nebo můžete vytvořit novou instanci virtuálního počítače s plánem prostřednictvím portálu. Po přijetí podmínek v novém předplatném můžete virtuální počítač odstranit.

1. V cílovém předplatném znovu vytvořte virtuální počítač z klonovaného disku s operačním systémem pomocí PowerShellu, CLI nebo šablony Azure Resource Manager. Zahrňte plán na tržišti, který je připojený k disku. Informace o plánu by se měly shodovat s plánem, který jste zakoupili v rámci nového předplatného.

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače s Azure Backup

Chcete-li přesunout virtuální počítače nakonfigurované s Azure Backup, je nutné odstranit body obnovení z trezoru.

Pokud je pro virtuální počítač povolené [obnovitelné odstranění](../../../backup/soft-delete-virtual-machines.md) , nemůžete virtuální počítač přesunout, pokud jsou tyto body obnovení zachované. Buď [zakažte obnovitelné odstranění](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) nebo počkejte 14 dní po odstranění bodů obnovení.

### <a name="portal"></a>Portál

1. Dočasně zastavte zálohování a udržujte zálohovaná data.
2. Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, proveďte následující kroky:

   1. Najděte umístění virtuálního počítače.
   2. Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<VM location>_1` . Název je například ve formátu *AzureBackupRG_westus2_1*.
   3. V Azure Portal zaškrtnout **Zobrazit skryté typy**.
   4. Vyhledejte prostředek typu **Microsoft. COMPUTE/restorePointCollections** , který má vzor pojmenování `AzureBackup_<VM name>_###########` .
   5. Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
   6. Po dokončení operace odstranění můžete virtuální počítač přesunout.

3. Přesuňte virtuální počítač do cílové skupiny prostředků.
4. Obnovte zálohu.

### <a name="powershell"></a>PowerShell

1. Najděte umístění virtuálního počítače.

1. Vyhledejte skupinu prostředků se vzorem pojmenování `AzureBackupRG_<VM location>_1` . Název může být například `AzureBackupRG_westus2_1` .

1. Pokud přesouváte jenom jeden virtuální počítač, Získejte pro tento virtuální počítač kolekci bodů obnovení.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Pokud přesouváte všechny virtuální počítače s back-výstupními zdroji v tomto umístění, získáte pro tyto virtuální počítače kolekce bodů obnovení.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Odstraňte jednotlivé prostředky. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. Najděte umístění virtuálního počítače.

1. Vyhledejte skupinu prostředků se vzorem pojmenování `AzureBackupRG_<VM location>_1` . Název může být například `AzureBackupRG_westus2_1` .

1. Pokud přesouváte jenom jeden virtuální počítač, Získejte pro tento virtuální počítač kolekci bodů obnovení.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Pokud přesouváte všechny virtuální počítače s back-výstupními zdroji v tomto umístění, získáte pro tyto virtuální počítače kolekce bodů obnovení.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Odstraňte jednotlivé prostředky. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Další kroky

* Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).

* Informace o přesunutí trezorů služby Recovery Services pro zálohování najdete v tématu [omezení Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
