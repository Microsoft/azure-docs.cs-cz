---
title: Vytváření uživatelů – Azure Database for MySQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database for MySQL server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 99b614de87c666d1cb1fb8a34eaafadf6fa82849
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632553"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Vytvoření uživatelů v Azure Database pro mySQL server

Tento článek popisuje, jak můžete vytvořit uživatele v azure databázi pro server MySQL.

Při prvním vytvoření databáze Azure pro MySQL jste zadali přihlašovací jméno a heslo správce serveru. Další informace získáte v programu [Rychlý start](quickstart-create-mysql-server-database-using-azure-portal.md). Přihlašovací uživatelské jméno správce serveru můžete najít na webu Azure Portal.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno: Select, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Po vytvoření azure databáze pro mysql server, můžete použít první účet správce serveru k vytvoření dalších uživatelů a udělit přístup správce k nim. Účet správce serveru lze také použít k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivým databázovým schématům.

> [!NOTE]
> Super oprávnění a dba role nejsou podporovány. Zkontrolujte [oprávnění](concepts-limits.md#privilege-support) v článku omezení pochopit, co není podporováno ve službě.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak vytvořit další uživatele správce v Azure Database for MySQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno najít na stránce **Přehled** serveru nebo na stránce **Vlastnosti** na webu Azure Portal.

2. K databázovému serveru se můžete připojit pomocí účtu správce a hesla. Použijte preferovaný klientský nástroj, například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné.
   Pokud si nejste jisti, jak se připojit, přečtěte [si část Použití pracovní plochy MySQL pro připojení a dotazování na data](./connect-workbench.md)

3. Upravte a spusťte následující kód SQL. Nahraďte nové uživatelské jméno `new_master_user`pro zástupnou hodnotu . Tato syntaxe uděluje uvedená oprávnění ve všech schématech databáze (*.*) uživatelskému jménu (new_master_user v tomto příkladu).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Ověření grantů

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Jak vytvořit uživatele databáze v Azure Database for MySQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno najít na stránce **Přehled** serveru nebo na stránce **Vlastnosti** na webu Azure Portal.

2. K databázovému serveru se můžete připojit pomocí účtu správce a hesla. Použijte preferovaný klientský nástroj, například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné.
   Pokud si nejste jisti, jak se připojit, přečtěte [si část Použití pracovní plochy MySQL pro připojení a dotazování na data](./connect-workbench.md)

3. Upravte a spusťte následující kód SQL. Nahraďte zástupnou hodnotu `db_user` zamýšleným novým uživatelským jménem a zástupnou hodnotou `testdb` vlastním názvem databáze.

   Tato syntaxe kódu SQL vytvoří novou databázi s názvem testdb pro například účely. Potom vytvoří nového uživatele ve službě MySQL a udělí všechna oprávnění novému schématu databáze (testdb.\*) pro tohoto uživatele.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Ověřte granty v databázi.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Přihlaste se k serveru a zadejte určenou databázi pomocí nového uživatelského jména a hesla. Tento příklad ukazuje příkazový řádek mysql. Pomocí tohoto příkazu budete vyzváni k zadání hesla pro uživatelské jméno. Nahraďte svůj vlastní název serveru, název databáze a uživatelské jméno.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy počítačů nových uživatelů, abyste jim umožnili připojení: [Vytvořte a spravujte pravidla brány firewall Azure Database for MySQL pomocí portálu Azure nebo](howto-manage-firewall-using-portal.md) [nastavení uživatelského příkazu Azure](howto-manage-firewall-using-cli.md).

Další informace o správě uživatelských účtů naleznete v dokumentaci k produktu MySQL pro [správu uživatelských účtů](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [syntaxi GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html)a [oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
