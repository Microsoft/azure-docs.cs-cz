---
title: Přesun virtuálních počítačů Azure do nového předplatného nebo skupiny prostředků
description: K přesunutí virtuálních počítačů do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: faeba1c0d7342a4c00f19d4cee8d67b8dbde8e6a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528424"
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

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače s Azure Backup

Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, použijte následující řešení:

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<location of your VM>_1` například AzureBackupRG_westus2_1
* Pokud v Azure Portal, zaškrtněte "Zobrazit skryté typy"
* Pokud používáte prostředí PowerShell, použijte rutinu `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`.
* Pokud v rozhraní příkazového řádku, použijte `az resource list -g AzureBackupRG_<location of your VM>_1`
* Vyhledejte prostředek typu `Microsoft.Compute/restorePointCollections` se vzorem pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
* Po dokončení odstranění můžete trezor a virtuální počítač přesunout do cílového předplatného. Po přesunutí můžete pokračovat v zálohování bez ztráty dat.
* Informace o přesunutí trezorů služby Recovery Services pro zálohování najdete v tématu [omezení Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
