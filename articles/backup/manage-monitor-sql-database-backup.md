---
title: Správa a monitorování databází systému SQL Server na Virtuálním počítači Azure zálohovat službou Azure Backup | Dokumentace Microsoftu
description: Tento článek popisuje, jak obnovit databáze systému SQL Server běžící na Virtuálním počítači Azure, které se zálohují v Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430798"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Správa a monitorování zálohovat databáze SQL serveru 


Tento článek popisuje běžné úlohy pro správu a monitorování databáze systému SQL Server běžící na Virtuálním počítači Azure, které budou zálohovány do služby Azure Backup Recovery Services vault podle [Azure Backup](backup-overview.md) služby. Úlohy, včetně monitorování, úlohy a výstrahy, zastavení a obnovení ochrany databáze, spuštění úloh zálohování a registrace virtuálních počítačů ze zálohy.


> [!NOTE]
> Zálohování databází systému SQl Server běžící na Virtuálním počítači Azure pomocí služby Azure Backup je momentálně ve verzi public preview.


TIf nebyly nakonfigurované zálohování databází systému SQL Server, postupujte podle pokynů, ale [v tomto článku](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs"></a>Monitorování úlohy zálohování

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>Monitorování ad hoc úloh na portálu

Zobrazuje všechny ručně aktivované úlohy v Azure Backup **úlohy zálohování** portálu, včetně zjišťování a registrací databáze a operace zálohování a obnovení.

![Úlohy zálohování portálu](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Naplánované úlohy zálohování se nezobrazují v **úlohy zálohování** portálu. Pomocí SQL Server Management Studio ke sledování naplánovaných úlohách zálohování, jak je popsáno v další části.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Monitorování úlohy zálohování pomocí aplikace SQL Server Management Studio 

Azure Backup používá nativní rozhraní API SQL pro všechny operace zálohování.

Pomocí nativních rozhraní API k načtení všech informací o úlohu z [tabulku záloh SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) v databázi msdb.

V následujícím příkladu je dotaz, který načte všechny úlohy zálohování pro databázi s názvem **DB1**. Upravte dotaz pro rozšířené monitorování.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Protože protokol zálohování každých 15 minut, může být zdlouhavé monitorování úlohy zálohování. Azure Backup usnadňuje monitorování pomocí e-mailové výstrahy.

- Upozornění se aktivuje všechny případné selhání zálohování.
- Upozornění konsolidované podle kódu chyby na úrovni databáze.
- Jenom pro první selhání zálohování pro databázi přijde e-mailové upozornění. 

Chcete-li monitorovat výstrahy zálohování:

1. Přihlaste se ke svému předplatnému Azure v [webu Azure portal](https://portal.azure.com) monitorování výstrahy databáze.

2. Na řídicím panelu trezoru vyberte **výstrahy a události**.

   ![Vyberte výstrahy a události](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. V **výstrahy a události**vyberte **výstrahy zálohování**.

   ![Vyberte výstrahy zálohování](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zastavte ochranu pro databázi serveru SQL Server

Můžete zastavit zálohování databáze serveru SQL Server v několika způsoby:

* Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení.
* Zastavit všechny budoucí úlohy zálohování, ale ponechat body obnovení beze změn.

Poznámky:

Pokud necháte body obnovení, body vyčistit v souladu se zásadami zálohování. Vám účtovat poplatky za chráněné instance a spotřebovaného úložiště, dokud se vyčistí všechny body obnovení. [Další informace](https://azure.microsoft.com/pricing/details/backup/) informace o cenách.
- Po opuštění body obnovení beze změn, i když jejich podle zásad uchovávání informací vyprší, Azure Backup vždy udržuje jeden poslední bod obnovení dokud explicitně odstranit zálohovaná data.
- Při odstranění zdroje dat bez zastavení zálohování, začne nových záloh služeb při selhání. Znovu vyprší platnost starých bodů obnovení podle zásady, ale jeden poslední bod obnovení vždy zachová až do zastavení zálohování a odstraňte data.
- Nelze zastavit zálohování pro databázi povolená pro automatickou ochranu, dokud je zakázáno automatické ochrany.

Ukončit ochranu pro databázi:

1. Na řídicím panelu trezoru v části **využití**vyberte **zálohování položek**.

    ![Otevřete nabídku zálohování položek](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. V **typu správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Vyberte SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Vyberte databázi, pro kterou chcete ukončit ochranu.

    ![Vyberte databáze, kterou chcete zastavit ochranu](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. V nabídce databáze vyberte **Zastavit zálohování**.

    ![Vyberte Zastavit zálohování](./media/backup-azure-sql-database/stop-db-button.png)


6. V **Zastavit zálohování** nabídce vyberte, jestli chcete zachovat nebo odstraňovat data. Volitelně zadejte důvod a komentář.

    ![Zastavit zálohování nabídky](./media/backup-azure-sql-database/stop-backup-button.png)

7. Klikněte na tlačítko **Zastavit zálohování** .

  

### <a name="resume-protection-for-a-sql-database"></a>Pokračovat v ochraně databáze SQL

Pokud **zachovat zálohovaná Data** byla vybrána možnost, pokud ochranu databáze SQL se zastavila, můžete obnovit ochranu. Pokud se data záloh se uchovávají, nelze obnovit ochranu.

1. Chcete-li obnovit ochranu pro službu SQL database, otevřete zálohovaná položka a vyberte **obnovit zálohu**.

    ![Vyberte obnovit zálohu k obnovení ochrany databáze](./media/backup-azure-sql-database/resume-backup-button.png)

2. Na **zásady zálohování** nabídku, vyberte zásadu a pak vyberte **Uložit**.

## <a name="run-an-on-demand-backup"></a>Spustit zálohu na vyžádání

Můžete spouštět různé typy záloh na vyžádání:

* Úplné zálohování
* Úplné zálohy
* Rozdílové zálohování
* Zálohování protokolů

[Další informace](backup-architecture.md#sql-server-backup-types) o SQL serveru zálohování typy.

## <a name="unregister-a-sql-server-instance"></a>Zrušit registraci instance serveru SQL Server

Zrušit registraci instance systému SQL Server po deaktivovali ochranu, ale před odstranění trezoru:

1. Na řídicím panelu trezoru v části **spravovat**vyberte **infrastruktura zálohování**.  

   ![Vyberte infrastruktury zálohování](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. V části **servery pro správu**vyberte **chráněné servery**.

   ![Vyberte chráněné servery](./media/backup-azure-sql-database/protected-servers.png)


3. V **chráněné servery**, vyberte server ke zrušení registrace. Odstranit trezor, musíte zrušit registraci všech serverů.

4. Klikněte pravým tlačítkem na chráněném serveru > **odstranit**.

   ![Vyberte odstranit](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Další postup

[Kontrola](backup-sql-server-azure-troubleshoot.md) řešení potíží s informace o zálohování databáze serveru SQL Server.
