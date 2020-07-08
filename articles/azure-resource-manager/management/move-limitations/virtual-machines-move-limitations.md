---
title: Přesun virtuálních počítačů Azure do nového předplatného nebo skupiny prostředků
description: K přesunutí virtuálních počítačů do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: c85ec175d802a29de7a8a87ee7a51c0916762a5a
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044545"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny pro přesunutí virtuálních počítačů

Tento článek popisuje scénáře, které aktuálně nejsou podporované, a Postup přesunutí virtuálních počítačů se zálohou.

## <a name="scenarios-not-supported"></a>Nepodporované scénáře

Následující scénáře se zatím nepodporují:

* Managed Disks v Zóny dostupnosti nejde přesunout do jiného předplatného.
* Virtual Machine Scale Sets se standardní SKU Load Balancer nebo veřejnou IP adresou standardní SKU nejde přesunout.
* Virtuální počítače vytvořené z prostředků z Marketplace s připojenými plány se nedají přesouvat mezi skupinami prostředků nebo předplatnými. Zrušte zřízení virtuálního počítače v aktuálním předplatném a znovu ho nasaďte do nového předplatného.
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
   2. Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<location of your VM>_1` . Například *AzureBackupRG_westus2_1*
   3. V Azure Portal zaškrtnout **Zobrazit skryté typy**.
   4. Vyhledejte prostředek typu **Microsoft. COMPUTE/restorePointCollections** , který má vzor pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
   6. Po dokončení operace odstranění můžete virtuální počítač přesunout.

3. Přesuňte virtuální počítač do cílové skupiny prostředků.
4. Obnovte zálohu.

### <a name="powershell"></a>PowerShell

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<location of your VM>_1` například AzureBackupRG_westus2_1
* Pokud používáte prostředí PowerShell, použijte `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` rutinu.
* Najít prostředek s typem `Microsoft.Compute/restorePointCollections` , který má vzor pojmenování`AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

### <a name="azure-cli"></a>Azure CLI

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<location of your VM>_1` například AzureBackupRG_westus2_1
* Pokud v rozhraní příkazového řádku, použijte`az resource list -g AzureBackupRG_<location of your VM>_1`
* Najít prostředek s typem `Microsoft.Compute/restorePointCollections` , který má vzor pojmenování`AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.

## <a name="next-steps"></a>Další kroky

* Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).

* Informace o přesunutí trezorů služby Recovery Services pro zálohování najdete v tématu [omezení Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
