---
title: Vytvoření uživatelů ve službě Azure Database pro MariaDB server
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty k interakci se službou Azure Database pro MariaDB server.
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 50154a7fee63eb3ff9e08155123f9e5962bbfcf0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946104"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Vytvoření uživatelů ve službě Azure Database pro MariaDB 
Tento článek popisuje, jak vytvořit uživatele ve službě Azure Database pro MariaDB.

Při prvním vytvoření Azure Database pro MariaDB, jste zadali přihlašovací uživatelské jméno správce serveru a heslo. Další informace, můžete postupovat podle [rychlý Start](quickstart-create-mariadb-server-database-using-azure-portal.md). Můžete vyhledat vaše přihlašovací jméno správce serveru uživatele z portálu Azure portal.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno: vyberte, vkládání, aktualizaci, odstranění, vytvořit, VYŘADIT, znovu NAČÍST, proces, odkazy, INDEX, ALTER, zobrazit databází, vytvořit dočasné tabulky, ZÁMKU tabulky, EXECUTE, podřízený server REPLIKACE, REPLIKACE KLIENTA, VYTVOŘIT ZOBRAZENÍ, ZOBRAZENÍ, VYTVOŘENÍ RUTINNÍ, ALTER RUTINU, VYTVOŘENÍ AKTIVAČNÍ UDÁLOSTI PRO UŽIVATELE, UDÁLOSTI,

Po vytvoření Azure Database pro MariaDB server slouží k vytvoření dalších uživatelů a udělit přístup správce k nim první účet uživatele správce serveru. Také účet správce serveru slouží k vytvoření méně privilegovaným uživatele, kteří mají přístup k jednotlivým databázových schématech.

## <a name="create-additional-admin-users"></a>Vytvořit další uživatele
1. Získejte název uživatelské informace a Správce připojení.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Můžete snadno vyhledat název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** stránky na webu Azure Portal. 

2. Použijte účet správce a heslo pro připojení k vašemu databázovému serveru. Použijte váš upřednostňovaný klientském nástroji, jako aplikace MySQL Workbench, mysql.exe, HeidiSQL nebo ostatním uživatelům. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [pomocí aplikace MySQL Workbench k připojení a dotazování dat](./connect-workbench.md)

3. Upravit a spuštěním následujícího kódu SQL. Nahraďte nové uživatelské jméno pro hodnotu zástupného symbolu `new_master_user`. Tato syntaxe uděluje oprávnění uvedená na databázových schématech (*.*) pro uživatelské jméno (new_master_user v tomto příkladu). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Ověřte, uděluje 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Vytvoření uživatelů databáze

1. Získejte název uživatelské informace a Správce připojení.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Můžete snadno vyhledat název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** stránky na webu Azure Portal. 

2. Použijte účet správce a heslo pro připojení k vašemu databázovému serveru. Použijte váš upřednostňovaný klientském nástroji, jako aplikace MySQL Workbench, mysql.exe, HeidiSQL nebo ostatním uživatelům. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [pomocí aplikace MySQL Workbench k připojení a dotazování dat](./connect-workbench.md)

3. Upravit a spuštěním následujícího kódu SQL. Nahraďte hodnotu zástupného symbolu `db_user` zamýšlený novým uživatelským jménem a zástupnou hodnotu `testdb` nahraďte vlastním názvem databáze.

   Tato syntaxe kódu sql vytvoří novou databázi s názvem testdb pro účely tohoto příkladu. Vytvoří nového uživatele ve službě Azure Database pro MariaDB službu a uděluje všechna oprávnění na nové schéma databáze (testdb.\*) pro daného uživatele. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ověřte podpory v rámci databáze.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Přihlaste se k serveru zadáním určené databázi pomocí nové uživatelské jméno a heslo. Tento příklad ukazuje příkazového řádku mysql. Pomocí tohoto příkazu budete vyzváni k zadání hesla pro uživatelské jméno. Nahraďte vlastní název serveru, název databáze a uživatelské jméno.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
Další informace týkající se správy uživatelských účtů, najdete v dokumentaci MariaDB pro [Správa uživatelských účtů](https://mariadb.com/kb/en/library/user-account-management/), [udělení syntaxe](https://mariadb.com/kb/en/library/grant/), a [oprávnění](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Další postup
Otevření brány firewall pro IP adresy počítačů noví uživatelé jim připojení povolit: [vytvořit a spravovat Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
