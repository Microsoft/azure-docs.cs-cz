---
title: Vytvořit uživatele – Databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s databází Azure pro PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 127d484d6cfc35368803069f9c3d602e787baa56
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384343"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Vytvoření uživatelů v databázi Azure pro PostgreSQL – jeden server

Tento článek popisuje, jak můžete vytvořit uživatele v rámci databáze Azure pro postgreSQL server.

Pokud se chcete dozvědět o tom, jak vytvořit a spravovat uživatele předplatného Azure a jejich oprávnění, můžete navštívit [článek řízení přístupu azure založené na rolích (RBAC)](../role-based-access-control/built-in-roles.md) nebo [zkontrolovat, jak přizpůsobit role](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Účet správce serveru

Při prvním vytvoření databáze Azure pro PostgreSQL jste zadali uživatelské jméno a heslo správce serveru. Další informace můžete podle [úvodního startu](quickstart-create-server-database-portal.md) zobrazit postupný přístup. Vzhledem k tomu, že uživatelské jméno správce serveru je vlastní jméno, můžete najít zvolené uživatelské jméno správce serveru z webu Azure Portal.

Azure Database for PostgreSQL server se vytvoří s definovanými 3 výchozími rolemi. Tyto role můžete zobrazit spuštěním příkazu:`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- váš správce serveru

Váš uživatel správce serveru je členem role azure_pg_admin. Účet správce serveru však není součástí azure_superuser role. Vzhledem k tomu, že tato služba je spravovanou službou PaaS, je součástí role super uživatele pouze společnost Microsoft.

Modul PostgreSQL používá oprávnění k řízení přístupu k databázovým objektům, jak je popsáno v [dokumentaci k produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). V Azure Database for PostgreSQL je uživateli správce serveru udělena tato oprávnění: LOGIN, NOSUPERUSER, INHERIT, CREATEB, CREATEROLE, NOREPLICATION

Uživatelský účet správce serveru lze použít k vytvoření dalších uživatelů a udělení těchto uživatelů do role azure_pg_admin. Účet správce serveru lze také použít k vytvoření méně privilegovaných uživatelů a rolí, které mají přístup k jednotlivým databázím a schématům.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Jak vytvořit další uživatele správce v Azure Database pro PostgreSQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno najít na stránce **Přehled** serveru nebo na stránce **Vlastnosti** na webu Azure Portal.

2. K databázovému serveru se můžete připojit pomocí účtu správce a hesla. Použijte preferovaný klientský nástroj, například pgAdmin nebo psql.
   Pokud si nejste jisti, jak se připojit, podívejte [se na](./quickstart-create-server-database-portal.md)

3. Upravte a spusťte následující kód SQL. Nahraďte nové uživatelské jméno pro zástupnou hodnotu <new_user> a zástupné heslo nahraďte vlastním silným heslem. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Jak vytvořit uživatele databáze v Azure Database pro PostgreSQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno najít na stránce **Přehled** serveru nebo na stránce **Vlastnosti** na webu Azure Portal.

2. K databázovému serveru se můžete připojit pomocí účtu správce a hesla. Použijte preferovaný klientský nástroj, například pgAdmin nebo psql.

3. Upravte a spusťte následující kód SQL. Nahraďte zástupnou hodnotu `<db_user>` zamýšleným novým uživatelským jménem a zástupnou hodnotou `<newdb>` vlastním názvem databáze. Nahraďte zástupné heslo vlastním silným heslem.

   Tato syntaxe kódu SQL vytvoří novou databázi s názvem testdb, například účely. Potom vytvoří nového uživatele ve službě PostgreSQL a udělí oprávnění k připojení k nové databázi pro tohoto uživatele.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Pomocí účtu správce může být nutné udělit další oprávnění k zabezpečení objektů v databázi. Další podrobnosti o databázových rolích a oprávněních naleznete v [dokumentaci k PostgreSQL.](https://www.postgresql.org/docs/current/static/ddl-priv.html) Například:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Přihlaste se k serveru a zadejte určenou databázi pomocí nového uživatelského jména a hesla. Tento příklad ukazuje příkazový řádek psql. Pomocí tohoto příkazu budete vyzváni k zadání hesla pro uživatelské jméno. Nahraďte svůj vlastní název serveru, název databáze a uživatelské jméno.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy počítačů nových uživatelů, abyste jim umožnili připojení: [Vytvořte a spravujte pravidla brány firewall Azure Database for PostgreSQL pomocí portálu Azure nebo](howto-manage-firewall-using-portal.md) [Azure CLI](howto-manage-firewall-using-cli.md).

Další informace o správě uživatelských účtů naleznete v dokumentaci k produktu PostgreSQL pro [databázové role a oprávnění](https://www.postgresql.org/docs/current/static/user-manag.html), [Syntaxi GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)a [Oprávnění](https://www.postgresql.org/docs/current/static/ddl-priv.html).
