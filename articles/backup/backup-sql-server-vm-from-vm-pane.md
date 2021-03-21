---
title: Zálohování virtuálního počítače s SQL Server v podokně virtuálního počítače
description: V tomto článku se dozvíte, jak zálohovat SQL Server databáze na virtuálních počítačích Azure z podokna virtuálního počítače.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891653"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Zálohování SQL Server v podokně virtuálního počítače

Tento článek vysvětluje, jak pomocí služby [Azure Backup](backup-overview.md) zálohovat SQL Server běžící na virtuálních počítačích Azure. SQL Server virtuálních počítačů můžete zálohovat pomocí dvou metod:

- Jeden SQL Server virtuální počítač Azure: pokyny v tomto článku popisují, jak zálohovat SQL Server virtuální počítač přímo ze zobrazení virtuálního počítače.
- Více SQL Server virtuálních počítačů Azure: můžete nastavit trezor Recovery Services a nakonfigurovat zálohování pro více virtuálních počítačů. Postupujte podle pokynů v [tomto článku v tomto](backup-sql-server-database-azure-vms.md) scénáři.

## <a name="before-you-start"></a>Než začnete

1. Ověřte si prostředí s [maticí podpory](sql-support-matrix.md).
2. Získejte [přehled](backup-azure-sql-database.md) Azure Backup pro SQL Server virtuální počítač.
3. Ověřte, že virtuální počítač má [připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="configure-backup-on-the-sql-server"></a>Konfigurace zálohování na SQL Server

Můžete povolit zálohování na svém SQL Serverm VIRTUÁLNÍm počítači z podokna **zálohování** na virtuálním počítači. Tato metoda provádí dvě věci:

- Zaregistruje virtuální počítač SQL se službou Azure Backup, abyste mu poskytli přístup.
- Autoprotectuje všechny instance SQL Server běžící v rámci virtuálního počítače. To znamená, že zásady zálohování se použijí na všechny existující databáze a také na databáze, které budou v budoucnu přidány do těchto instancí.

1. Vyberte banner v horní části stránky a otevřete tak SQL Server zobrazení zálohy.

    ![Zobrazení zálohování SQL Server](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Nezobrazuje se banner? Banner se zobrazí jenom pro ty SQL Server virtuálních počítačů, které se vytvářejí pomocí Azure Marketplace imagí. Zobrazuje se taky pro virtuální počítače, které jsou chráněné pomocí zálohování virtuálních počítačů Azure. U ostatních imagí můžete nakonfigurovat zálohování tak, jak je popsáno [zde](backup-sql-server-database-azure-vms.md).

2. Zadejte název trezoru Recovery Services. Trezor je logická entita pro ukládání a správu všech záloh. Pokud vytváříte nový trezor:

    - Vytvoří se ve stejném předplatném a oblasti jako virtuální počítač SQL Server, který chráníte.
    - Vytvoří se s nastavením geograficky redundantního úložiště (GRS) pro všechny zálohy. Pokud chcete změnit typ redundance, měli byste tak učinit před ochranou virtuálního počítače. Další informace najdete v [tomto článku](backup-create-rs-vault.md#set-storage-redundancy).

3. Vyberte **zásady zálohování**. Můžete si vybrat z výchozích zásad nebo jakékoli jiné existující zásady, které jste v trezoru vytvořili. Pokud chcete vytvořit novou zásadu, můžete si v [tomto článku](backup-sql-server-database-azure-vms.md#create-a-backup-policy) projít podrobný průvodce.

    ![Vybrat zásady zálohování](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Vyberte **Povolit zálohování**. Dokončení operace může trvat několik minut.

    ![Vyberte povolit zálohování.](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. Po dokončení operace se v proužku zobrazí **název trezoru** .

    ![Název trezoru se zobrazí v banneru.](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Vyberte hlavičku, do které se má přejít v zobrazení trezoru, kde vidíte všechny registrované virtuální počítače a jejich stav ochrany.

    ![Zobrazení trezoru s registrovanými virtuálními počítači](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. U imagí mimo Marketplace může být registrace úspěšná, ale **Konfigurace zálohování** nemusí být spuštěná, dokud Azure Backup rozšíření neudělí oprávnění SQL Server. V takových případech čtení sloupce **připravenosti na zálohování** **není připravené**. Je potřeba [přiřadit příslušná oprávnění](backup-azure-sql-database.md#set-vm-permissions) ručně pro Image mimo Marketplace, aby se konfigurace zálohování mohla aktivovat.

    ![Připravenost na zálohování není připravená](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Pro další operace nebo monitorování, které musíte provést na zálohovaných SQL Server virtuálních počítačích, přejdete do odpovídajícího trezoru Recovery Services. Přejděte na **zálohované položky** a zobrazte všechny databáze zálohované v tomto trezoru a aktivujte operace, jako je zálohování a obnovení na vyžádání. Podobně můžete přejít na **úlohy zálohování** a [monitorovat](manage-monitor-sql-database-backup.md) úlohy, které odpovídají operacím, jako je konfigurace ochrany, zálohování a obnovení.

    ![Viz zálohované databáze v zálohovaných položkách.](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>Zálohování není automaticky nakonfigurováno na žádné nové instance SQL Server, které mohou být přidány později do chráněného virtuálního počítače. Pokud chcete nakonfigurovat zálohování u nově přidaných instancí, musíte přejít do trezoru, ve kterém je virtuální počítač zaregistrovaný, a postupovat podle kroků uvedených [tady](backup-sql-server-database-azure-vms.md).

## <a name="next-steps"></a>Další kroky

Naučte se:

- [Obnovení zálohovaných SQL Server databází](restore-sql-database-azure-vm.md)
- [Správa zálohovaných SQL Server databází](manage-monitor-sql-database-backup.md)
