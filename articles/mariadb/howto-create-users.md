---
title: Vytvoření uživatelů ve službě Azure Database pro MariaDB server
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty k interakci se službou Azure Database pro MariaDB server.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 189e122e04d56d28c1e1e94d328569647614a124
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542123"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Vytvoření uživatelů ve službě Azure Database pro MariaDB 
Tento článek popisuje, jak vytvořit uživatele ve službě Azure Database pro MariaDB.

Při prvním vytvoření Azure Database pro MariaDB, jste zadali přihlašovací uživatelské jméno správce serveru a heslo. Další informace, můžete postupovat podle [rychlý Start](quickstart-create-mariadb-server-database-using-azure-portal.md). Můžete vyhledat vaše přihlašovací jméno správce serveru uživatele z portálu Azure portal.

Uživatel správce serveru získá určitá privilegia, pro váš server, jak je uvedeno: VYBRAT, VLOŽIT, AKTUALIZOVAT, ODSTRANIT, VYTVOŘIT, VYŘADIT, ZNOVU NAČÍST, ZPRACOVÁNÍ, ODKAZY, INDEX, ALTER, ZOBRAZTE DATABÁZE, VYTVOŘIT DOČASNÉ TABULKY, UZAMKNOUT TABULKY, SPUŠTĚNÍ, VYTVOŘIT PODŘÍZENÝ SERVER REPLIKACE, KLIENT REPLIKACE, ZOBRAZENÍ, ZOBRAZENÍ, VYTVOŘENÍ RUTINNÍ, ALTER RUTINU, VYTVOŘTE UŽIVATELE , UDÁLOSTI, AKTIVAČNÍ UDÁLOSTI

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
Otevření brány firewall pro IP adresy počítačů novým uživatelům povolit jim připojení: [Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
