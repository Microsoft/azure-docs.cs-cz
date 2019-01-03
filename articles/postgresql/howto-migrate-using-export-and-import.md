---
title: Migrace databáze pomocí importu a exportu v Azure Database for PostgreSQL
description: Popisuje, jak extrahovat databázi PostgreSQL do souboru skriptu a importovat data do cílové databáze z tohoto souboru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: ecd7dc225379fc9d3eda6fb2e80e3c47a73db49b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547622"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrace vaší databáze PostgreSQL pomocí exportu a importu
Můžete použít [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) extrahovat databázi PostgreSQL do souboru skriptu a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) pro import dat do cílové databáze z tohoto souboru.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [– Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) pomocí pravidel brány firewall umožňující přístup a databáze pod ním.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) nainstalovaný nástroj příkazového řádku
- [Nástroj psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) nainstalovaný nástroj příkazového řádku

Následujícím postupem exportujte a importujte databázi PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Vytvoření souboru skriptu pomocí pg_dump, který obsahuje data, která mají být načteny
K exportu vašeho existujícího PostgreSQL místní databáze nebo do souboru skriptu sql na virtuálním počítači, spusťte následující příkaz v prostředí vašeho stávajícího:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Například, pokud máte místní server a databázi s názvem **testdb** v ní:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Import dat na cíl – Azure Database for PostgreSQL
Můžete použít příkazového řádku psql a parametr--dbname (-d) k importu dat do Azure Database for PostgreSQL server a k načtení dat ze souboru sql.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Tento příklad používá nástroj psql a souboru skriptu s názvem **testdb.sql** z předchozího kroku k importu dat do databáze **mypgsqldb** na cílovém serveru  **mydemoserver.postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Další postup
- Pokud chcete migrovat databázi PostgreSQL pomocí výpisu a obnovení, najdete v článku [migrovat vaše databáze PostgreSQL pomocí výpisu a obnovení](howto-migrate-using-dump-and-restore.md).
- Další informace o migraci databází do služby Azure Database for PostgreSQL, najdete v článku [Průvodce migrací databází](https://aka.ms/datamigration). 
