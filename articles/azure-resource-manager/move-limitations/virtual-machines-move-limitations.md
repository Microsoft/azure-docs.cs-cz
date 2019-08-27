---
title: Přesunout Azure Virtual Machines do nového předplatného nebo skupiny prostředků | Microsoft Docs
description: K přesunutí virtuálních počítačů do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 7b9cce7ac367f42329e3198c75a7640a205d01fe
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035530"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny pro přesunutí virtuálních počítačů

Tento článek popisuje scénáře, které aktuálně nejsou podporované, a Postup přesunutí virtuálních počítačů se zálohou.

## <a name="scenarios-not-supported"></a>Nepodporované scénáře

Následující scénáře se zatím nepodporují:

* Managed Disks v Zóny dostupnosti nejde přesunout do jiného předplatného.
* Virtual Machine Scale Sets se standardní SKU Load Balancer nebo veřejnou IP adresou standardní SKU nejde přesunout.
* Virtuální počítače vytvořené z Marketplace prostředky s plány připojené se nedají přesouvat mezi skupinami prostředků nebo předplatných. Zrušte zřízení virtuálního počítače v aktuálním předplatném a znovu ho nasaďte do nového předplatného.
* Virtuální počítače ve stávající virtuální síti, ale nepřesouváte všechny prostředky ve virtuální síti.
* Virtuální počítače s nízkou prioritou a sady škálování virtuálních počítačů s nízkou prioritou nelze přesouvat mezi skupinami prostředků nebo předplatnými.

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače s Azure Backup

Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, použijte následující řešení:

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s následujícím vzorem pojmenování `AzureBackupRG_<location of your VM>_1` : například AzureBackupRG_westus2_1
* Pokud na webu Azure portal, pak zaškrtněte "Zobrazit skryté typy"
* Pokud v prostředí PowerShell, použijte `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` rutiny
* Pokud v rozhraní příkazového řádku, použijte `az resource list -g AzureBackupRG_<location of your VM>_1`
* Najít prostředek s typem `Microsoft.Compute/restorePointCollections` , který má vzor pojmenování`AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
* Po dokončení odstranění můžete trezor a virtuální počítač přesunout do cílového předplatného. Po přesunutí můžete pokračovat v zálohování bez ztráty dat.
* Informace o přesunutí trezorů služby Recovery Services pro zálohování najdete v tématu [omezení Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo](../resource-group-move-resources.md)předplatného.
