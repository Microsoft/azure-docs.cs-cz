---
title: Vytváření databází a uživatelů – Azure Database for MySQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci se serverem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: ed653ffb6fc24a75170d51d345c0c64724ff90f1
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651017"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql-server"></a>Vytvoření databází a uživatelů na serveru Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Tento článek popisuje, jak můžete vytvářet uživatele na serveru Azure Database for MySQL.

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _podřízený_text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Toto slovo se v tomto článku používá kvůli konzistenci, protože je aktuálně slovo, které se zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>

Při prvním vytvoření Azure Database for MySQL jste zadali přihlašovací uživatelské jméno a heslo správce serveru. Další informace najdete v [rychlém](quickstart-create-mysql-server-database-using-azure-portal.md)startu. Přihlašovací uživatelské jméno správce serveru můžete najít z Azure Portal.

Uživatel správce serveru získá určitá oprávnění pro váš server, jak je uvedeno níže: 

   VYBRAT, VLOŽIT, AKTUALIZOVAT, ODSTRANIT, VYTVOŘIT, VYŘADIT, ZNOVU NAČÍST, ZPRACOVAT, ODKAZY, INDEXOVAT, ZMĚNIT, ZOBRAZIT DATABÁZE, VYTVOŘIT DOČASNÉ TABULKY, UZAMKNOUT TABULKY, SPUSTIT, REPLIKACE PODŘÍZENÁ, KLIENT REPLIKACE, VYTVOŘIT ZOBRAZENÍ, ZOBRAZIT ZOBRAZENÍ, VYTVOŘIT RUTINU, ZMĚNIT RUTINU, VYTVOŘIT UŽIVATELE, UDÁLOST, AKTIVAČNÍ UDÁLOST


Po vytvoření serveru Azure Database for MySQL můžete pomocí prvního uživatelského účtu správce serveru vytvořit další uživatele a udělit jim přístup správce. Účet správce serveru se taky dá použít k vytvoření méně privilegovaných uživatelů, kteří mají přístup k jednotlivým schématům databáze.

> [!NOTE]
> Role SUPER Privilege a DBA nejsou podporované. Pokud chcete zjistit, co služba nepodporuje, přečtěte si [oprávnění](concepts-limits.md#privileges--data-manipulation-support) v článku omezení.<br><br>
> Moduly plug-in pro heslo, jako je například "validate_password" a "caching_sha2_password", nejsou službou podporovány.

## <a name="how-to-create-database-with-non-admin-user-in-azure-database-for-mysql"></a>Vytvoření databáze s uživatelem bez správce v Azure Database for MySQL

1. Získejte informace o připojení a uživatelské jméno správce.
   Pokud se chcete připojit ke svému databázovému serveru, potřebujete úplný název serveru a přihlašovací údaje správce. Název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru nebo na stránce **vlastnosti** v Azure Portal.

2. K připojení k databázovému serveru použijte účet správce a heslo. Použijte preferovaný klientský nástroj, jako je například MySQL Workbench, mysql.exe, HeidiSQL nebo jiné.
   Pokud si nejste jistí, jak se připojit, přečtěte si téma jak použít MySQL Workbench k [připojení a dotazování dat pro jeden server](./connect-workbench.md) nebo [připojení a dotazování dat pro flexibilní Server](./flexible-server/connect-workbench.md) .

3. Upravte a spusťte následující kód SQL. Nahraďte hodnotu zástupného symbolu `db_user` vaším zamýšleným novým uživatelským jménem a zástupnou hodnotou `testdb` s vlastním názvem databáze.

   Tato syntaxe kódu SQL vytvoří pro ukázkové účely novou databázi s názvem TestDB. Potom vytvoří nového uživatele ve službě MySQL a udělí všem oprávněním novému schématu databáze (TestDB. \* ) pro tohoto uživatele.

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

# <a name="single-server"></a>[Jeden server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
# <a name="flexible-server"></a>[Flexibilní server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Vytvoření dalších uživatelů s oprávněními správce v Azure Database for MySQL

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

4. Ověřit granty

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
