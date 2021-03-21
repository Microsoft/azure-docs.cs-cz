---
title: Migrace databáze-Azure Database for PostgreSQL – jeden server
description: Popisuje, jak extrahovat databázi PostgreSQL do souboru skriptu a importovat data do cílové databáze z daného souboru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d0f1824a1e4754dddea26484c836df79e95246d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580976"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrace databáze PostgreSQL pomocí exportu a importu
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

K extrakci databáze PostgreSQL do souboru skriptu a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) pro import dat do cílové databáze z tohoto souboru můžete použít [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) .

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md) s pravidly brány firewall pro povolení přístupu a databáze pod ní.
- nainstalovaný nástroj příkazového řádku [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html)
- nainstalovaný nástroj příkazového řádku [psql](https://www.postgresql.org/docs/current/static/app-psql.html)

Při exportu a importu databáze PostgreSQL postupujte podle těchto kroků.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Vytvoření souboru skriptu pomocí pg_dump obsahujícího data, která mají být načtena
Pokud chcete exportovat stávající databázi PostgreSQL místně nebo na virtuálním počítači do souboru skriptu SQL, spusťte v existujícím prostředí následující příkaz:

```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Například pokud máte místní server a databázi s názvem **TestDB** v ní:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importovat data na cílovém Azure Database for PostgreSQL
Pomocí příkazového řádku psql a parametru--dbname (-d) můžete importovat data do serveru Azure Database for PostgreSQL a načíst data ze souboru SQL.

```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
Tento příklad používá nástroj psql a soubor skriptu s názvem **TestDB. SQL** z předchozího kroku k importu dat do databáze **mypgsqldb** cílového serveru **mydemoserver.Postgres.Database.Azure.com**.

V případě **jednoho serveru** použijte tento příkaz. 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

V případě **flexibilního serveru** použijte tento příkaz.  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>Další kroky
- Postup migrace databáze PostgreSQL pomocí výpisu a obnovení najdete v tématu [migrace databáze PostgreSQL pomocí výpisu a obnovení](howto-migrate-using-dump-and-restore.md).
- Další informace o migraci databází do Azure Database for PostgreSQL najdete v [Průvodci migrací databáze](https://aka.ms/datamigration).
