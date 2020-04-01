---
title: Přesunutí virtuálních počítačů Azure na nové předplatné nebo skupinu prostředků
description: Pomocí Správce prostředků Azure přesuňte virtuální počítače do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478661"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny pro přesuny pro virtuální počítače

Tento článek popisuje scénáře, které nejsou aktuálně podporovány a kroky k přesunutí virtuálních počítačů se zálohou.

## <a name="scenarios-not-supported"></a>Scénáře nejsou podporovány

Následující scénáře ještě nejsou podporovány:

* Spravované disky v zónách dostupnosti nelze přesunout do jiného předplatného.
* Škálovací sady virtuálních počítačů se standardním nástrojem pro vyrovnávání zatížení skladové položky nebo standardní veřejnou IP jednotkou skladové položky nelze přesunout.
* Virtuální počítače vytvořené z prostředků Marketplace s připojenými plány nelze přesouvat mezi skupinami prostředků nebo předplatnými. Zrušení zřízení virtuálního počítače v aktuálním předplatném a znovu nasadit v novém předplatném.
* Virtuální počítače v existující virtuální síti nelze přesunout do nového předplatného, pokud nepřesouváte všechny prostředky ve virtuální síti.
* Virtuální počítače s nízkou prioritou a škálovací sady virtuálních počítačů s nízkou prioritou nelze přesouvat mezi skupinami prostředků nebo předplatnými.
* Virtuální počítače v sadě dostupnosti nelze přesunout jednotlivě.

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače se zálohováním Azure

Chcete-li přesunout virtuální počítače nakonfigurované pomocí služby Azure Backup, musíte odstranit body obnovení z úložiště.

Pokud je pro váš virtuální počítač povoleno [obnovitelné odstranění,](../../../backup/backup-azure-security-feature-cloud.md) nemůžete virtuální počítač přesunout, dokud jsou tyto body obnovení zachovány. Zakázat [obnovitelné odstranění](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete) nebo počkejte 14 dní po odstranění bodů obnovení.

### <a name="portal"></a>Portál

1. Vyberte virtuální počítač, který je nakonfigurovaný pro zálohování.

1. V levém podokně vyberte **zálohovat**.

1. Vyberte **možnost Zastavit zálohování**.

1. Vyberte **odstranit zpět data**.

1. Po dokončení odstranění můžete přesunout trezor a virtuální počítač do cílového předplatného. Po přesunutí můžete pokračovat v zálohování.

### <a name="powershell"></a>PowerShell

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s `AzureBackupRG_<location of your VM>_1` následujícím vzorem pojmenování: například AzureBackupRG_westus2_1
* Pokud v prostředí PowerShell, použijte rutinu `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Vyhledání prostředku `Microsoft.Compute/restorePointCollections` s typem, který má vzor pojmenování`AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstraňte tento prostředek. Tato operace odstraní pouze okamžité body obnovení, nikoli zálohovaná data v úschovně.

### <a name="azure-cli"></a>Azure CLI

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s `AzureBackupRG_<location of your VM>_1` následujícím vzorem pojmenování: například AzureBackupRG_westus2_1
* Pokud v CLI, použijte`az resource list -g AzureBackupRG_<location of your VM>_1`
* Vyhledání prostředku `Microsoft.Compute/restorePointCollections` s typem, který má vzor pojmenování`AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstraňte tento prostředek. Tato operace odstraní pouze okamžité body obnovení, nikoli zálohovaná data v úschovně.

## <a name="next-steps"></a>Další kroky

* Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).

* Informace o přesunutí trezorů služby Recovery Service pro zálohování naleznete v [tématu Omezení služby Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
