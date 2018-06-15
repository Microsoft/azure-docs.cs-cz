---
title: Vytvořte uživatele v Azure databázi PostgreSQL serveru
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database pro PostgreSQL server.
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29575636"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Vytvořte uživatele v Azure databázi PostgreSQL serveru 
Tento článek popisuje, jak můžete vytvořit uživatele v databázi Azure pro PostgreSQL server.

## <a name="the-server-admin-account"></a>Účet správce serveru
Při prvním vytvoření databáze Azure pro PostgreSQL, můžete zadat uživatelské jméno správce serveru a heslo. Další informace, můžete postupovat podle [rychlý Start](quickstart-create-server-database-portal.md) zobrazíte podrobné přístup. Vzhledem k tomu, že uživatelské jméno správce serveru je název vlastního, můžete vyhledat uživatelské jméno správce serveru vybrané z portálu Azure.

Vytvoření databáze Azure PostgreSQL serveru s rolí 3 výchozí definované. Tyto role můžete zjistit spuštěním příkazu: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- uživatel s oprávněními správce vašeho serveru

Uživatel s oprávněními správce váš server je členem azure_pg_admin role. Účet správce serveru ale není součástí azure_superuser role. Vzhledem k tomu, že tato služba je spravovaná služba PaaS, pouze společnosti Microsoft je součástí role superuživatele. 

Modul PostgreSQL používá oprávnění k řízení přístupu k databázové objekty, jak je popsáno v [PostgreSQL dokumentaci](https://www.postgresql.org/docs/current/static/sql-createrole.html). V databázi Azure pro PostgreSQL, uživatel správce serveru jsou udělena tato oprávnění: přihlášení, NOSUPERUSER, ZDĚDĚNÉ, CREATEDB, CREATEROLE, NOREPLICATION

Uživatelský účet správce serveru můžete použít k vytvoření dalších uživatelů a udělit uživatelům, do azure_pg_admin role. Navíc účet správce serveru slouží k vytvoření méně privilegovaných uživatelů a rolí, které mají přístup k jednotlivé databáze a schématy.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Jak vytvořit další správci v databázi Azure pro PostgreSQL
1. Získání připojení informace a správce uživatelské jméno.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Budete moci snadno najít název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** na portálu Azure. 

2. Pomocí účtu správce a heslo pro připojení k databázovému serveru. Použijte váš upřednostňované klientském nástroji, jako je například pgAdmin nebo psql.
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [připojit k databázi PostgreSQL pomocí psql v prostředí cloudu](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Upravit a spustit následující kód SQL. Nahraďte nové uživatelské jméno pro hodnotu zástupného symbolu < new_user > a nahraďte zástupný symbol heslo silné heslo. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Jak vytvořit uživatele databáze v databázi Azure pro PostgreSQL

1. Získání připojení informace a správce uživatelské jméno.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Budete moci snadno najít název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** na portálu Azure. 

2. Pomocí účtu správce a heslo pro připojení k databázovému serveru. Použijte váš upřednostňované klientském nástroji, jako je například pgAdmin nebo psql.

3. Upravit a spustit následující kód SQL. Nahraďte hodnotu zástupného symbolu `<db_user>` se zamýšlené nové uživatelské jméno a hodnotu zástupného symbolu `<newdb>` s vlastní název databáze. Nahraďte zástupný symbol heslo silné heslo. 

   Tato syntaxe kódu sql vytvoří novou databázi s názvem testdb, pro účely příklad. Vytvoří nového uživatele ve službě PostgreSQL a uděluje oprávnění připojit k nové databázi pro daného uživatele. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. Pomocí účtu správce, můžete udělit další oprávnění k zabezpečení objektů v databázi. Odkazovat [PostgreSQL dokumentace](https://www.postgresql.org/docs/current/static/ddl-priv.html) další podrobnosti o databázové role a oprávnění. Příklad: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Přihlaste se k serveru, zadání určené databázi pomocí nové uživatelské jméno a heslo. Tento příklad ukazuje psql příkazového řádku. Pomocí tohoto příkazu zobrazí se výzva k zadání hesla pro uživatelské jméno. Nahraďte váš vlastní název serveru, názvu databáze a uživatelské jméno.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Další postup
Otevření brány firewall pro IP adresy počítačů noví uživatelé jim připojení povolit: [vytvořit a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí portálu Azure](howto-manage-firewall-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md).

Další informace týkající se správy uživatelských účtů, najdete v dokumentaci k produktu PostgreSQL [databázové role a oprávnění](https://www.postgresql.org/docs/current/static/user-manag.html), [GRANT syntaxe](https://www.postgresql.org/docs/current/static/sql-grant.html), a [oprávnění](https://www.postgresql.org/docs/current/static/ddl-priv.html).
