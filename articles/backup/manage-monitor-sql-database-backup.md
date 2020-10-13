---
title: Správa a sledování SQL Server databáze na virtuálním počítači Azure
description: Tento článek popisuje, jak spravovat a monitorovat databáze SQL Server, které běží na virtuálním počítači Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: b0df22002521c8148cac1200e79aeb0ae5a60546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146517"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Správa a monitorování zálohovaných databází SQL Serveru

Tento článek popisuje běžné úlohy pro správu a monitorování SQL Serverch databází, které běží na virtuálním počítači Azure a které se zálohují do Azure Backup Recovery Services trezoru služby [Azure Backup](backup-overview.md) . Naučíte se monitorovat úlohy a výstrahy, zastavovat a obnovovat ochranu databáze, spouštět úlohy zálohování a rušit registraci virtuálního počítače ze záloh.

Pokud jste ještě nenakonfigurovali zálohy pro databáze SQL Server, přečtěte si téma [zálohování databází SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md) .

## <a name="monitor-backup-jobs-in-the-portal"></a>Monitorování úloh zálohování na portálu

Azure Backup zobrazuje všechny plánované a vyžádané operace v rámci **úlohy zálohování** na portálu s výjimkou naplánovaných záloh protokolů, protože můžou být velmi časté. Úlohy, které vidíte na tomto portálu, zahrnují zjišťování a registraci databází, konfiguraci zálohování a operace zálohování a obnovení.

![Portál úlohy zálohování](./media/backup-azure-sql-database/jobs-list.png)

Podrobné informace o scénářích monitorování najdete [v části monitorování v Azure Portal](backup-azure-monitoring-built-in-monitor.md) a [monitorování pomocí Azure monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Vzhledem k tomu, že se zálohy protokolů vyskytují každých 15 minut, může být monitorování úloh zálohování zdlouhavé. Azure Backup usnadňuje monitorování odesláním e-mailových upozornění. E-mailová upozornění:

- Aktivované pro všechna selhání zálohování.
- Konsolidovat na úrovni databáze podle kódu chyby.
- Odesílá se jenom pro první selhání zálohování databáze.

Monitorování výstrah zálohování databáze:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

2. Na řídicím panelu trezoru vyberte **výstrahy a události**.

   ![Vybrat výstrahy a události](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. V **upozorněních a událostech**vyberte **výstrahy zálohování**.

   ![Vybrat výstrahy zálohování](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zastavení ochrany pro databázi SQL Server

Můžete zastavit zálohování databáze SQL Server několika způsoby:

- Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
- Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změny.

Pokud se rozhodnete zachovat body obnovení, mějte na paměti tyto informace:

- Všechny body obnovení zůstanou nedotčené trvale a všechny vyřazení se zastaví při zastavení ochrany s uchováním dat.
- Bude se vám účtovat chráněná instance a spotřebované úložiště. Další informace najdete v tématu [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/).
- Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohování se nezdaří. V souladu se zásadami vyprší platnost starých bodů obnovení, ale nejnovější bod obnovení bude vždycky uložený, dokud nezastavíte zálohování a data odstraníte.

Zastavení ochrany databáze:

1. Na řídicím panelu trezoru vyberte **zálohované položky**.

2. V části **typ správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Výběr SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Vyberte databázi, pro kterou chcete zastavit ochranu.

    ![Vyberte databázi pro zastavení ochrany](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. V nabídce databáze vyberte **Zastavit zálohování**.

    ![Vyberte Zastavit zálohování.](./media/backup-azure-sql-database/stop-db-button.png)

5. V nabídce **Zastavit zálohování** vyberte, jestli se mají data zachovat nebo odstranit. Pokud chcete, zadejte důvod a komentář.

    ![Uchování nebo odstranění dat v nabídce zastavit zálohování](./media/backup-azure-sql-database/stop-backup-button.png)

6. Vyberte **Zastavit zálohování**.

> [!NOTE]
>
>Další informace o možnosti odstranit data najdete v následujících nejčastějších dotazech:
>
>- [Když odstraním databázi z autoprotected instance, co se stane se zálohami?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Když zabráním operaci zálohování funkce autoprotected Database, která bude její chování?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Obnovení ochrany databáze SQL

Pokud zastavíte ochranu pro databázi SQL, pokud vyberete možnost **uchovat data záloh** , můžete později obnovit ochranu. Pokud data zálohy nezachováte, nemůžete obnovit ochranu.

Obnovení ochrany databáze SQL:

1. Otevřete zálohovanou položku a vyberte **pokračovat v zálohování**.

    ![Pokud chcete pokračovat v ochraně databáze, vyberte pokračovat v zálohování.](./media/backup-azure-sql-database/resume-backup-button.png)

2. V nabídce **zásady zálohování** vyberte zásadu a pak vyberte **Uložit**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Můžete spouštět různé typy zálohování na vyžádání:

- Úplné zálohování
- Úplná záloha jenom pro kopírování
- Rozdílové zálohování
- Zálohování protokolů

I když potřebujete zadat dobu uchování pro úplné zálohování, rozsah uchování pro úplné zálohování na vyžádání bude automaticky nastaven na 45 dní od aktuálního času.

Další informace najdete v tématu [SQL Server typy zálohování](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Upravit zásadu

Upravte zásadu pro změnu četnosti zálohování nebo rozsahu uchovávání.

> [!NOTE]
> Jakákoli změna v období uchování se použije zpět na všechny starší body obnovení kromě nových.

Na řídicím panelu trezoru klikněte na **Spravovat**  >  **zásady zálohování** a vyberte zásadu, kterou chcete upravit.

  ![Spravovat zásady zálohování](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Upravit zásady zálohování](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Změna zásad ovlivní všechny přidružené zálohované položky a spustí odpovídající úlohy **Konfigurace ochrany** .

### <a name="inconsistent-policy"></a>Nekonzistentní zásady

V některých případech může operace upravit zásady vést k **nekonzistentní** verzi zásad pro některé zálohované položky. K tomu dojde v případě, že se po aktivaci operace Upravit zásadu pro zálohovanou položku nezdařila odpovídající úloha **Konfigurace ochrany** . V zobrazení zálohovaná položka se zobrazí takto:

  ![Nekonzistentní zásady](./media/backup-azure-sql-database/inconsistent-policy.png)

Verzi zásad můžete u všech ovlivněných položek opravit jedním kliknutím:

  ![Opravit nekonzistentní zásady](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Zrušení registrace instance SQL Server

Zrušení registrace instance SQL Server po zakázání ochrany, ale před odstraněním trezoru:

1. Na řídicím panelu trezoru v části **Spravovat**vyberte **infrastruktura zálohování**.  

   ![Výběr infrastruktury zálohování](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. V části **servery pro správu**vyberte **chráněné servery**.

   ![Vybrat chráněné servery](./media/backup-azure-sql-database/protected-servers.png)

3. V části **chráněné servery**vyberte server, jehož registraci chcete zrušit. Pokud chcete trezor odstranit, musíte zrušit registraci všech serverů.

4. Klikněte pravým tlačítkem myši na chráněný Server a vyberte zrušit **registraci**.

   ![Vybrat odstranit](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Opětovné zaregistrování rozšíření na SQL Serverm virtuálním počítači

Někdy se může stát, že rozšíření úlohy na virtuálním počítači bude mít vliv na jeden nebo jiný důvod. V takových případech se začnou zdařit všechny operace aktivované na virtuálním počítači. Pak možná budete muset znovu zaregistrovat rozšíření na virtuálním počítači. Operace **opětovného zápisu znovu** nainstaluje rozšíření zálohování úlohy na virtuálním počítači, aby operace pokračovaly. Tuto možnost najdete v části **infrastruktura zálohování** v trezoru Recovery Services.

![Chráněné servery v rámci infrastruktury zálohování](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Tuto možnost používejte opatrně. Pokud se aktivuje na virtuálním počítači s již nedobrým rozšířením, tato operace způsobí restartování rozšíření. To může způsobit selhání všech probíhajících úloh. Před aktivací operace opětovného registrace se podívejte na jeden nebo více [příznaků](backup-sql-server-azure-troubleshoot.md#re-registration-failures) .

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [řešení potíží s zálohami v databázi SQL Server](backup-sql-server-azure-troubleshoot.md).
