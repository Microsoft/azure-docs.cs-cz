---
title: Výpis a obnovení Azure Database for PostgreSQL – jeden server
description: Databázi PostgreSQL můžete extrahovat do souboru s výpisem paměti. Pak můžete obnovit soubor vytvořený pomocí pg_dump v Azure Database for PostgreSQL jednom serveru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450051"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Migrace databáze PostgreSQL pomocí výpisu a obnovení
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

K extrakci databáze PostgreSQL do souboru s výpisem paměti můžete použít [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) . Pak pomocí [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) obnovte databázi PostgreSQL z archivního souboru vytvořeného nástrojem `pg_dump` .

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md), včetně pravidel brány firewall pro povolení přístupu.
- jsou nainstalované nástroje příkazového řádku [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) .

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Vytvoření souboru s výpisem paměti obsahujícího data, která mají být načtena

Pokud chcete zálohovat stávající databázi PostgreSQL místně nebo na virtuálním počítači, spusťte následující příkaz:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Například pokud máte místní server a databázi s názvem **TestDB** , spusťte:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Obnovení dat do cílové databáze

Po vytvoření cílové databáze můžete pomocí `pg_restore` příkazu a  `--dbname` parametru obnovit data do cílové databáze ze souboru s výpisem paměti.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Zahrnutí `--no-owner` parametru způsobí, že všechny objekty vytvořené během obnovení mají vlastní uživatel zadaný pomocí `--username` . Další informace najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Na serverech Azure Database for PostgreSQL jsou připojení TLS/SSL ve výchozím nastavení zapnutá. Pokud váš server PostgreSQL vyžaduje připojení TLS/SSL, ale nemá je, nastavte proměnnou prostředí `PGSSLMODE=require` tak, aby se nástroj pg_restore připojil k TLS. Bez TLS může chyba přečíst: "závažné: připojení SSL je povinné. Zadejte prosím možnosti SSL a zkuste to znovu. " V příkazovém řádku Windows spusťte příkaz `SET PGSSLMODE=require` před spuštěním `pg_restore` příkazu. V systému Linux nebo bash spusťte příkaz `export PGSSLMODE=require` před spuštěním `pg_restore` příkazu. 
>

V tomto příkladu obnovte data ze souboru s výpisem paměti **TestDB. vypíšete** do databáze **mypgsqldb** na cílovém serveru **mydemoserver.Postgres.Database.Azure.com**.

Tady je příklad, jak použít tento `pg_restore` pro jeden server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Tady je příklad, jak použít tento objekt `pg_restore` pro flexibilní Server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Optimalizace procesu migrace

Jedním ze způsobů, jak migrovat stávající databázi PostgreSQL do Azure Database for PostgreSQL, je zálohovat databázi ve zdroji a obnovit ji v Azure. Pokud chcete zkrátit dobu potřebnou k dokončení migrace, zvažte použití následujících parametrů s příkazy Backup a Restore.

> [!NOTE]
> Podrobné informace o syntaxi naleznete v tématu [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Pro zálohu

Proveďte zálohu pomocí `-Fc` přepínače, aby bylo možné provést obnovení paralelně a zrychlit. Například:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>Pro obnovení

- Přesuňte záložní soubor do virtuálního počítače Azure ve stejné oblasti jako server Azure Database for PostgreSQL, do kterého migrujete. Pokud `pg_restore` chcete snížit latenci sítě, proveďte z tohoto virtuálního počítače. Vytvořte virtuální počítač s povolenými [akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md) službami.

- Otevřete soubor s výpisem paměti a ověřte, zda jsou příkazy CREATE index po vložení dat. Pokud tomu tak není, přesuňte příkazy CREATE index po vložení dat. Toto by se už mělo udělat ve výchozím nastavení, ale je vhodné ho potvrdit.

- Obnovte s přepínači `-Fc` a `-j` (s číslem), aby se paralelizovato obnovení. Číslo, které zadáte, je počet jader na cílovém serveru. Pro zobrazení dopadu můžete také nastavit dvojnásobek počtu jader cílového serveru.

    Tady je příklad, jak použít tento `pg_restore` pro jeden server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Tady je příklad, jak použít tento objekt `pg_restore` pro flexibilní Server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- Soubor s výpisem paměti můžete také upravit přidáním příkazu `set synchronous_commit = off;` na začátek a příkazu `set synchronous_commit = on;` na konci. Nezapne se na konci, předtím než aplikace změní data, může dojít k následné ztrátě dat.

- Na cílovém serveru Azure Database for PostgreSQL před obnovením zvažte následující:
    
  - Vypněte sledování výkonu dotazů. Tyto statistiky nejsou během migrace potřeba. To můžete provést nastavením `pg_stat_statements.track` , `pg_qs.query_capture_mode` a `pgms_wait_sampling.query_capture_mode` na `NONE` .

  - K urychlení migrace použijte vysoce výpočetní a vysoce paměťovou SKU, například 32 vCore paměť optimalizované pro optimalizaci. Až se obnovení dokončí, můžete se snadno škálovat zpátky na preferovanou skladovou jednotku. Čím vyšší je SKU, tím více paralelismu můžete dosáhnout zvýšením odpovídajícího `-j` parametru v `pg_restore` příkazu.

  - Další IOPS na cílovém serveru můžou zlepšit výkon při obnovení. Další IOPS můžete zřídit zvýšením velikosti úložiště serveru. Toto nastavení není vratné, ale vezměte v úvahu, jestli by měl vyšší vstupně-výstupní operace těžit z vašich skutečných úloh do budoucna.

Nezapomeňte otestovat a ověřit tyto příkazy v testovacím prostředí předtím, než je použijete v produkčním prostředí.

## <a name="next-steps"></a>Další kroky

- Postup migrace databáze PostgreSQL pomocí exportu a importu najdete v tématu [migrace databáze PostgreSQL pomocí exportu a importu](howto-migrate-using-export-and-import.md).
- Další informace o migraci databází do Azure Database for PostgreSQL najdete v [Průvodci migrací databáze](https://aka.ms/datamigration).


