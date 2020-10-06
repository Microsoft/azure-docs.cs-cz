---
title: Vytváření databází a uživatelů – Azure Database for MySQL
description: Tento článek popisuje, jak vytvořit nové uživatelské účty pro interakci se serverem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766870"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Vytváření databází a uživatelů v Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Tento článek popisuje, jak vytvořit uživatele v Azure Database for MySQL.

> [!NOTE]
> **Komunikace bez posunu**
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na *podřízený*text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Slovo se v tomto článku používá kvůli konzistenci, protože se jedná o slovo, které se v současnosti zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>

Při prvním vytvoření serveru Azure Database for MySQL jste zadali uživatelské jméno a heslo správce serveru. Další informace najdete v tomto [rychlém](quickstart-create-mysql-server-database-using-azure-portal.md)startu. Uživatelské jméno správce serveru můžete určit v Azure Portal.

Uživatel s oprávněními správce serveru má tato oprávnění: 

   VYBRAT, VLOŽIT, AKTUALIZOVAT, ODSTRANIT, VYTVOŘIT, VYŘADIT, ZNOVU NAČÍST, ZPRACOVAT, ODKAZY, INDEXOVAT, ZMĚNIT, ZOBRAZIT DATABÁZE, VYTVOŘIT DOČASNÉ TABULKY, UZAMKNOUT TABULKY, SPUSTIT, REPLIKACE PODŘÍZENÁ, KLIENT REPLIKACE, VYTVOŘIT ZOBRAZENÍ, ZOBRAZIT ZOBRAZENÍ, VYTVOŘIT RUTINU, ZMĚNIT RUTINU, VYTVOŘIT UŽIVATELE, UDÁLOST, AKTIVAČNÍ UDÁLOST


Po vytvoření serveru Azure Database for MySQL můžete použít první účet správce serveru k vytvoření dalších uživatelů a udělení přístupu správcům. Účet správce serveru můžete použít také k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivým schématům databáze.

> [!NOTE]
> Role SUPER Privilege a DBA není podporovaná. Pokud chcete zjistit, co služba nepodporuje, přečtěte si [oprávnění](concepts-limits.md#privileges--data-manipulation-support) v článku omezení.
>
> Moduly plug-in `validate_password` pro hesla, jako `caching_sha2_password` jsou a nejsou podporovány službou.


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Vytvoření databáze s uživatelem bez role správce v Azure Database for MySQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal.

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je například MySQL Workbench, mysql.exe nebo HeidiSQL.
   
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [připojení a dotazování dat pro jeden server](./connect-workbench.md) nebo [připojení a dotazování dat pro flexibilní Server](./flexible-server/connect-workbench.md).

3. Upravte a spusťte následující kód SQL. Nahraďte hodnotu zástupného symbolu `db_user` vaším zamýšleným novým uživatelským jménem. Nahraďte hodnotu zástupného symbolu `testdb` názvem vaší databáze.

   Tento kód SQL vytvoří novou databázi s názvem TestDB. Potom vytvoří nového uživatele ve službě MySQL a udělí tomuto uživateli všechna oprávnění pro nové schéma databáze (TestDB \* ).

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Ověřte granty v databázi:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Přihlaste se k serveru, určete určenou databázi a použijte nové uživatelské jméno a heslo. Tento příklad ukazuje příkazový řádek MySQL. Když použijete tento příkaz, zobrazí se výzva k zadání hesla uživatele. Použijte vlastní název serveru, název databáze a uživatelské jméno.

   # <a name="single-server"></a>[Jeden server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[Flexibilní Server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>Vytvoření dalších uživatelů správce v Azure Database for MySQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal.

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je například MySQL Workbench, mysql.exe nebo HeidiSQL.
   
   Pokud si nejste jistí, jak se připojit, přečtěte si téma [použití aplikace MySQL Workbench pro připojení a dotazování dat](./connect-workbench.md).

3. Upravte a spusťte následující kód SQL. Nahraďte hodnotu zástupného symbolu `new_master_user` novým uživatelským jménem. Tato syntaxe uděluje uvedená oprávnění na všech schématech databáze (*.*) uživateli ( `new_master_user` v tomto příkladu).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Ověřte granty:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Všechny Azure Database for MySQL servery se vytvoří s uživatelem s názvem "azure_superuser". Toto je systémový účet vytvořený společností Microsoft za účelem správy serveru za účelem provádění monitorování, zálohování a jiné pravidelné údržby. Technici na vyžádání můžou tento účet použít taky k přístupu k serveru během incidentu s ověřováním certifikátů a musí požádat o přístup pomocí procesů JIT (just-in-time).

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, aby se mohly připojit:
- [Vytváření a Správa pravidel brány firewall na jednom serveru](howto-manage-firewall-using-portal.md) 
- [ Vytváření a Správa pravidel brány firewall na flexibilním serveru](flexible-server/how-to-connect-tls-ssl.md)

Další informace o správě uživatelských účtů najdete v dokumentaci k produktu MySQL pro [správu uživatelských účtů](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [udělení syntaxe](https://dev.mysql.com/doc/refman/5.7/en/grant.html)a [oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
