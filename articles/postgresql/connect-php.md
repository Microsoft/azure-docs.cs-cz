---
title: 'Rychlý Start: připojení pomocí PHP-Azure Database for PostgreSQL-Single server'
description: V tomto rychlém startu najdete vzorový kód PHP, který můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL na jednom serveru.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 2/28/2018
ms.openlocfilehash: 200fdd126e2ed95804f81c1dd36804ecc6c61d85
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019682"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rychlý Start: použití PHP k připojení a dotazování dat v Azure Database for PostgreSQL-Single server

Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for PostgreSQL pomocí aplikace v [PHP](https://secure.php.net/manual/intro-whatis.php). Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Kroky v tomto článku předpokládají, že máte zkušenosti s vývojem pomocí PHP a teprve začínáte pracovat se službou Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start jako výchozí bod využívá prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření databáze – portál](quickstart-create-server-database-portal.md)
- [Vytvoření databáze – Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>Instalace PHP
Nainstalujte PHP na vlastní server nebo vytvořte [webovou aplikaci](../app-service/overview.md) Azure, která zahrnuje PHP.

### <a name="windows"></a>Windows
- Stáhněte [verzi PHP 7.1.4 Non-Thread Safe (x64)](https://windows.php.net/download#php-7.1).
- Nainstalujte PHP a další konfiguraci vyhledejte v [příručce k PHP](https://secure.php.net/manual/install.windows.php).
- Kód využívá třídu **pgsql** (ext/php_pgsql.dll), která je součástí instalace PHP. 
- Povolte rozšíření **pgsql** upravením konfiguračního souboru php.ini, který se obvykle nachází v umístění `C:\Program Files\PHP\v7.1\php.ini`. Konfigurační soubor by měl obsahovat řádek s textem `extension=php_pgsql.so`. Pokud se tento text nezobrazuje, přidejte ho a uložte soubor. Pokud se tam text nachází, ale je okomentovaný předponou středníku, odebráním středníku text odkomentujte.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Stáhněte [verzi PHP 7.1.4 Non-Thread Safe (x64)](https://secure.php.net/downloads.php). 
- Nainstalujte PHP a další konfiguraci vyhledejte v [příručce k PHP](https://secure.php.net/manual/install.unix.php).
- Kód využívá třídu **pgsql** (php_pgsql.so). Nainstalujte ji spuštěním příkazu `sudo apt-get install php-pgsql`.
- Povolte rozšíření **pgsql** upravením konfiguračního souboru `/etc/php/7.0/mods-available/pgsql.ini`. Konfigurační soubor by měl obsahovat řádek s textem `extension=php_pgsql.so`. Pokud se tento text nezobrazuje, přidejte ho a uložte soubor. Pokud se tam text nachází, ale je okomentovaný předponou středníku, odebráním středníku text odkomentujte.

### <a name="macos"></a>MacOS
- Stáhněte [verzi PHP 7.1.4](https://secure.php.net/downloads.php).
- Nainstalujte PHP a další konfiguraci vyhledejte v [příručce k PHP](https://secure.php.net/manual/install.macosx.php).

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for PostgreSQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-php/1-connection-string.png" alt-text="Název serveru Azure Database for PostgreSQL":::

## <a name="connect-and-create-a-table"></a>Připojení a vytvoření tabulky
Pomocí následujícího kódu se připojte a vytvořte tabulku s využitím příkazu **CREATE TABLE** jazyka SQL, po kterém následují příkazy **INSERT INTO** jazyka SQL, které do tabulky přidají řádky.

Metoda volání kódu [pg_connect ()](https://secure.php.net/manual/en/function.pg-connect.php) pro připojení k Azure Database for PostgreSQL. Potom několikrát volá metodu [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) pro spuštění několika příkazů a pokaždé, když dojde k chybě, volá metodu [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) pro kontrolu podrobností. Potom volá metodu [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) pro ukončení připojení.

Nahraďte parametry `$host`, `$database`, `$user` a `$password` vlastními hodnotami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

 Metoda volání kódu [pg_connect ()](https://secure.php.net/manual/en/function.pg-connect.php) pro připojení k Azure Database for PostgreSQL. Potom volá metodu [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) pro spuštění příkazu SELECT, výsledky uloží v sadě výsledků dotazu a v případě, že dojde k chybě, volá metodu [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) pro kontrolu podrobností.  Pro účely čtení sady výsledků dotazu se ve smyčce pro každý řádek jednou volá metoda [pg_fetch_row()](https://secure.php.net/manual/en/function.pg-fetch-row.php), a data řádku se načítají v poli `$row`, kde je na každé pozici pole jedna hodnota dat na sloupec.  Pro uvolnění sady výsledků dotazu se volá metoda [pg_free_result()](https://secure.php.net/manual/en/function.pg-free-result.php). Potom volá metodu [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) pro ukončení připojení.

Nahraďte parametry `$host`, `$database`, `$user` a `$password` vlastními hodnotami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL.

Metoda volání kódu [pg_connect ()](https://secure.php.net/manual/en/function.pg-connect.php) pro připojení k Azure Database for PostgreSQL. Potom volá metodu [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) pro spuštění příkazu a v případě, že dojde k chybě, volá metodu [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) pro kontrolu podrobností. Potom volá metodu [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) pro ukončení připojení.

Nahraďte parametry `$host`, `$database`, `$user` a `$password` vlastními hodnotami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL. 

 Kód volá metodu [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) pro připojení ke službě Azure Database for PostgreSQL. Potom volá metodu [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) pro spuštění příkazu a v případě, že dojde k chybě, volá metodu [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) pro kontrolu podrobností. Potom volá metodu [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) pro ukončení připojení.

Nahraďte parametry `$host`, `$database`, `$user` a `$password` vlastními hodnotami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
