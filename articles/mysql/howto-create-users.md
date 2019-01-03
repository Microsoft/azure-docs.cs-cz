---
title: Vytvoření uživatelů ve službě Azure Database pro MySQL server
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s serveru Azure Database for MySQL.
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: e8714777b1f9f08de4d02fcb44c25197cdc48899
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546007"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Vytvoření uživatelů ve službě Azure Database pro MySQL server 
Tento článek popisuje, jak vytvořit uživatele v serveru Azure Database for MySQL.

Při prvním vytvoření Azure Database for MySQL poskytuje přihlašovací uživatelské jméno správce serveru a heslo. Další informace, můžete postupovat podle [rychlý Start](quickstart-create-mysql-server-database-using-azure-portal.md). Můžete vyhledat vaše přihlašovací jméno správce serveru uživatele z portálu Azure portal.

Uživatel správce serveru získá určitá privilegia, pro váš server, jak je uvedeno: VYBRAT, VLOŽIT, AKTUALIZOVAT, ODSTRANIT, VYTVOŘIT, VYŘADIT, ZNOVU NAČÍST, ZPRACOVÁNÍ, ODKAZY, INDEX, ALTER, ZOBRAZTE DATABÁZE, VYTVOŘIT DOČASNÉ TABULKY, UZAMKNOUT TABULKY, SPUŠTĚNÍ, VYTVOŘIT PODŘÍZENÝ SERVER REPLIKACE, KLIENT REPLIKACE, ZOBRAZENÍ, ZOBRAZENÍ, VYTVOŘENÍ RUTINNÍ, ALTER RUTINU, VYTVOŘTE UŽIVATELE , UDÁLOSTI, AKTIVAČNÍ UDÁLOSTI

Po vytvoření Azure Database for MySQL server slouží k vytvoření dalších uživatelů a udělit přístup správce k nim první účet uživatele správce serveru. Také účet správce serveru slouží k vytvoření méně privilegovaným uživatele, kteří mají přístup k jednotlivým databázových schématech.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak vytvořit další uživatele ve službě Azure Database for MySQL
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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Postup pro vytváření uživatelů databáze ve službě Azure Database for MySQL

1. Získejte název uživatelské informace a Správce připojení.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Můžete snadno vyhledat název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** stránky na webu Azure Portal. 

2. Použijte účet správce a heslo pro připojení k vašemu databázovému serveru. Použijte váš upřednostňovaný klientském nástroji, jako aplikace MySQL Workbench, mysql.exe, HeidiSQL nebo ostatním uživatelům. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [pomocí aplikace MySQL Workbench k připojení a dotazování dat](./connect-workbench.md)

3. Upravit a spuštěním následujícího kódu SQL. Nahraďte hodnotu zástupného symbolu `db_user` zamýšlený novým uživatelským jménem a zástupnou hodnotu `testdb` nahraďte vlastním názvem databáze.

   Tato syntaxe kódu sql vytvoří novou databázi s názvem testdb pro účely tohoto příkladu. Vytvoří nového uživatele ve službě MySQL a uděluje všechna oprávnění na nové schéma databáze (testdb.\*) pro daného uživatele. 

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

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Další postup
Otevření brány firewall pro IP adresy počítačů novým uživatelům povolit jim připojení: [Vytvoření a správě Azure Database for MySQL pravidla brány firewall pomocí webu Azure portal](howto-manage-firewall-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md).

Další informace týkající se správy uživatelských účtů, najdete v dokumentaci k produktu MySQL [Správa uživatelských účtů](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [udělení syntaxe](https://dev.mysql.com/doc/refman/5.7/en/grant.html), a [oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
