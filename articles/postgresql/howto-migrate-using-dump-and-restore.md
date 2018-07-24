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
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213646"
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

> [!IMPORTANT]
> Zkopírujte záložní soubory do Azure blob a úložiště a provést obnovení z něj, které by měly být mnohem rychlejší než provádění obnovení na Internetu.
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Obnovení dat do cílové databáze Azure pro použití pg_restore PostrgeSQL
Po vytvoření cílové databáze můžete použít příkaz pg_restore a -d, parametr--dbname k obnovení dat do cílové databáze ze souboru s výpisem paměti.
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

## <a name="next-steps"></a>Další postup
- Pokud chcete migrovat databázi PostgreSQL pomocí exportu a importu, naleznete v tématu [migrovat vaše databáze PostgreSQL pomocí exportu a importu](howto-migrate-using-export-and-import.md).
- Další informace o migraci databází do služby Azure Database for PostgreSQL, najdete v článku [Průvodce migrací databází](http://aka.ms/datamigration).
