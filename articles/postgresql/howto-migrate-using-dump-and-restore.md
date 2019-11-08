---
title: Postup při vystavení a obnovení na Azure Database for PostgreSQL – jeden server
description: Popisuje postup extrakce databáze PostgreSQL do souboru s výpisem paměti a obnovení ze souboru vytvořeného nástrojem pg_dump v Azure Database for PostgreSQLm jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4291db0bb1edbc366c42febed992a7c27d46eb15
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796753"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrace databáze PostgreSQL pomocí výpisu a obnovení
[Pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) můžete použít k extrakci databáze PostgreSQL do souboru s výpisem paměti a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) pro obnovení databáze PostgreSQL z archivního souboru vytvořeného pomocí pg_dump.

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md) s pravidly brány firewall pro povolení přístupu a databáze pod ní.
- nainstalované nástroje příkazového řádku [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)

Pomocí těchto kroků vypíšete a obnovíte databázi PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Vytvořte soubor s výpisem paměti pomocí pg_dump, který obsahuje data, která se mají načíst.
Pokud chcete zálohovat stávající databázi PostgreSQL místně nebo na virtuálním počítači, spusťte následující příkaz:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Například pokud máte místní server a databázi s názvem **TestDB** .
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Obnovení dat do cílového Azure Database for PostgreSQL pomocí pg_restore
Po vytvoření cílové databáze můžete použít příkaz pg_restore a parametr-d,--dbname k obnovení dat do cílové databáze ze souboru s výpisem paměti.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Zahrnutím parametru--No-Owner dojde k tomu, že všechny objekty vytvořené během obnovování budou vlastněny uživatelem zadaným parametrem--username. Další informace najdete v oficiální dokumentaci k PostgreSQL na [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Pokud váš server PostgreSQL vyžaduje připojení SSL (ve výchozím nastavení na Azure Database for PostgreSQL serverech), nastavte proměnnou prostředí `PGSSLMODE=require` tak, aby se nástroj pg_restore připojil k protokolu SSL. Bez protokolu SSL se může zobrazit chyba `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> V příkazovém řádku Windows spusťte příkaz `SET PGSSLMODE=require` před spuštěním příkazu pg_restore. V systému Linux nebo bash spusťte příkaz `export PGSSLMODE=require` před spuštěním příkazu pg_restore.
>

V tomto příkladu obnovte data ze souboru s výpisem paměti **TestDB. dump** do databáze **mypgsqldb** na cílovém serveru **mydemoserver.Postgres.Database.Azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimalizace procesu migrace

Jedním ze způsobů, jak migrovat stávající databázi PostgreSQL do služby Azure Database for PostgreSQL, je zálohovat databázi ve zdroji a obnovit ji v Azure. Pokud chcete zkrátit dobu potřebnou k dokončení migrace, zvažte použití následujících parametrů s příkazy Backup a Restore.

> [!NOTE]
> Podrobné informace o syntaxi najdete v článcích [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Pro zálohu
- Proveďte zálohu pomocí přepínače-FC, aby bylo možné provést obnovení paralelně, abyste ho urychlili. Příklad:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Pro obnovení
- Doporučujeme přesunout záložní soubor do virtuálního počítače Azure ve stejné oblasti jako server Azure Database for PostgreSQL, do kterého migrujete, a provést pg_restore z tohoto virtuálního počítače a snížit tak latenci sítě. Doporučujeme také vytvořit virtuální počítač s povolenými [akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md) službami.

- Mělo by být již provedeno ve výchozím nastavení, ale otevřete soubor s výpisem paměti, abyste ověřili, že příkazy CREATE index jsou po vložení dat. Pokud tomu tak není, přesuňte příkazy CREATE index po vložení dat.

- Obnovte pomocí přepínačů-FC a-j *#* paralelizovat obnovení. *#* je počet jader na cílovém serveru. Můžete také zkusit s *#* nastavenou na dvojnásobek počtu jader cílového serveru, abyste viděli dopad. Příklad:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Soubor s výpisem paměti můžete také upravit přidáním sady příkazů *synchronous_commit = off;* na začátku a *sady příkazů synchronous_commit = on;* na konci. Nezapne se na konci, předtím než aplikace změní data, může dojít k následné ztrátě dat.

- Na cílovém serveru Azure Database for PostgreSQL před obnovením zvažte následující:
    - Vypněte sledování výkonu dotazů, protože tyto statistiky nejsou během migrace potřeba. To můžete provést nastavením pg_stat_statements. Track, pg_qs. query_capture_mode a pgms_wait_sampling. query_capture_mode na NONE.

    - K urychlení migrace použijte vysoce výpočetní a vysoce paměťovou SKU, například 32 vCore paměť optimalizované pro optimalizaci. Až se obnovení dokončí, můžete se snadno škálovat zpátky na preferovanou skladovou jednotku. Čím vyšší je SKU, tím více paralelismu můžete dosáhnout zvýšením odpovídajícího parametru `-j` v příkazu pg_restore. 

    - Další IOPS na cílovém serveru můžou zlepšit výkon při obnovení. Další IOPS můžete zřídit zvýšením velikosti úložiště serveru. Toto nastavení se nedá vrátit, ale zvažte, jestli by vyšší IOPS během budoucna využila výhody vaší skutečné zátěže.

Nezapomeňte otestovat a ověřit tyto příkazy v testovacím prostředí předtím, než je použijete v produkčním prostředí.

## <a name="next-steps"></a>Další kroky
- Postup migrace databáze PostgreSQL pomocí exportu a importu najdete v tématu [migrace databáze PostgreSQL pomocí exportu a importu](howto-migrate-using-export-and-import.md).
- Další informace o migraci databází do Azure Database for PostgreSQL najdete v [Průvodci migrací databáze](https://aka.ms/datamigration).
