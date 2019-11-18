---
title: Přesun virtuálních počítačů Azure do nového předplatného nebo skupiny prostředků
description: K přesunutí virtuálních počítačů do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b03e9618e621216f74cb02828183df7ee6b502ea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150934"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny pro přesunutí virtuálních počítačů

Tento článek popisuje scénáře, které aktuálně nejsou podporované, a Postup přesunutí virtuálních počítačů se zálohou.

## <a name="scenarios-not-supported"></a>Nepodporované scénáře

Následující scénáře se zatím nepodporují:

* Managed Disks v Zóny dostupnosti nejde přesunout do jiného předplatného.
* Virtual Machine Scale Sets se standardní SKU Load Balancer nebo veřejnou IP adresou standardní SKU nejde přesunout.
* Virtuální počítače vytvořené z Marketplace prostředky s plány připojené se nedají přesouvat mezi skupinami prostředků nebo předplatných. Zrušte zřízení virtuálního počítače v aktuálním předplatném a znovu ho nasaďte do nového předplatného.
* Virtuální počítače ve stávající virtuální síti nejde přesunout do nového předplatného, když nepřesouváte všechny prostředky ve virtuální síti.
* Virtuální počítače s nízkou prioritou a virtuální počítače s nízkou prioritou se nedají přesouvat mezi skupinami prostředků nebo předplatnými.
* Virtuální počítače ve skupině dostupnosti se nedají přesunout jednotlivě.

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače s Azure Backup

Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, použijte následující řešení:

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<location of your VM>_1` například AzureBackupRG_westus2_1
* Pokud na webu Azure portal, pak zaškrtněte "Zobrazit skryté typy"
* Pokud v prostředí PowerShell, použijte `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` rutiny
* Pokud v rozhraní příkazového řádku, použijte `az resource list -g AzureBackupRG_<location of your VM>_1`
* Vyhledejte prostředek typu `Microsoft.Compute/restorePointCollections` se vzorem pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
* Po dokončení odstranění můžete trezor a virtuální počítač přesunout do cílového předplatného. Po přesunutí můžete pokračovat v zálohování bez ztráty dat.
* Informace o přesunutí trezorů služby Recovery Services pro zálohování najdete v tématu [omezení Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
