---
title: Přesunutí virtuálních počítačů Azure na nové předplatné nebo skupinu prostředků
description: Pomocí Správce prostředků Azure přesuňte virtuální počítače do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479758"
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

Pokud chcete přesunout virtuální počítače nakonfigurované pomocí Azure Backup, použijte následující řešení:

* Najděte umístění virtuálního počítače.
* Vyhledejte skupinu prostředků s `AzureBackupRG_<location of your VM>_1` následujícím vzorem pojmenování: například AzureBackupRG_westus2_1
* Pokud na webu Azure Portal, pak zkontrolujte "Zobrazit skryté typy"
* Pokud v prostředí PowerShell, použijte rutinu `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Pokud v CLI, použijte`az resource list -g AzureBackupRG_<location of your VM>_1`
* Vyhledání prostředku `Microsoft.Compute/restorePointCollections` s typem, který má vzor pojmenování`AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstraňte tento prostředek. Tato operace odstraní pouze okamžité body obnovení, nikoli zálohovaná data v úschovně.
* Po dokončení odstranění můžete přesunout trezor a virtuální počítač do cílového předplatného. Po přesunutí můžete pokračovat v zálohování bez ztráty dat.
* Informace o přesunutí trezorů služby Recovery Service pro zálohování naleznete v [tématu Omezení služby Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Další kroky

Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
