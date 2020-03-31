---
title: Výpis a obnovení – databáze Azure pro PostgreSQL – jeden server
description: Popisuje, jak extrahovat databázi PostgreSQL do souboru s výpisem stavu paměti a obnovit ze souboru vytvořeného pg_dump v Azure Database for PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770233"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrace databáze PostgreSQL pomocí výpisu a obnovení
Můžete použít [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) extrahovat databázi PostgreSQL do souboru výpisu a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) k obnovení databáze PostgreSQL z archivního souboru vytvořeného pg_dump.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) s pravidly brány firewall, který umožňuje přístup a databázi pod ním.
- instalované nástroje příkazového řádku [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)

Chcete-li vypáčit a obnovit databázi PostgreSQL, postupujte takto:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Vytvoření souboru s výpisem stavu paměti pomocí pg_dump obsahujícídata, která mají být načtena
Chcete-li zálohovat existující databázi PostgreSQL místně nebo ve virtuálním počítači, spusťte následující příkaz:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Například pokud máte místní server a databázi s názvem **testdb** v něm
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Obnovení dat do cílové databáze Azure pro PostgreSQL pomocí pg_restore
Po vytvoření cílové databáze můžete pomocí příkazu pg_restore a parametru -d, --dbname obnovit data do cílové databáze ze souboru výpisu.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Včetně parametru --no-owner způsobí, že všechny objekty vytvořené během obnovení budou vlastněny uživatelem zadaným s --username. Další informace naleznete v oficiální dokumentaci PostgreSQL o [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Pokud váš server PostgreSQL vyžaduje připojení SSL (ve výchozím nastavení v Azure `PGSSLMODE=require` Database for PostgreSQL servery), nastavte proměnnou prostředí tak, aby se nástroj pg_restore spojil s Protokolem SSL. Bez SSL může chyba číst`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> V příkazovém řádku systému Windows spusťte příkaz `SET PGSSLMODE=require` před spuštěním příkazu pg_restore. V Linuxu nebo `export PGSSLMODE=require` Bash spustit příkaz před spuštěním příkazu pg_restore.
>

V tomto příkladu obnovte data z souboru s výpisem stavu **paměti testdb.dump** do databáze **mypgsqldb** na cílovém serveru **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimalizace procesu migrace

Jedním ze způsobů, jak migrovat stávající databázi PostgreSQL do služby Azure Database for PostgreSQL, je zálohovat databázi ve zdroji a obnovit ji v Azure. Chcete-li minimalizovat čas potřebný k dokončení migrace, zvažte použití následujících parametrů s příkazy pro zálohování a obnovení.

> [!NOTE]
> Podrobné informace o syntaxi naleznete v článcích [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Pro zálohování
- Vezměte zálohu s přepínačem -Fc, abyste mohli provést obnovení paralelně, abyste ho urychlili. Například:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Pro obnovení
- Doporučujeme přesunout záložní soubor do virtuálního počítače Azure ve stejné oblasti jako Azure Database for PostgreSQL server, na který migrujete, a provést pg_restore z tohoto virtuálního počítače, abyste snížili latenci sítě. Doporučujeme také, aby se virtuální virtuální byl vytvořit s [povoleným zrychleným připojením k síti.](../virtual-network/create-vm-accelerated-networking-powershell.md)

- Mělo by být již provedeno ve výchozím nastavení, ale otevřete soubor výpisu a ověřte, zda příkazy create index jsou po vložení dat. Pokud tomu tak není, přesuňte příkazy vytvořit index po vložení dat.

- Obnovení s přepínači -Fc *#* a -j paralelizovat obnovení. *#* je počet jader na cílovém serveru. Můžete také zkusit s *#* nastaveným na dvojnásobek počtu jader cílového serveru, abyste viděli dopad. Například:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Soubor výpisu můžete také upravit přidáním sady příkazů *synchronous_commit = off;* na začátku a sada příkazů *synchronous_commit = zapnuto;* na konci. Nezapnutí na konci, před tím, než aplikace změní data, může mít za následek následnou ztrátu dat.

- Na cílové Azure Database pro PostgreSQL server, zvažte provedení následujícího než obnovení:
    - Vypněte sledování výkonu dotazu, protože tyto statistiky nejsou potřeba během migrace. To lze provést nastavením pg_stat_statements.track, pg_qs.query_capture_mode a pgms_wait_sampling.query_capture_mode na NONE.

    - Použijte vysokou výpočetní a vysokou paměťovou sku, jako je optimalizovaná 32 virtuálních jader, abyste urychlili migraci. Po dokončení obnovení můžete snadno škálovat zpět na upřednostňovanou sku. Čím vyšší je sku, tím více paralelismu `-j` můžete dosáhnout zvýšením odpovídající parametr v příkazu pg_restore. 

    - Další videa VOPS na cílovém serveru může zlepšit výkon obnovení. Můžete zřídit více viops zvýšením velikosti úložiště serveru. Toto nastavení není reverzibilní, ale zvažte, zda vyšší vipos by prospěch vaše skutečné pracovní vytížení v budoucnu.

Nezapomeňte tyto příkazy otestovat a ověřit v testovacím prostředí před jejich použitím v produkčním prostředí.

## <a name="next-steps"></a>Další kroky
- Informace o migraci databáze PostgreSQL pomocí exportu a importu naleznete v [tématu Migrace databáze PostgreSQL pomocí exportu a importu](howto-migrate-using-export-and-import.md).
- Další informace o migraci databází do databáze Azure pro PostgreSQL najdete v [průvodci migrací databáze](https://aka.ms/datamigration).
