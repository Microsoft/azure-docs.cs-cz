---
title: Jak výpisu a obnovení ve službě Azure Database for PostgreSQL
description: Popisuje, jak extrahovat databázi PostgreSQL do souboru výpisu a obnovení ze souboru vytvořené pg_dump ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 2aa59bcf0d56358601b81730abe330a56ca35d02
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966904"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrace vaší databáze PostgreSQL pomocí výpisu a obnovení.
Můžete použít [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) extrahovat databázi PostgreSQL do souboru s výpisem paměti a [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) obnovit ze souboru archivu vytvořené pg_dump databázi PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [– Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) pomocí pravidel brány firewall umožňující přístup a databáze pod ním.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) nainstalované nástroje příkazového řádku

Postupujte podle těchto kroků k výpisu a obnovení databáze PostgreSQL:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Vytvořte soubor s výpisem paměti pomocí pg_dump, který obsahuje data, která mají být načteny
Chcete-li zálohovat existující PostgreSQL místní databáze nebo na virtuálním počítači, spusťte následující příkaz:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Například, pokud máte místní server a databázi s názvem **testdb** v ní
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Obnovení dat do cílové databáze Azure pro použití pg_restore PostrgeSQL
Po vytvoření cílové databázi, můžete použít příkaz pg_restore a -d, parametr--dbname k obnovení dat do cílové databáze ze souboru s výpisem paměti.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Včetně--no vlastník způsobí, že všechny objekty vytvořené během obnovení jeho vlastníkem uživatelem zadaným – uživatelské jméno. Další informace najdete v oficiální dokumentaci PostgreSQL na [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Pokud váš server PostgreSQL vyžaduje připojení SSL (na ve výchozím nastavení ve službě Azure Database for postgresql – servery), nastavte proměnnou prostředí `PGSSLMODE=require` tak, aby nástroj pg_restore připojení s protokolem SSL. Bez protokolu SSL je možné číst chyby  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na příkazovém řádku Windows, spusťte příkaz `SET PGSSLMODE=require` před spuštěním příkazu pg_restore. V systému Linux nebo prostředí Bash spusťte příkaz `export PGSSLMODE=require` před spuštěním příkazu pg_restore.
>

V tomto příkladu, obnovte data ze souboru s výpisem paměti **testdb.dump** do databáze **mypgsqldb** na cílovém serveru **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimalizace procesu migrace

Jeden způsob, jak migrovat existující databázi PostgreSQL do služby Azure Database pro služba PostgreSQL je zálohování databáze na zdroji a obnovení v Azure. Chcete-li minimalizovat čas potřebný k dokončení migrace, zvažte použití následujících parametrů pomocí zálohování a obnovení příkazů.

> [!NOTE]
> Podrobné informace o syntaxi, najdete v článcích [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Pro zálohování
- Proveďte zálohu s přepínačem -Fc, tak, aby obnovení můžete provádět paralelně urychlit. Příklad:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Pro obnovení
- Doporučujeme přesunout záložní soubor do virtuálního počítače Azure ve stejné oblasti jako serveru Azure Database for PostgreSQL migrují na a proveďte pg_restore z tohoto virtuálního počítače chcete snížit latenci sítě. Doporučujeme také, že virtuální počítač se vytvoří s [akcelerovanými síťovými službami](../virtual-network/create-vm-accelerated-networking-powershell.md) povolena.
- By mělo být provedeno již ve výchozím nastavení, ale otevřete soubor s výpisem paměti a ověřte, že příkazy create index se po vložení dat. Pokud není tento případ, přesuňte po vložení dat příkazy create index.
- Obnovit pomocí přepínače -Fc a -j *#* pro paralelní zpracování obnovení. *#* je počet jader na cílovém serveru. Můžete také zkusit s *#* nastavte zobrazení dopadů na dvojnásobný počet jader na cílový server. Příklad:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Soubor s výpisem paměti můžete také upravit tak, že přidáte příkaz *nastavit synchronous_commit = off;* na začátku a příkaz *nastavit synchronous_commit = on;* na konci. Není jeho zapnutím na konci, než aplikace změnit data, může způsobit ztrátu dalších údajů.

Nezapomeňte otestovat a ověřit tyto příkazy v testovacím prostředí před použitím v produkčním prostředí.

## <a name="next-steps"></a>Další postup
- Pokud chcete migrovat databázi PostgreSQL pomocí exportu a importu, naleznete v tématu [migrovat vaše databáze PostgreSQL pomocí exportu a importu](howto-migrate-using-export-and-import.md).
- Další informace o migraci databází do služby Azure Database for PostgreSQL, najdete v článku [Průvodce migrací databází](https://aka.ms/datamigration).
