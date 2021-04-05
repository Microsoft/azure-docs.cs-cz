---
title: 'Rychlý Start: připojení pomocí Node.js-Azure Database for MySQL'
description: V tomto rychlém startu najdete několik vzorových kódů Node.js, které můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/11/2020
ms.openlocfilehash: 6a9134e13e3145daea1eed81c4aa8795a0a49950
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588229"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití Node.js k připojení a dotazování dat v Azure Database for MySQL

V tomto rychlém startu se připojíte k Azure Database for MySQL pomocí Node.js. Pak použijete příkazy SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi z platforem Mac, Ubuntu Linux a Windows. 

V tomto tématu se předpokládá, že máte zkušenosti s vývojem pomocí Node.js, ale začínáte pracovat s Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Server Azure Database for MySQL. [Vytvořte Azure Database for MySQL server pomocí Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [Vytvořte Azure Database for MySQL server pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Ujistěte se, že IP adresa, ze které se připojujete, přidala pravidla brány firewall serveru pomocí [Azure Portal](./howto-manage-firewall-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](./howto-manage-firewall-using-cli.md) .

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalace Node.js a konektoru MySQL

V závislosti na vaší platformě nainstalujte [Node.js](https://nodejs.org)podle pokynů v příslušné části. Pomocí npm nainstalujte balíček [MySQL](https://www.npmjs.com/package/mysql) a jeho závislosti do složky projektu.

### <a name="windows"></a>Windows

1. Přejděte na [stránku pro stažení Node.js](https://nodejs.org/en/download/) a vyberte požadovanou možnost Instalační služby systému Windows.
2. Vytvořte místní složku projektu, například `nodejsmysql`. 
3. Otevřete příkazový řádek a poté změňte adresář na složku projektu, například `cd c:\nodejsmysql\`
4. Spusťte nástroj NPM a nainstalujte knihovnu MySQL do složky projektu.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Ověřte instalaci tak, že zkontrolujete `npm list` Výstupní text. Číslo verze se může lišit v závislosti na vydávání nových oprav.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Spuštěním následujících příkazů nainstalujte **Node.js** a **npm** – správce balíčků pro Node.js.

   ```bash
    # Using Ubuntu
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Using Debian, as root
    curl -sL https://deb.nodesource.com/setup_14.x | bash -
    apt-get install -y nodejs
   ```

2. Spuštěním následujících příkazů vytvořte složku projektu `mysqlnodejs` a nainstalujte do ní balíček MySQL.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Ověřte instalaci tak, že zkontrolujete výstupní text seznamu npm. Číslo verze se může lišit v závislosti na vydávání nových oprav.

### <a name="macos"></a>macOS

1. Přejděte na [ stránkuNode.js ke stažení](https://nodejs.org/en/download/)a pak vyberte instalační program MacOS.

2. Spuštěním následujících příkazů vytvořte složku projektu `mysqlnodejs` a nainstalujte do ní balíček MySQL.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Ověřte instalaci tak, že zkontrolujete `npm list` Výstupní text. Číslo verze se může lišit v závislosti na vydávání nových oprav.

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo v Azure Portal vyberte **všechny prostředky** a potom vyhledejte server, který jste vytvořili (například **mydemoserver**).
3. Vyberte název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="Název serveru Azure Database for MySQL":::

## <a name="running-the-javascript-code-in-nodejs"></a>Spuštění kódu jazyka JavaScript v Node.js

1. Vložte kód jazyka JavaScript do textového souboru a ten uložte s příponou .js do složky projektu (například C:\nodejsmysql\createtable.js nebo /home/username/nodejsmysql/createtable.js).
2. Otevřete příkazový řádek nebo prostředí bash a pak změňte adresář na složku vašeho projektu `cd nodejsmysql` .
3. Chcete-li spustit aplikaci, zadejte příkaz Node následovaný názvem souboru, například `node createtable.js` .
4. Pokud v systému Windows není aplikace Node ve vaší proměnné prostředí PATH, možná bude nutné ke spuštění aplikace Node použít úplnou cestu, například `"C:\Program Files\nodejs\node.exe" createtable.js`.

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat

Pomocí následujícího kódu se připojte a načtěte data pomocí **Create Table** a  **vložte do** příkazů SQL.

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Funkce [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Funkce [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }
});

function queryDatabase(){
    conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
        if (err) throw err; 
        console.log('Dropped inventory table if existed.');
    })
        conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Created inventory table.');
    })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
        else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
    function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.end(function (err) { 
    if (err) throw err;
    else  console.log('Done.') 
    });
};
```

## <a name="read-data"></a>Čtení dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. Pole results slouží k ukládání výsledků dotazu.

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }
    });

function readData(){
    conn.query('SELECT * FROM inventory', 
        function (err, results, fields) {
            if (err) throw err;
            else console.log('Selected ' + results.length + ' row(s).');
            for (i = 0; i < results.length; i++) {
                console.log('Row: ' + JSON.stringify(results[i]));
            }
            console.log('Done.');
        })
    conn.end(
        function (err) { 
            if (err) throw err;
            else  console.log('Closing connection.') 
    });
};
```

## <a name="update-data"></a>Aktualizace dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **UPDATE** jazyka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Odstranění dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
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
> [Migrace vaší databáze pomocí exportu a importu](./concepts-migrate-import-export.md)
