---
title: Vytváření uživatelů – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database for PostgreSQLem na jednom serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: 1dbbdde03d1c24882be298d8c81362744debeecf
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704935"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Vytváření uživatelů v Azure Database for PostgreSQL – jeden server

Tento článek popisuje, jak můžete vytvářet uživatele v rámci serveru Azure Database for PostgreSQL.

Pokud chcete získat informace o tom, jak vytvářet a spravovat uživatele předplatného Azure a jejich oprávnění, můžete navštívit [článek řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/built-in-roles.md) nebo si přečtěte, [jak přizpůsobit role](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Účet správce serveru

Při prvním vytvoření Azure Database for PostgreSQL jste zadali uživatelské jméno a heslo správce serveru. Další informace najdete v tématu [rychlý Start](quickstart-create-server-database-portal.md) , kde se můžete podívat na podrobný postup. Vzhledem k tomu, že uživatelské jméno správce serveru je vlastní název, můžete najít zvolené uživatelské jméno správce serveru z Azure Portal.

Server Azure Database for PostgreSQL se vytvoří se 3 definovanými výchozími rolemi. Tyto role můžete zobrazit spuštěním příkazu: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- uživatel s oprávněními správce serveru

Uživatel správce serveru je členem role azure_pg_admin. Účet správce serveru ale není součástí role azure_superuser. Vzhledem k tomu, že tato služba je spravovaná služba PaaS, pouze společnost Microsoft je součástí role superuživatele.

Modul PostgreSQL využívá oprávnění k řízení přístupu k databázovým objektům, jak je popsáno v [dokumentaci k produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). V Azure Database for PostgreSQL má uživatel pro správu serveru udělená tato oprávnění: LOGIN, NOSUPERUSER, DĚDĚNí, CREATEDB, CREATEROLE, inreplikace.

Uživatelský účet správce serveru se dá použít k vytvoření dalších uživatelů a udělení těchto uživatelů do role azure_pg_admin. Účet správce serveru se taky dá použít k vytvoření méně privilegovaných uživatelů a rolí, které mají přístup k jednotlivým databázím a schématům.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Vytvoření dalších uživatelů s oprávněními správce v Azure Database for PostgreSQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal.

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, například pgAdmin nebo psql.
   Pokud si nejste jisti, jak se připojit, přečtěte si [rychlý Start](./quickstart-create-server-database-portal.md) .

3. Upravte a spusťte následující kód SQL. Nahraďte nové uživatelské jméno pro hodnotu zástupného symbolu <new_user> a nahraďte zástupný symbol heslem vlastním silným heslem. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Postup vytvoření uživatelů databáze v Azure Database for PostgreSQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal.

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, například pgAdmin nebo psql.

3. Upravte a spusťte následující kód SQL. Nahraďte hodnotu zástupného symbolu `<db_user>` vaším zamýšleným novým uživatelským jménem a zástupnou hodnotou `<newdb>` s vlastním názvem databáze. Zástupné heslo nahraďte vlastním silným heslem.

   Tato syntaxe kódu SQL vytvoří pro účely například novou databázi s názvem TestDB. Potom vytvoří nového uživatele ve službě PostgreSQL a udělí oprávnění k nové databázi pro tohoto uživatele.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Pomocí účtu správce může být potřeba udělit další oprávnění k zabezpečení objektů v databázi. Další podrobnosti o databázových rolích a oprávněních najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) . Například:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Přihlaste se k serveru a určete určenou databázi pomocí nového uživatelského jména a hesla. Tento příklad ukazuje příkazový řádek psql. Pomocí tohoto příkazu se zobrazí výzva k zadání hesla pro uživatelské jméno. Nahraďte vlastní název serveru, název databáze a uživatelské jméno.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, aby se mohly připojit: [vytváření a správa Azure Database for PostgreSQL pravidel brány firewall pomocí Azure Portal nebo rozhraní](howto-manage-firewall-using-portal.md) příkazového [řádku Azure](howto-manage-firewall-using-cli.md).

Další informace o správě uživatelských účtů najdete v dokumentaci k produktu PostgreSQL pro [databázové role a oprávnění](https://www.postgresql.org/docs/current/static/user-manag.html), [udělení syntaxe](https://www.postgresql.org/docs/current/static/sql-grant.html)a [oprávnění](https://www.postgresql.org/docs/current/static/ddl-priv.html).
