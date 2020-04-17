---
title: Správa a monitorování db s SQL Server na virtuálním počítači Azure
description: Tento článek popisuje, jak spravovat a monitorovat databáze SQL Serveru, které běží na virtuálním počítači Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 14e3a4797fe60a3d1857f1e6d947fa0c669bdcfe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537300"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Správa a monitorování zálohovaných databází SQL Serveru

Tento článek popisuje běžné úlohy pro správu a monitorování databází SQL Serveru, které běží na virtuálním počítači Azure (VM) a které jsou zálohovány do trezoru služby Azure Backup Recovery Services službou [Azure Backup.](backup-overview.md) Dozvíte se, jak sledovat úlohy a výstrahy, zastavit a obnovit ochranu databáze, spustit úlohy zálohování a zrušit registraci virtuálního počítače ze záloh.

Pokud jste ještě nenakonfigurovali zálohy pro databáze SQL Serveru, přečtěte si informace [o zálohování databází SERVERU SQL Server na virtuálních počítačích Azure.](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Sledování úloh ručního zálohování na portálu

Azure Backup zobrazuje všechny ručně aktivované úlohy na portálu **úloh zálohování.** Úlohy, které se zobrazí na tomto portálu, zahrnují zjišťování a registraci databáze a operace zálohování a obnovení.

![Portál úloh zálohování](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Na portálu **úloh zálohování** se nezobrazují naplánované úlohy zálohování. Pomocí aplikace SQL Server Management Studio můžete sledovat naplánované úlohy zálohování, jak je popsáno v další části.
>

Podrobnosti o scénářích monitorování najdete [v části Monitorování na webu Azure Portal](backup-azure-monitoring-built-in-monitor.md) a Monitorování pomocí Azure [Monitoru](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Vzhledem k tomu, že zálohy protokolu dochází každých 15 minut, monitorování úloh zálohování může být únavné. Azure Backup usnadňuje monitorování odesíláním e-mailových upozornění. E-mailová upozornění jsou:

- Spuštěno pro všechny selhání zálohování.
- Konsolidováno na úrovni databáze kódem chyby.
- Odesláno pouze pro první selhání zálohování v databázi.

Sledování výstrah zálohování databáze:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Na řídicím panelu trezoru vyberte **Výstrahy a události**.

   ![Vybrat výstrahy a události](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. V **seznamu Výstrahy a události**vyberte možnost Zálohování **výstrah**.

   ![Vybrat výstrahy zálohování](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Ukončení ochrany databáze serveru SQL Server

Zálohování databáze serveru SQL Server můžete ukončit několika způsoby:

- Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
- Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení nedotčené.

Pokud se rozhodnete ponechat body obnovení, mějte na paměti tyto podrobnosti:

- Všechny body obnovení zůstanou neporušené navždy, veškeré prořezávání se zastaví při zastavení ochrany s uchováním dat.
- Bude se vám účtovat chráněná instance a spotřebované úložiště. Další informace najdete v tématu [Azure Backup pricing](https://azure.microsoft.com/pricing/details/backup/).
- Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohy se nezdaří. Platnost starých bodů obnovení vyprší podle zásad, ale jeden poslední bod obnovení bude vždy zachován, dokud nezastavíte zálohy a neodstraníte data.

Ukončení ochrany databáze:

1. Na řídicím panelu úschovny vyberte **Zálohovat položky**.

2. V části **Typ správy zálohování**vyberte SQL v **virtuálním počítači Azure**.

    ![Výběr SQL ve virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Vyberte databázi, pro kterou chcete ochranu zastavit.

    ![Vyberte databázi, kterou chcete zastavit.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. V nabídce databáze vyberte **Zastavit zálohování**.

    ![Vybrat možnost Zastavit zálohování.](./media/backup-azure-sql-database/stop-db-button.png)

5. V nabídce **Zastavit zálohování** vyberte, zda chcete data uchovávat nebo odstraňovat. Pokud chcete, uveďte důvod a komentář.

    ![Zachování nebo odstranění dat v nabídce Zastavit zálohování](./media/backup-azure-sql-database/stop-backup-button.png)

6. Vyberte **možnost Zastavit zálohování**.

> [!NOTE]
>
>Další informace o možnosti odstranění dat naleznete v nejčastějších dotazech níže:
>
>- [Pokud odstraním databázi z automaticky chráněné instance, co se stane se zálohami?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Pokud zastavím operaci zálohování automaticky chráněné databáze, jaké bude její chování?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Obnovení ochrany databáze SQL

Když zastavíte ochranu databáze SQL, pokud vyberete možnost **Zachovat záložní data,** můžete později obnovit ochranu. Pokud záložní data neuchováte, nemůžete ochranu obnovit.

Obnovení ochrany databáze SQL:

1. Otevřete položku zálohování a vyberte **Pokračovat v zálohování**.

    ![Chcete-li obnovit ochranu databáze, vyberte možnost Pokračovat v zálohování.](./media/backup-azure-sql-database/resume-backup-button.png)

2. V nabídce **Zásady zálohování** vyberte zásadu a pak vyberte **Uložit**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

Můžete spustit různé typy záloh na vyžádání:

- Úplné zálohování
- Úplné zálohování pouze pro kopírování
- Rozdílové zálohování
- Zálohování protokolů

I když je třeba zadat dobu uchovávání pro úplné zálohování pouze pro kopírování, rozsah uchování pro úplné zálohování na vyžádání bude automaticky nastaven na 45 dní od aktuálního času.

Další informace naleznete v tématu [SQL Server typy zálohování](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Zrušení registrace instance serveru SQL Server

Po zakázání ochrany zrušte registraci instance serveru SQL Server, ale před odstraněním trezoru:

1. Na řídicím panelu trezoru vyberte v části **Správa** **položku Zálohovací infrastruktura**.  

   ![Vybrat infrastrukturu zálohování](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. V části **Servery pro správu**vyberte **chráněné servery**.

   ![Vybrat chráněné servery](./media/backup-azure-sql-database/protected-servers.png)

3. V **části Chráněné servery**vyberte server, který chcete zrušit registraci. Chcete-li trezor odstranit, musíte zrušit registraci všech serverů.

4. Klepněte pravým tlačítkem myši na chráněný server a vyberte možnost **Zrušit registraci**.

   ![Vybrat Odstranit](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Změnit zásadu

Upravte zásadu a změňte frekvenci zálohování nebo rozsah uchování.

> [!NOTE]
> Každá změna retenčního období bude použita zpětně na všechny starší body výtěžnosti kromě nových bodů.

Na řídicím panelu trezoru přejděte na **Spravovat** > **zásady zálohování** a zvolte zásady, které chcete upravit.

  ![Správa zásad zálohování](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Upravit zásady zálohování](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Změna zásad ovlivní všechny přidružené položky zálohování a aktivuje odpovídající **úlohy ochrany konfigurace.**

### <a name="inconsistent-policy"></a>Nekonzistentní zásady

V některých případě může operace upravit zásady vést k **nekonzistentní** verzi zásad pro některé položky zálohování. K tomu dochází, když se po aktivaci operace zásad změny nezdaří odpovídající úloha **ochrany konfigurace** pro položku zálohování. V zobrazení položky zálohování se zobrazí následujícím způsobem:

  ![Nekonzistentní zásady](./media/backup-azure-sql-database/inconsistent-policy.png)

Verzi zásad můžete opravit pro všechny ovlivněné položky jedním kliknutím:

  ![Oprava nekonzistentních zásad](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Znovu zaregistrovat rozšíření na virtuálním počítači SQL Server

V některých proto rozšíření zatížení na virtuálním počítači může získat vliv z jednoho důvodu nebo jiný. V takových případech všechny operace aktivované na virtuálním počítači začne selhat. Potom může být nutné znovu zaregistrovat rozšíření na virtuálním počítači. **Operace opětovnéregistrace** přeinstaluje rozšíření zálohování úloh y na virtuálním počítači, aby operace mohly pokračovat.

Tuto možnost používejte opatrně. při aktivaci na virtuálním počítači s již v pořádku rozšíření, tato operace způsobí rozšíření získat restartování. To může mít za následek selhání všech probíhajících úloh. Před spuštěním operace opětovné registrace zkontrolujte jeden nebo více [příznaků.](backup-sql-server-azure-troubleshoot.md#re-registration-failures)

## <a name="next-steps"></a>Další kroky

Další informace naleznete [v tématu Poradce při potížích se zálohami v databázi serveru SQL Server](backup-sql-server-azure-troubleshoot.md).
