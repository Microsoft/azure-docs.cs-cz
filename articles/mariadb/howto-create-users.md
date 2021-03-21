---
title: Vytvoření uživatelů – Azure Database for MariaDB
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci se serverem Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 28ec060e95d09cb150fc699919dde6cc0e1eaf23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663702"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Vytváření uživatelů ve službě Azure Database for MariaDB

Tento článek popisuje, jak můžete vytvářet uživatele v Azure Database for MariaDB.

Při prvním vytvoření Azure Database for MariaDB jste zadali přihlašovací uživatelské jméno a heslo správce serveru. Další informace najdete v [rychlém](quickstart-create-mariadb-server-database-using-azure-portal.md)startu. Přihlašovací uživatelské jméno správce serveru můžete najít z Azure Portal.

> [!NOTE]
> Tento článek obsahuje odkazy na _podřízený_ termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno v seznamu: vybrat, vložit, aktualizovat, odstranit, vytvořit, vyřadit, znovu načíst, zpracovat, odkazy, INDEXovat, změnit, Zobrazit databáze, vytvořit dočasné tabulky, zamknout tabulky, spustit, PODŘÍZENou PROCEDURu, klienta replikace, vytvořit zobrazení, zobrazit, vytvořit RUTINu, změnit RUTINu, vytvořit uživatele, událost

Po vytvoření serveru Azure Database for MariaDB můžete pomocí prvního uživatelského účtu správce serveru vytvořit další uživatele a udělit jim přístup správce. Účet správce serveru se taky dá použít k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivým schématům databáze.

> [!NOTE]
> Role SUPER Privilege a DBA nejsou podporované. Pokud chcete zjistit, co služba nepodporuje, přečtěte si [oprávnění](concepts-limits.md#privileges--data-manipulation-support) v článku omezení.<br><br>
> Moduly plug-in pro heslo, jako je například "validate_password" a "caching_sha2_password", nejsou službou podporovány.

## <a name="create-more-admin-users"></a>Vytvoření dalších uživatelů správců

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal.

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné.
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [použití aplikace MySQL Workbench pro připojení a dotazování dat](./connect-workbench.md) .

3. Upravte a spusťte následující kód SQL. Nahraďte nové uživatelské jméno pro hodnotu zástupného textu `new_master_user` . Tato syntaxe uděluje uvedená oprávnění na všech schématech databáze (*.*) k uživatelskému jménu (new_master_user v tomto příkladu). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Ověřte granty.

   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Vytvoření uživatelů databáze

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal. 

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné.
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [použití aplikace MySQL Workbench pro připojení a dotazování dat](./connect-workbench.md) .

3. Upravte a spusťte následující kód SQL. Nahraďte hodnotu zástupného symbolu `db_user` vaším zamýšleným novým uživatelským jménem a zástupnou hodnotou `testdb` s vlastním názvem databáze.

   Tato syntaxe kódu SQL vytvoří pro ukázkové účely novou databázi s názvem TestDB. Potom vytvoří nového uživatele ve službě Azure Database for MariaDB a udělí pro tohoto uživatele všechna oprávnění k novému schématu databáze (TestDB. \* ). 

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

5. Přihlaste se k serveru a určete určenou databázi pomocí nového uživatelského jména a hesla. Tento příklad ukazuje příkazový řádek MySQL. Pomocí tohoto příkazu budete vyzváni k zadání hesla pro uživatelské jméno. Nahraďte vlastní název serveru, název databáze a uživatelské jméno.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   Další informace o správě uživatelských účtů najdete v dokumentaci k MariaDB pro [správu uživatelských účtů](https://mariadb.com/kb/en/library/user-account-management/), [udělení syntaxe](https://mariadb.com/kb/en/library/grant/)a [oprávnění](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="azure_superuser"></a>azure_superuser

Všechny Azure Database for MySQL servery se vytvoří s uživatelem s názvem "azure_superuser". Toto je systémový účet vytvořený společností Microsoft za účelem správy serveru za účelem provádění monitorování, zálohování a jiné pravidelné údržby. Technici na vyžádání můžou tento účet použít taky k přístupu k serveru během incidentu s ověřováním certifikátů a musí požádat o přístup pomocí procesů JIT (just-in-time).

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, aby se mohly připojit: [vytváření a správa Azure Database for MariaDB pravidel brány firewall pomocí Azure Portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
