---
title: Správa a monitorování databází systému SQL Server na Virtuálním počítači Azure, který je zálohovaný službou Azure Backup | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat a monitorovat databáze systému SQL Server, na kterých běží na Virtuálním počítači Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: f5ec5a64f1de39cd0d196242fb1a93669dbab15d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681847"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Správa a monitorování zálohovat databáze SQL serveru

Tento článek popisuje běžné úlohy pro správu a monitorování databází systému SQL Server, který běží na virtuálním počítači Azure (VM) a, která budou zálohovány do služby Azure Backup Recovery Services vault podle [Azure Backup](backup-overview.md) služby. Budete se dozvíte, jak monitorovat úlohy a výstrahy, zastavení a obnovení ochrany databáze, spuštění úloh zálohování a zrušte registraci virtuálního počítače ze zálohy.

Pokud jste ještě nenakonfigurovali zálohování databází systému SQL Server, přečtěte si téma [zálohování databází systému SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Sledování ručního zálohování úloh na portálu

Zobrazuje všechny ručně aktivované úlohy v Azure Backup **úlohy zálohování** portálu. Úlohy, naleznete v tomto zjišťování databází portálu zahrnout a registrace a zálohování a obnovení operací.

![Na portálu úlohy zálohování](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **Úlohy zálohování** portálu nezobrazí naplánovaných úloh zálohování. Pomocí SQL Server Management Studio ke sledování naplánovaných úlohách zálohování, jak je popsáno v další části.
>

Podrobnosti o scénářích monitorování, přejděte na [monitorování na webu Azure Portal](backup-azure-monitoring-built-in-monitor.md) a [monitorováním s využitím Azure monitoru](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Protože protokol zálohování každých 15 minut, může být zdlouhavé monitorování úlohy zálohování. Azure Backup usnadňuje monitorování zasláním e-mailové výstrahy. E-mailová upozornění jsou:

- Aktivuje všechny případné selhání zálohování.
- Konsolidované na úrovni databáze podle kódu chyby.
- Odesílat jenom pro první selhání zálohování databáze.

Chcete-li monitorovat výstrahy zálohování databáze:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Na řídicím panelu trezoru vyberte **výstrahy a události**.

   ![Vyberte výstrahy a události](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. V **výstrahy a události**vyberte **výstrahy zálohování**.

   ![Vyberte výstrahy zálohování](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zastavte ochranu pro databázi serveru SQL Server

Můžete zastavit zálohování databáze serveru SQL Server v několika způsoby:

* Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení.
* Zastavit všechny budoucí úlohy zálohování a bodů obnovení nechte beze změny.

Pokud se rozhodnete ponechat body obnovení, musíte mít na paměti tyto podrobnosti:

* Všechny body obnovení zůstane zachován beze změny navždy, všechny vyřazení se zastaví na Zastavit ochranu při zachování dat.
* Účtuje se za chráněné instance a spotřebované úložiště. Další informace najdete v tématu [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Při odstranění zdroje dat bez zastavení zálohování, se nezdaří nových záloh.

Ukončit ochranu pro databázi:

1. Na řídicím panelu trezoru vyberte **zálohování položek**.

2. V části **typu správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Vyberte SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Vyberte databázi, pro kterou chcete ukončit ochranu.

    ![Vyberte databáze, kterou chcete zastavit ochranu](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. V nabídce databáze vyberte **Zastavit zálohování**.

    ![Vyberte Zastavit zálohování](./media/backup-azure-sql-database/stop-db-button.png)


5. Na **Zastavit zálohování** nabídce vyberte, jestli chcete zachovat nebo odstraňovat data. Pokud chcete, zadejte důvod a komentář.

    ![Uchovat nebo odstranit data v nabídce Zastavit zálohování](./media/backup-azure-sql-database/stop-backup-button.png)

6. Vyberte **Zastavit zálohování**.


## <a name="resume-protection-for-a-sql-database"></a>Pokračovat v ochraně databáze SQL

Při zastavení ochrany pro SQL database, pokud jste vybrali **zachovat zálohovaná Data** možnost, můžete později obnovit ochranu. Pokud nechcete zachovat zálohovaná data, nelze obnovit ochranu.

Pokud chcete obnovit ochranu pro službu SQL database:

1. Otevřete zálohovaná položka a vyberte **obnovit zálohu**.

    ![Vyberte obnovit zálohu k obnovení ochrany databáze](./media/backup-azure-sql-database/resume-backup-button.png)

2. Na **zásady zálohování** nabídku, vyberte zásadu a pak vyberte **Uložit**.

## <a name="run-an-on-demand-backup"></a>Spustit zálohu na vyžádání

Můžete spouštět různé typy záloh na vyžádání:

* Úplné zálohování
* Úplné zálohy
* Rozdílové zálohování
* Zálohování protokolů

Zatímco budete muset zadat dobu uchování Copy-only úplné zálohování, rozsah uchování pro ostatní typy zálohování automaticky nastaví na 30 dní od aktuálního času. <br/>
Další informace najdete v tématu [typy zálohování systému SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Zrušit registraci instance serveru SQL Server

Po zakázání ochrany, ale před odstraněním trezoru, zrušit registraci instance systému SQL Server:

1. Na řídicím panelu trezoru v části **spravovat**vyberte **infrastruktura zálohování**.  

   ![Vyberte infrastruktury zálohování](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. V části **servery pro správu**vyberte **chráněné servery**.

   ![Vyberte chráněné servery](./media/backup-azure-sql-database/protected-servers.png)

3. V **chráněné servery**, vyberte server ke zrušení registrace. Odstranit trezor, musíte zrušit registraci všech serverů.

4. Klikněte pravým tlačítkem na chráněném serveru a vyberte **Unregister**.

   ![Vyberte odstranit](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Znovu zaregistrovat příponu na virtuálním počítači SQL serveru

V některých případech může mít vliv rozšíření úlohy na virtuálním počítači pro jedním z důvodů, nebo druhé. V takovém případě budou všechny operace na virtuálním počítači aktivovat začnou být neúspěšné. Pak budete muset znovu zaregistrovat příponu na virtuálním počítači. **Znovu zaregistrovat** operace přeinstaluje rozšíření úlohy zálohování na virtuálním počítači pro operace, abyste mohli pokračovat.  <br>

Doporučujeme tuto možnost používejte s opatrností; Při aktivaci na virtuálním počítači s příponou už v pořádku, tato operace způsobí rozšíření k získání restartování. Výsledkem může být všechny probíhající úlohy nezdaří. Zkontrolujte pro jeden nebo více [příznaky](backup-sql-server-azure-troubleshoot.md#symptoms) před aktivací operace znovu zaregistrovat.

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [řešení zálohování v databázi serveru SQL Server](backup-sql-server-azure-troubleshoot.md).
