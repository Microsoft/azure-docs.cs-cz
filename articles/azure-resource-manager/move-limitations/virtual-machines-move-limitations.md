---
title: Přesunout do nové předplatné nebo skupinu prostředků Azure Virtual Machines | Dokumentace Microsoftu
description: Použití Azure Resource Manageru pro přesun virtuálních počítačů do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723542"
---
# <a name="move-guidance-for-virtual-machines"></a>Pokyny k přesunutí virtuálních počítačů

Tento článek popisuje scénáře, které nejsou aktuálně podporovány a postup přesunutí virtuálních počítačů pomocí služby backup.

## <a name="scenarios-not-supported"></a>Nepodporované scénáře

Zatím nejsou podporovány následující scénáře:

* Spravované disky v zónách dostupnosti nelze přesunout do jiného předplatného.
* Virtuální počítače pomocí certifikátu uloženého ve službě Key Vault můžete přesunout do nové skupiny prostředků ve stejném předplatném, ale ne napříč předplatnými.
* Škálovací sady virtuálních počítačů pomocí nástroje pro vyrovnávání zatížení standardní SKU nebo standardní veřejnou IP Adresou skladové položky nelze přesunout.
* Virtuální počítače vytvořené z Marketplace prostředky s plány připojené se nedají přesouvat mezi skupinami prostředků nebo předplatných. Zrušení zřízení virtuálního počítače v rámci aktuálního předplatného a znovu nasadit v rámci nového předplatného.
* Virtuální počítače v existující virtuální síť, ale nejsou přesouvá všechny prostředky ve virtuální síti.

## <a name="virtual-machines-with-azure-backup"></a>Virtuální počítače pomocí služby Azure Backup

Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, použijte následující alternativní řešení:

* Nalezení vašeho virtuálního počítače.
* Najít skupinu prostředků pomocí následující vzor pro pojmenování: `AzureBackupRG_<location of your VM>_1` například AzureBackupRG_westus2_1
* Pokud na webu Azure portal, pak zaškrtněte "Zobrazit skryté typy"
* Pokud v prostředí PowerShell, použijte `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` rutiny
* Pokud v rozhraní příkazového řádku, použijte `az resource list -g AzureBackupRG_<location of your VM>_1`
* Najít prostředek s typem `Microsoft.Compute/restorePointCollections` , který má vzor pro pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstraníte tento prostředek. Tato operace odstraní pouze body obnovení rychlé, ne zálohovaná data v trezoru.
* Po dokončení odstraňování můžete přesunout trezor a virtuální počítač do cílového odběru. Po přesunutí můžete pokračovat v zálohování bez ztráty v datech.
* Informace o přesunutí trezory služby Recovery Service pro zálohování, naleznete v tématu [omezení Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Další postup

Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
