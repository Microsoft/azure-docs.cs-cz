---
title: 'Rychlý Start: připojení MySQL Workbench – Azure Database for MariaDB'
description: V tomto rychlém startu najdete postup pro použití aplikace MySQL Workbench k připojení a dotazování dat ze služby Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 3/18/2020
ms.openlocfilehash: 209c793903f1f706fc27102a783aa76e65382fcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664380"
---
# <a name="quickstart-azure-database-for-mariadb-use-mysql-workbench-to-connect-and-query-data"></a>Rychlý Start: Azure Database for MariaDB: použití aplikace MySQL Workbench pro připojení a dotazování dat

Tento rychlý start ukazuje, jak se připojit k instanci Azure Database for MariaDB pomocí aplikace MySQL Workbench. 

## <a name="prerequisites"></a>Předpoklady

Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z následujících průvodců:

- [Vytvoření serveru Azure Database for MariaDB pomocí webu Azure Portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MariaDB pomocí Azure CLI](./quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Instalace aplikace MySQL Workbench

[Stáhněte si aplikaci MySQL Workbench](https://dev.mysql.com/downloads/workbench/) a nainstalujte ji do svého počítače.

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace potřebné pro připojení k instanci Azure Database for MariaDB. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky**. Vyhledejte server, který jste vytvořili (například **mydemoserver**).

3. Vyberte název serveru.

4. Na stránce **Přehled** serveru si poznamenejte hodnoty **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na této stránce také resetovat.

   ![Název serveru Azure Database for MariaDB a přihlašovací jméno správce serveru](./media/connect-workbench/1_server-overview-name-login.png)

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Připojení k serveru pomocí aplikace MySQL Workbench

Připojení k serveru Azure Database for MariaDB pomocí aplikace MySQL Workbench:

1. Na svém počítači otevřete MySQL Workbench. 

2. V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

   | Nastavení | Navrhovaná hodnota | Popis pole |
   |---|---|---|
   |   Název připojení | **Ukázkové připojení** | Zadejte popisek pro toto připojení. |
   | Způsob připojení | **Standardní (TCP/IP)** | Standard (TCP/IP) je dostačující. |
   | Název hostitele | *název serveru* | Zadejte hodnotu názvu serveru, kterou jste použili při vytváření instance Azure Database for MariaDB. Server v našem příkladu je **mydemoserver.mariadb.database.azure.com**. Použijte plně kvalifikovaný název domény (\*.mariadb.database.azure.com), jak je znázorněno v příkladu. Pokud si název vašeho serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části.  |
   | Port | **3306** | Při připojování ke službě Azure Database for MariaDB vždy používejte port 3306. |
   | Uživatelské jméno |  *přihlašovací jméno správce serveru* | Zadejte přihlašovací uživatelské jméno správce serveru, které jste použili při vytváření instance Azure Database for MariaDB. Naše ukázkové uživatelské jméno je **myadmin \@ mydemoserver**. Pokud si přihlašovací jméno správce serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. Formát je *UserName \@ servername*.
   | Heslo | *Vaše heslo* | Výběrem možnosti **Uložit v trezoru** heslo uložte. |

   ![Nastavení nového připojení](./media/connect-workbench/2-setup-new-connection.png)

3. Pokud chcete zkontrolovat, jestli jsou všechny parametry správně nakonfigurované, vyberte **Test připojení**. 

4. Vyberte **OK** a uložte připojení. 

5. V části **Připojení k MySQL** vyberte dlaždici odpovídající vašemu serveru. Počkejte na navázání připojení.

   Otevře se nová karta SQL s prázdným editorem, do kterého můžete zadávat dotazy.
    
   > [!NOTE]
   > Ve výchozím nastavení se na serveru Azure Database for MariaDB vyžaduje a vynucuje zabezpečení připojení protokolem SSL. Přestože aplikace MySQL Workbench obvykle pro připojení k serveru nevyžaduje žádnou další konfiguraci certifikátů SSL, doporučujeme vytvořit vazbu certifikace SSL CA s aplikací MySQL Workbench. Pokud potřebujete SSL zakázat, v nabídce na stránce přehledu serveru na webu Azure Portal vyberte **Zabezpečení připojení**. V části **Vynutit připojení SSL** vyberte **Zakázáno**.

## <a name="create-table-and-insert-read-update-and-delete-data"></a>Vytvoření tabulky a vložení, čtení, aktualizace a odstranění dat

1. Pro ilustraci nějakých ukázkových dat zkopírujte následující vzorový kód SQL a vložte ho na stránku prázdné karty SQL.

    Tento kód vytvoří prázdnou databázi **quickstartdb**. Potom vytvoří ukázkovou tabulku **inventory**. Kód vloží několik řádků a ty pak načte. Pomocí příkazu UPDATE data změní a pak znovu načte řádky. Nakonec kód jeden řádek odstraní a znovu načte řádky.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Snímek obrazovky ukazuje příklad kódu SQL v aplikaci MySQL Workbench a výstup po jeho spuštění:
    
    ![Výběr karty SQL aplikace MySQL Workbench pro spuštění vzorového kódu SQL](media/connect-workbench/3-workbench-sql-tab.png)

2. Pokud chcete spustit vzorový kód SQL, na panelu nástrojů na kartě **Soubor SQL** vyberte ikonu blesku.
3. Všimněte si výsledků na třech kartách v části **Mřížka výsledků** uprostřed stránky. 
4. Všimněte si seznamu **Výstup** v dolní části stránky. V něm se zobrazí stavy jednotlivých příkazů. 

V tomto rychlém startu jste se připojili ke službě Azure Database for MariaDB pomocí aplikace MySQL Workbench a s využitím jazyka SQL jste se dotázali na data.

<!--
## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./concepts-migrate-import-export.md)
-->
