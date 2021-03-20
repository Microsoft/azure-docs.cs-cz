---
title: Obnovitelné odstranění u virtuálních počítačů
description: Přečtěte si, jak obnovitelné odstranění pro virtuální počítače zajišťuje větší zabezpečení záloh.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89022237"
---
# <a name="soft-delete-for-virtual-machines"></a>Obnovitelné odstranění u virtuálních počítačů

Obnovitelné odstranění pro virtuální počítače chrání zálohy virtuálních počítačů před nezamýšleným odstraněním. I po odstranění záloh jsou zachovány stav obnovitelného odstranění po dobu 14 dalších dnů.

> [!NOTE]
> Obnovitelné odstranění chrání pouze Odstraněná data zálohy. Pokud se virtuální počítač odstraní bez zálohy, funkce obnovitelného odstranění data nezachová. Všechny prostředky by měly být chráněné pomocí Azure Backup, aby se zajistila plná odolnost.
>

## <a name="supported-regions"></a>Podporované oblasti

Obnovitelné odstranění se v současné době podporuje v Středozápadní USA, Východní Asie, Kanadě – střed, Kanada – východ, Francie – střed, Francie – jih, Korea – jih, Jižní Korea, Velká Británie – jih, Velká Británie – západ, Austrálie – východ, Austrálie – jih, Severní Evropa, Západní USA, západní USA 2, Střed USA, Jižní Východní Asie, Střed USA – sever, Střed USA – jih, Japonsko – západ, Japonsko – západ, Indie – střed, Indie – jih , Švýcarsko – sever, Švýcarsko – západ, Norsko – západ, Norsko – východ a všechny národní oblasti.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Obnovitelné odstranění pro virtuální počítače pomocí Azure Portal

1. K odstranění zálohovaných dat virtuálního počítače je třeba zastavit zálohování. V Azure Portal přejděte do svého trezoru Recovery Services, klikněte pravým tlačítkem na zálohovanou položku a vyberte **Zastavit zálohování**.

   ![Snímek obrazovky Azure Portal zálohované položky](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. V následujícím okně budete mít možnost odstranit nebo zachovat data záloh. Pokud zvolíte možnost **Odstranit zálohovaná data** a pak **Zastavit zálohování**, zálohování virtuálního počítače se trvale neodstraní. Místo toho se zálohovaná data uchovávají 14 dnů ve stavu tichého odstranění. Pokud je zvolená možnost **Odstranit zálohovaná data** , pošle se na nakonfigurované ID e-mailu e-mailové upozornění s informací o uživateli, že pro zálohovaná data zbývá 14 dní rozšířené uchovávání dat. E-mailová výstraha se také pošle na 12. den, který vychází ze zbývajících dvou dnů, než se odobnovení Odstraněná data. Odstranění se odsadí do patnáctého dne, kdy dojde k trvalému odstranění a pošle se poslední e-mailová výstraha informující o trvalém odstranění dat.

   ![Snímek obrazovky Azure Portal, zastavení záložní obrazovky](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Během těchto 14 dnů se v Recovery Services trezoru u obnovitelného odstraněného virtuálního počítače zobrazí červená ikona "slabého odstranění" vedle něho.

   ![Snímek obrazovky Azure Portal, virtuální počítač ve stavu tichého odstranění](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Pokud se v trezoru nacházejí nějaké záložní položky odstraněné, trezor se v tuto chvíli nedá odstranit. Zkuste trezor odstranit, až se zálohované položky trvale odstraní a v trezoru nejsou žádné položky v tichém odstraněném stavu.

4. Aby bylo možné obnovit částečný odstraněný virtuální počítač, musí být nejprve obnoven. Pokud ho chcete obnovit, vyberte virtuální počítač s příjemným odstraněnou a potom vyberte možnost **obnovit**.

   ![Snímek obrazovky Azure Portal, odstranění virtuálního počítače](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Zobrazí se okno upozornění, že pokud je zvoleno možnost zrušit odstranění, všechny body obnovení pro virtuální počítač budou neodstraněny a budou k dispozici pro operaci obnovení. Virtuální počítač se zachová ve stavu zastavit ochranu se zachováním dat a zálohy se pozastaví a zálohovaná data se trvale zachovají beze zásad zálohování.

   ![Snímek obrazovky Azure Portal, potvrďte odstranění virtuálního počítače.](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   V tuto chvíli můžete virtuální počítač obnovit taky tak, že z vybraného bodu obnovení vyberete **obnovit virtuální počítač** .  

   ![Snímek obrazovky Azure Portal, obnovení možnosti virtuálního počítače](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Systém uvolňování paměti spustí a vyčistí body obnovení s vypršenou platností až poté, co uživatel provede operaci **obnovení zálohy** .

5. Po dokončení procesu zrušení odstranění se stav vrátí do části zastavit zálohování s daty zachovat a pak můžete zvolit **pokračovat v zálohování**. Operace **obnovit zálohování** vrátí zpět zálohovanou položku v aktivním stavu, která je přidružená k zásadám zálohování vybraným uživatelem, který definuje plány zálohování a uchovávání.

   ![Snímek obrazovky Azure Portal, obnovit možnost zálohování](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Obnovitelné odstranění pro virtuální počítače pomocí Azure PowerShell

> [!IMPORTANT]
> Verze AZ. RecoveryServices, která je nutná k použití obnovitelného odstranění pomocí Azure PowerShell, je minimální 2.2.0. Použijte ```Install-Module -Name Az.RecoveryServices -Force``` k získání nejnovější verze.

Jak je uvedeno výše pro Azure Portal, sekvence kroků je stejná i při použití Azure PowerShell.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Odstraňte zálohovanou položku pomocí Azure PowerShell

Odstraňte zálohovanou položku pomocí rutiny [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) prostředí PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

' DeleteState ' zálohované položky se změní z ' NotDeleted ' na ' ToBeDeleted '. Data zálohy se uchovávají 14 dnů. Chcete-li obnovit operaci odstranění, je třeba provést příkaz zpět a odstranit.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Zrušení operace odstranění pomocí Azure PowerShell

Nejdřív načtěte příslušnou zálohovanou položku, která je ve stavu tichého odstranění (tj., chystá se odstranit).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Pak proveďte operaci vrácení zpět se změnami pomocí rutiny [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) prostředí PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

' DeleteState ' zálohované položky se vrátí na ' NotDeleted '. Ochrana se ale pořád zastaví. [Obnovte zálohu](./backup-azure-vms-automation.md#change-policy-for-backup-items) pro opětovné povolení ochrany.

## <a name="soft-delete-for-vms-using-rest-api"></a>Obnovitelné odstranění pro virtuální počítače pomocí REST API

- Odstraňte zálohy pomocí REST API, jak je uvedeno [zde](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Pokud chcete tyto operace odstranění zrušit, přečtěte si postup uvedený [tady](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).

## <a name="how-to-disable-soft-delete"></a>Jak zakázat obnovitelné odstranění

Zakázání této funkce se nedoporučuje. Jediným případem, kdy byste měli zvážit zakázání obnovitelného odstranění, je, že plánujete přesunutí chráněných položek do nového trezoru a nemůžete počkat 14 dní před odstraněním a obnovením ochrany (například v testovacím prostředí). Pokyny, jak zakázat obnovitelné odstranění, najdete v tématu [povolení a zakázání obnovitelného odstranění](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Nejčastější dotazy](backup-azure-security-feature-cloud.md#frequently-asked-questions) týkající se obnovitelného odstranění
- Přečtěte si o všech [funkcích zabezpečení v Azure Backup](security-overview.md)
