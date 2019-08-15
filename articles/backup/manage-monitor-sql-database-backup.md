---
title: Správa a monitorování SQL Serverch databází na virtuálním počítači Azure pomocí Azure Backup
description: Tento článek popisuje, jak spravovat a monitorovat databáze SQL Server, které běží na virtuálním počítači Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dacurwin
ms.openlocfilehash: 1d50f239a0ef4de02c9f0c87a28b0f5092d9c529
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019040"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Správa a sledování zálohovaných SQL Server databází

Tento článek popisuje běžné úlohy pro správu a monitorování SQL Serverch databází, které běží na virtuálním počítači Azure a které se zálohují do Azure Backup Recovery Services trezoru služby [Azure Backup](backup-overview.md) . Naučíte se monitorovat úlohy a výstrahy, zastavovat a obnovovat ochranu databáze, spouštět úlohy zálohování a rušit registraci virtuálního počítače ze záloh.

Pokud jste ještě nenakonfigurovali zálohy pro databáze SQL Server, přečtěte si téma [zálohování databází SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md) .

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorování úloh ručního zálohování na portálu

Azure Backup zobrazí všechny ručně aktivované úlohy na portálu **úlohy zálohování** . Úlohy, které vidíte na tomto portálu, zahrnují zjišťování a registraci databází a operace zálohování a obnovení.

![Portál úlohy zálohování](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Portál **úlohy zálohování** nezobrazuje naplánované úlohy zálohování. Pomocí SQL Server Management Studio můžete monitorovat naplánované úlohy zálohování, jak je popsáno v následující části.
>

Podrobné informace o scénářích monitorování najdete [v části monitorování v Azure Portal](backup-azure-monitoring-built-in-monitor.md) a [monitorování pomocí Azure monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Vzhledem k tomu, že se zálohy protokolů vyskytují každých 15 minut, může být monitorování úloh zálohování zdlouhavé. Azure Backup usnadňuje monitorování odesláním e-mailových upozornění. E-mailová upozornění:

- Aktivované pro všechna selhání zálohování.
- Konsolidovat na úrovni databáze podle kódu chyby.
- Odesílá se jenom pro první selhání zálohování databáze.

Monitorování výstrah zálohování databáze:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Na řídicím panelu trezoru vyberte **výstrahy a události**.

   ![Vybrat výstrahy a události](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. V **upozorněních a událostech**vyberte **výstrahy zálohování**.

   ![Vybrat výstrahy zálohování](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zastavení ochrany pro databázi SQL Server

Můžete zastavit zálohování databáze SQL Server několika způsoby:

* Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změny.

Pokud se rozhodnete zachovat body obnovení, mějte na paměti tyto informace:

* Všechny body obnovení zůstanou nedotčeny trvale, všechna vyřazení se zastaví při zastavení ochrany s uchováním dat.
* Bude se vám účtovat za chráněnou instanci a spotřebované úložiště. Další informace najdete v tématu [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/).
* Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohování se nezdaří.

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
>* [Když odstraním databázi z autoprotected instance, co se stane se zálohami?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>* [Když zabráním operaci zálohování funkce autoprotected Database, která bude její chování?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
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

* Úplné zálohování
* Úplná záloha jenom pro kopírování
* Rozdílové zálohování
* Zálohování protokolů

I když potřebujete zadat dobu uchování pro úplné zálohování, rozsah uchování pro ad hoc úplné zálohování bude automaticky nastaven na 45 dní od aktuálního času. <br/>
Další informace najdete v tématu [SQL Server typy zálohování](backup-architecture.md#sql-server-backup-types).

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

V některých případech může mít rozšíření úlohy na virtuálním počítači vliv na jeden důvod nebo na druhý. V takových případech se začnou zdařit všechny operace aktivované na virtuálním počítači. Pak možná budete muset znovu zaregistrovat rozšíření na virtuálním počítači. Operace **opětovného registrace znovu** nainstaluje rozšíření zálohování úlohy na virtuálním počítači, aby operace pokračovaly.  <br>

Tuto možnost používejte opatrně. Pokud se aktivuje na virtuálním počítači s již nedobrým rozšířením, tato operace způsobí restartování rozšíření. To může vést k selhání všech probíhajících úloh. Před aktivací operace opětovného registrace zkontrolujte, zda [](backup-sql-server-azure-troubleshoot.md#re-registration-failures) některé z těchto symptomů existuje.

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [řešení potíží s zálohami v databázi SQL Server](backup-sql-server-azure-troubleshoot.md).
