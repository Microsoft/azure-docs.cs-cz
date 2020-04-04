---
title: Vytvořit uživatele – Databáze Azure pro MariaDB
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database pro mariadb server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 1b79a49b2fb87ebf180aaaa40447f40c5a982c2e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632288"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Vytváření uživatelů ve službě Azure Database for MariaDB 
Tento článek popisuje, jak můžete vytvořit uživatele v Azure Database pro MariaDB.

Při prvním vytvoření databáze Azure pro MariaDB jste zadali přihlašovací jméno a heslo správce serveru. Další informace získáte v programu [Rychlý start](quickstart-create-mariadb-server-database-using-azure-portal.md). Přihlašovací uživatelské jméno správce serveru můžete najít na webu Azure Portal.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno: Select, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Po vytvoření databáze Azure pro MariaDB server je možné použít první účet správce serveru k vytvoření dalších uživatelů a udělit přístup správce k nim. Účet správce serveru lze také použít k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivým databázovým schématům.

> [!NOTE]
> Super oprávnění a dba role nejsou podporovány. Zkontrolujte [oprávnění](concepts-limits.md#privilege-support) v článku omezení pochopit, co není podporováno ve službě.

## <a name="create-additional-admin-users"></a>Vytvoření dalších uživatelů správce
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

## <a name="create-database-users"></a>Vytvořit uživatele databáze

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno najít na stránce **Přehled** serveru nebo na stránce **Vlastnosti** na webu Azure Portal. 

2. K databázovému serveru se můžete připojit pomocí účtu správce a hesla. Použijte preferovaný klientský nástroj, například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné. 
   Pokud si nejste jisti, jak se připojit, přečtěte [si část Použití pracovní plochy MySQL pro připojení a dotazování na data](./connect-workbench.md)

3. Upravte a spusťte následující kód SQL. Nahraďte zástupnou hodnotu `db_user` zamýšleným novým uživatelským jménem a zástupnou hodnotou `testdb` vlastním názvem databáze.

   Tato syntaxe kódu SQL vytvoří novou databázi s názvem testdb pro například účely. Pak vytvoří nového uživatele ve službě Azure Database for MariaDB a udělí všechna oprávnění novému schématu databáze (testdb.\*) pro tohoto uživatele. 

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

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Další informace o správě uživatelských účtů naleznete v dokumentaci k MarioDB pro [správu uživatelských účtů](https://mariadb.com/kb/en/library/user-account-management/), [syntaxi GRANT](https://mariadb.com/kb/en/library/grant/)a [oprávnění](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Další kroky
Otevřete bránu firewall pro IP adresy počítačů nových uživatelů, abyste jim umožnili připojení: [Vytvoření a správa pravidel brány firewall Azure Database for MariaDB pomocí portálu Azure Portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
