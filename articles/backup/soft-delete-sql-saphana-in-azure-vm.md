---
title: Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a SAP HANA v úlohách virtuálních počítačů Azure
description: Přečtěte si, jak obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a SAP HANA v úlohách virtuálních počítačů Azure zajišťuje větší zabezpečení záloh.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254291"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a SAP HANA v úlohách virtuálních počítačů Azure

Azure Backup nyní poskytuje obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a SAP HANA v úlohách virtuálních počítačů Azure. To je navíc k již podporovanému [scénáři obnovitelného odstranění virtuálního počítače Azure](soft-delete-virtual-machines.md).

[Obnovitelné odstranění](backup-azure-security-feature-cloud.md) je funkce zabezpečení, která umožňuje chránit zálohovaná data i po jejím odstranění. Pomocí obnovitelného odstranění, a to i v případě, že škodlivý objekt actor odstraní zálohu databáze (nebo se data zálohují omylem), se zálohovaná data uchovávají po dobu 14 dalších dnů. To umožňuje obnovení této zálohované položky bez ztráty dat. Tato dodatečná doba uchovávání dat ze zálohy 14 dnů ve stavu "obnovitelné odstranění" neúčtují žádné náklady na zákazníka.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure pomocí Azure Portal

>[!NOTE]
>Tyto pokyny platí také pro SAP HANA na virtuálním počítači Azure.

1. Chcete-li odstranit data zálohy databáze v systému SQL Server, je nutné zálohování zastavit. V Azure Portal přejdete do svého trezoru Recovery Services, přejdete na položku zálohování a kliknete na **Zastavit zálohování**.

   ![Zastavit zálohování](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. V následujícím okně budete mít možnost odstranit nebo zachovat data záloh. Pokud zvolíte možnost **Odstranit zálohovaná data**, zálohování databáze se trvale neodstraní. Místo toho se zálohovaná data uchovávají 14 dnů ve stavu tichého odstranění. Odstranění se odvozuje do patnáctého dne s běžnými e-maily s výstrahami v prvním, 12 a 15 dnech o stavu zálohování databáze pro uživatele.

   ![Odstranění zálohovaných dat](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Během těchto 14 dnů se v Recovery Services trezoru zobrazí zpráva s příznakem "slabé odstranění" vedle ní.

   ![Obnovitelné odstraněné položky](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Chcete-li obnovit databázi s přípouštějínou odstraněnou, je nutné ji nejprve zrušit. Pokud ho chcete obnovit, zvolte databázi s přípouštějínou odstraněnou a pak vyberte možnost zrušit **odstranění**.

   ![Obnovit databázi](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   V okně se zobrazí upozornění, že pokud je zvoleno možnost zrušit odstranění, všechny body obnovení pro databázi budou obnoveny a budou k dispozici pro provádění operace obnovení. Zálohovaná položka se zachová ve stavu zastavit ochranu se zachováním dat a zálohy se pozastaví a zálohovaná data se trvale zachovají beze zásad zálohování.

   ![Upozornění na odstranění](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. V tomto okamžiku můžete také obnovit data výběrem možnosti **obnovit** pro zvolenou položku obnovitelné Odstraněná záloha.

   ![Obnovení virtuálního počítače](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Po dokončení procesu zrušení odstranění se stav vrátí do části zastavit zálohování s daty zachovat a pak můžete zvolit **pokračovat v zálohování**. Operace **obnovit zálohování** vrátí zpět zálohovanou položku v aktivním stavu, která je přidružená k zásadám zálohování vybraným uživatelem, který definuje plány zálohování a uchovávání.

   ![Pokračovat v zálohování](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Obnovitelné odstranění pro SQL Server na virtuálním počítači pomocí Azure PowerShell

>[!NOTE]
>Verze AZ. RecoveryServices, která je nutná k použití obnovitelného odstranění pomocí Azure PowerShell, je minimální 2.2.0. Použijte `Install-Module -Name Az.RecoveryServices -Force` k získání nejnovější verze.

Pořadí kroků použití Azure PowerShell je stejné jako v Azure Portal popsané výše.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Odstraňte zálohovanou položku pomocí Azure PowerShell

Odstraňte zálohovanou položku pomocí rutiny [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) prostředí PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

**DeleteState** zálohované položky se změní z **NotDeleted** na **ToBeDeleted**. Data zálohy se uchovávají 14 dnů. Chcete-li obnovit operaci odstranění, je třeba provést příkaz zpět a odstranit.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Zrušení operace odstranění pomocí Azure PowerShell

Nejdřív načtěte příslušnou zálohovanou položku, která je ve stavu tichého odstranění (tj., chystá se odstranit).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Pak proveďte operaci vrácení zpět se změnami pomocí rutiny [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) prostředí PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

**DeleteState** zálohované položky se vrátí na **NotDeleted**. Ochrana se ale pořád zastaví. Obnovte zálohu pro opětovné povolení ochrany.

## <a name="how-to-disable-soft-delete"></a>Jak zakázat obnovitelné odstranění

Zakázání této funkce se nedoporučuje. Jediným případem, kdy byste měli zvážit zakázání obnovitelného odstranění, je, že plánujete přesunutí chráněných položek do nového trezoru a nemůžete počkat 14 dní před odstraněním a obnovením ochrany (například v testovacím prostředí). Pokyny, jak zakázat obnovitelné odstranění, najdete v tématu [povolení a zakázání obnovitelného odstranění](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Nejčastější dotazy](backup-azure-security-feature-cloud.md#frequently-asked-questions) týkající se obnovitelného odstranění
- Přečtěte si o všech [funkcích zabezpečení v Azure Backup](security-overview.md)
