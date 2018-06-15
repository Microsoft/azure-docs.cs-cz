---
title: Připojení k Azure Database for PostgreSQL z Node.js
description: V tomto rychlém startu najdete vzorek kódu Node.js, který můžete použít k připojení a dotazování dat ze služby Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: ddccefd4d39de49809e5648454c8d9f50b34a7c5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29691939"
---
# <a name="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data"></a>Azure Database for PostgreSQL: Použití Node.js k připojení a dotazování dat
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for PostgreSQL pomocí aplikace [Node.js](https://nodejs.org/). Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Kroky v tomto článku předpokládají, že máte zkušenosti s vývojem pomocí Node.js a teprve začínáte pracovat se službou Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření databáze – portál](quickstart-create-server-database-portal.md)
- [Vytvoření databáze – rozhraní příkazového řádku](quickstart-create-server-database-azure-cli.md)

Budete také muset:
- Instalovat [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>Instalace klienta pg
Nainstalovat [pg](https://www.npmjs.com/package/pg), což je klient PostgreSQL pro Node.js.

Abyste to mohli udělat, spusťte správce balíčků pro uzly (npm) pro JavaScript z příkazového řádku, abyste instalovali klienta pg.
```bash
npm install pg
```

Ověřte instalaci tak, že vypíšete nainstalované balíčky.
```bash
npm list
```

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for PostgreSQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for PostgreSQL](./media/connect-nodejs/1-connection-string.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Spuštění kódu jazyka JavaScript v Node.js
Node.js můžete spouštět z prostředí Bash, Terminálu nebo příkazového řádku Windows tak, že zadáte `node` a potom interaktivně spustíte příklad kódu jazyka JavaScript tak, že ho zkopírujete a vložíte na příkazový řádek. Případně můžete kód jazyka JavaScript uložit do textového souboru a provést příkaz `node filename.js`, kdy název souboru se bude shodovat s parametrem, který ho spouští.

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat
Použijte následující kód k připojení a načtení dat pomocí příkazů **CREATE TABLE** a **INSERT INTO** jazyka SQL.
Objekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) slouží k vytvoření rozhraní pro server PostgreSQL. Funkce [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se používá k navázání připojení k serveru. Funkce [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se používá k provedení dotazu SQL na databázi PostgreSQL. 

Nahraďte parametry host (hostitel), dbname (název databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. Objekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) slouží k vytvoření rozhraní pro server PostgreSQL. Funkce [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se používá k navázání připojení k serveru. Funkce [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se používá k provedení dotazu SQL na databázi PostgreSQL. 

Nahraďte parametry host (hostitel), dbname (název databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření serveru a databáze. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Aktualizace dat
Použijte následující kód k připojení a čtení dat pomocí příkazu **UPDATE** jazyka SQL. Objekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) slouží k vytvoření rozhraní pro server PostgreSQL. Funkce [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se používá k navázání připojení k serveru. Funkce [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se používá k provedení dotazu SQL na databázi PostgreSQL. 

Nahraďte parametry host (hostitel), dbname (název databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření serveru a databáze. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL. Objekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) slouží k vytvoření rozhraní pro server PostgreSQL. Funkce [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se používá k navázání připojení k serveru. Funkce [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se používá k provedení dotazu SQL na databázi PostgreSQL. 

Nahraďte parametry host (hostitel), dbname (název databáze), user (uživatel) a password (heslo) hodnotami, které jste zadali při vytváření serveru a databáze. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
