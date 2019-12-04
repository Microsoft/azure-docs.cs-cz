---
title: Vytvoření uživatelů – Azure Database for MySQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci se serverem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e76e63030cc8e10c857d361cca69e1d35ba8c2c1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770471"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Vytvoření uživatelů na serveru Azure Database for MySQL 
Tento článek popisuje, jak můžete vytvářet uživatele na serveru Azure Database for MySQL.

Při prvním vytvoření Azure Database for MySQL jste zadali přihlašovací uživatelské jméno a heslo správce serveru. Další informace najdete v [rychlém](quickstart-create-mysql-server-database-using-azure-portal.md)startu. Přihlašovací uživatelské jméno správce serveru můžete najít z Azure Portal.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno níže: vybrat, vložit, aktualizovat, odstranit, vytvořit, vyřadit, znovu načíst, zpracovat, odkazy, INDEXovat, změnit, Zobrazit databáze, vytvořit dočasné tabulky, zamknout tabulky, provést, replikace PODŘÍZENÝch, replikace KLIENT, VYTVOŘIT ZOBRAZENÍ, ZOBRAZIT ZOBRAZENÍ, VYTVOŘIT RUTINU, ZMĚNIT RUTINU, VYTVOŘIT UŽIVATELE, UDÁLOST, AKTIVAČNÍ UDÁLOST

Po vytvoření serveru Azure Database for MySQL můžete pomocí prvního uživatelského účtu správce serveru vytvořit další uživatele a udělit jim přístup správce. Účet správce serveru se taky dá použít k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivým schématům databáze.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Vytvoření dalších uživatelů s oprávněními správce v Azure Database for MySQL
1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal. 

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je MySQL Workbench, MySQL. exe, HeidiSQL nebo jiné. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [použití aplikace MySQL Workbench pro připojení a dotazování dat](./connect-workbench.md) .

3. Upravte a spusťte následující kód SQL. Nahraďte nové uživatelské jméno pro hodnotu zástupného symbolu `new_master_user`. Tato syntaxe uděluje uvedená oprávnění na všech schématech databáze ( *.* ) k uživatelskému jménu (new_master_user v tomto příkladu). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Ověřit granty 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Postup vytvoření uživatelů databáze v Azure Database for MySQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal. 

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je MySQL Workbench, MySQL. exe, HeidiSQL nebo jiné. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [použití aplikace MySQL Workbench pro připojení a dotazování dat](./connect-workbench.md) .

3. Upravte a spusťte následující kód SQL. Nahraďte hodnotu zástupného symbolu `db_user` vaším zamýšleným novým uživatelským jménem a zástupnou hodnotou `testdb` s vlastním názvem databáze.

   Tato syntaxe kódu SQL vytvoří pro ukázkové účely novou databázi s názvem TestDB. Potom vytvoří nového uživatele ve službě MySQL a udělí všem oprávněním novému schématu databáze (TestDB.\*) pro tohoto uživatele. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ověřte granty v rámci databáze.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Přihlaste se k serveru a určete určenou databázi pomocí nového uživatelského jména a hesla. Tento příklad ukazuje příkazový řádek MySQL. Pomocí tohoto příkazu se zobrazí výzva k zadání hesla pro uživatelské jméno. Nahraďte vlastní název serveru, název databáze a uživatelské jméno.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Další kroky
Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, aby se mohly připojit: [vytváření a správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal nebo rozhraní](howto-manage-firewall-using-portal.md) příkazového [řádku Azure](howto-manage-firewall-using-cli.md).

Další informace o správě uživatelských účtů najdete v dokumentaci k produktu MySQL pro [správu uživatelských účtů](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [udělení syntaxe](https://dev.mysql.com/doc/refman/5.7/en/grant.html)a [oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
