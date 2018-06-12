---
title: Vytvořte uživatele v databázi Azure pro server databáze MySQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database pro server databáze MySQL.
services: mysql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee74ea9e114f6401bfcafe44ca3caedfcd0005c5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265614"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Vytvořte uživatele v databázi Azure pro server databáze MySQL 
Tento článek popisuje, jak můžete vytvořit uživatele v databázi Azure pro server databáze MySQL.

Při prvním vytvoření Azure Database pro databázi MySQL, můžete zadat server správce přihlašovací uživatelské jméno a heslo. Další informace, můžete postupovat podle [rychlý Start](quickstart-create-mysql-server-database-using-azure-portal.md). Můžete vyhledat název serveru správce přihlášení uživatele z portálu Azure.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno: vybrat, vložit, aktualizovat, odstranit, vytvořit, VYŘADIT, znovu NAČÍST, proces, odkazy, INDEX, ALTER, zobrazit databáze, vytvoření dočasných tabulek, ZÁMKU tabulky, EXECUTE, PODŘÍZENÝ REPLIKACE, REPLIKACE KLIENT, VYTVOŘIT ZOBRAZENÍ, NASTAVÍ ZOBRAZENÍ, VYTVOŘIT RUTINY, ALTER RUTINY, VYTVOŘIT AKTIVAČNÍ UDÁLOST UŽIVATELE, UDÁLOSTI,

Po vytvoření databáze MySQL serveru Azure slouží k vytvoření dalších uživatelů a udělit přístup správce k nim první účet uživatele správce serveru. Navíc účet správce serveru slouží k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivých databázových schématech.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak vytvořit další správci ve službě Azure Database pro databázi MySQL
1. Získání připojení informace a správce uživatelské jméno.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Budete moci snadno najít název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** na portálu Azure. 

2. Pomocí účtu správce a heslo pro připojení k databázovému serveru. Použijte váš upřednostňované klientském nástroji, například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [MySQL Workbench použít k připojení a dotazování dat](./connect-workbench.md)

3. Upravit a spustit následující kód SQL. Nahraďte nové uživatelské jméno pro hodnotu zástupného symbolu `new_master_user`. Tuto syntaxi uděluje oprávnění uvedených na všechny databáze schémata (*.*) k názvu uživatele (new_master_user v tomto příkladu). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Ověřte uděluje 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Postup vytvoření databáze uživatelů ve službě Azure Database pro databázi MySQL

1. Získání připojení informace a správce uživatelské jméno.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Budete moci snadno najít název serveru a přihlašovací údaje ze serveru **přehled** stránky nebo **vlastnosti** na portálu Azure. 

2. Pomocí účtu správce a heslo pro připojení k databázovému serveru. Použijte váš upřednostňované klientském nástroji, například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné. 
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [MySQL Workbench použít k připojení a dotazování dat](./connect-workbench.md)

3. Upravit a spustit následující kód SQL. Nahraďte hodnotu zástupného symbolu `db_user` se zamýšlené nové uživatelské jméno a hodnotu zástupného symbolu `testdb` s vlastní název databáze.

   Tato syntaxe kódu sql vytvoří novou databázi s názvem testdb pro účely příklad. Vytvoří nového uživatele ve službě MySQL a uděluje všechna oprávnění pro nové schéma databáze (testdb.\*) pro daného uživatele. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ověřte uděluje v databázi.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Přihlaste se k serveru, zadání určené databázi pomocí nové uživatelské jméno a heslo. Tento příklad ukazuje mysql příkazového řádku. Pomocí tohoto příkazu zobrazí se výzva k zadání hesla pro uživatelské jméno. Nahraďte váš vlastní název serveru, názvu databáze a uživatelské jméno.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Další postup
Otevření brány firewall pro IP adresy počítačů noví uživatelé jim připojení povolit: [vytvořit a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí portálu Azure](howto-manage-firewall-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md).

Další informace týkající se správy uživatelských účtů, najdete v dokumentaci k produktu MySQL [Správa uživatelských účtů](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [GRANT syntaxe](https://dev.mysql.com/doc/refman/5.7/en/grant.html), a [oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
