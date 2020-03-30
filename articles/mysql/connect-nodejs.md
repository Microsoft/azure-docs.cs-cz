---
title: Připojení pomocí souboru Node.js – databáze Azure pro MySQL
description: V tomto rychlém startu najdete několik vzorových kódů Node.js, které můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 3/18/2020
ms.openlocfilehash: a8c4f84fe958c1b2762509432596fea772e39d7e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067939"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Úvodní příručka: Použití souboru Node.js k připojení a dotazování na data v Azure Database for MySQL

V tomto rychlém startu se připojíte k databázi Azure pro MySQL pomocí Node.js. Potom pomocí příkazů SQL můžete dotazovat, vkládat, aktualizovat a odstraňovat data v databázi z platforem Mac, Ubuntu Linux a Windows. 

Toto téma předpokládá, že jste obeznámeni s vývojem pomocí Node.js, ale jste na práci s Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Databáze Azure pro server MySQL. [Vytvořte databázi Azure pro server MySQL pomocí portálu Azure](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [vytvořte azure databázi pro server MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalace Node.js a konektoru MySQL

V závislosti na platformě nainstalujte soubor [Node.js](https://nodejs.org)podle pokynů v příslušné části . Použijte npm k instalaci balíčku [mysql](https://www.npmjs.com/package/mysql) a jeho závislostí do složky projektu.

### <a name="windows"></a>**Windows**

1. Přejděte na [stránku pro stažení Node.js](https://nodejs.org/en/download/) a vyberte požadovanou možnost Instalační služby systému Windows.
2. Vytvořte místní složku projektu, například `nodejsmysql`. 
3. Otevřete příkazový řádek a změňte adresář do složky projektu, například`cd c:\nodejsmysql\`
4. Spusťte nástroj NPM a nainstalujte knihovnu mysql do složky projektu.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Zkontrolujte instalaci kontrolou `npm list` výstupního textu. Číslo verze se může lišit v závislosti na vydávání nových oprav.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

1. Spuštěním následujících příkazů nainstalujte **Node.js** a **npm** – správce balíčků pro Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Spusťte následující příkazy k `mysqlnodejs` vytvoření složky projektu a nainstalujte balíček mysql do této složky.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Ověřte instalaci kontrolou výstupního textu seznamu npm. Číslo verze se může lišit v závislosti na vydávání nových oprav.

### <a name="mac-os"></a>**Mac OS**

1. Zadejte následující příkazy, abyste nainstalovali **brew**, snadno použitelného správce balíčků pro Mac OS X a **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Spusťte následující příkazy k `mysqlnodejs` vytvoření složky projektu a nainstalujte balíček mysql do této složky.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Zkontrolujte instalaci kontrolou `npm list` výstupního textu. Číslo verze se může lišit v závislosti na vydávání nových oprav.

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky**a vyhledejte server, který jste vytvořili (například **mydemoserver).**
3. Vyberte název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for MySQL](./media/connect-nodejs/server-name-azure-database-mysql.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Spuštění kódu jazyka JavaScript v Node.js

1. Vložte kód jazyka JavaScript do textového souboru a ten uložte s příponou .js do složky projektu (například C:\nodejsmysql\createtable.js nebo /home/username/nodejsmysql/createtable.js).
2. Otevřete příkazový řádek nebo prostředí bash a `cd nodejsmysql`změňte adresář do složky projektu .
3. Chcete-li aplikaci spustit, zadejte příkaz uzlu následovaný `node createtable.js`názvem souboru, například .
4. Pokud v systému Windows není aplikace Node ve vaší proměnné prostředí PATH, možná bude nutné ke spuštění aplikace Node použít úplnou cestu, například `"C:\Program Files\nodejs\node.exe" createtable.js`.

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat

Pomocí následujícího kódu připojte a načtěte data pomocí **příkazů CREATE TABLE** a **INSERT INTO** SQL.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./concepts-migrate-import-export.md)
