---
title: Migrace databáze – databáze Azure pro PostgreSQL – jeden server
description: Popisuje, jak extrahovat databázi PostgreSQL do souboru skriptu a importovat data do cílové databáze z tohoto souboru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770199"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrace databáze PostgreSQL pomocí exportu a importu
Můžete použít [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) extrahovat databázi PostgreSQL do souboru skriptu a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) importovat data do cílové databáze z tohoto souboru.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) s pravidly brány firewall, který umožňuje přístup a databázi pod ním.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) nainstalován nástroj příkazového řádku
- Nainstalován nástroj příkazového řádku [pSQL](https://www.postgresql.org/docs/current/static/app-psql.html)

Chcete-li exportovat a importovat databázi PostgreSQL, postupujte takto.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Vytvoření souboru skriptu pomocí pg_dump obsahujícídata, která mají být načtena
Chcete-li exportovat existující databázi PostgreSQL místně nebo ve virtuálním počítači do souboru skriptu SQL, spusťte ve stávajícím prostředí následující příkaz:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Například pokud máte místní server a databázi s názvem **testdb** v něm:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Import dat v cílové databázi Azure pro PostgreSQL
Příkazový řádek psql a parametr --dbname (-d) můžete importovat data do databáze Azure pro server PostgreSQL a načíst data ze souboru SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Tento příklad používá nástroj psql a soubor skriptu s názvem **testdb.sql** z předchozího kroku k importu dat do databáze **mypgsqldb** na cílovém serveru **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Další kroky
- Chcete-li migrovat databázi PostgreSQL pomocí výpisu a obnovení, přečtěte si informace [o migraci databáze PostgreSQL pomocí výpisu a obnovení](howto-migrate-using-dump-and-restore.md).
- Další informace o migraci databází do databáze Azure pro PostgreSQL najdete v [průvodci migrací databáze](https://aka.ms/datamigration). 
