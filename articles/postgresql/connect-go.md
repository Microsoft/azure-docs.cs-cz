---
title: Použití jazyka přejít pro připojení k Azure Database for PostgreSQL – jeden server
description: V tomto rychlém startu najdete ukázku programovacího jazyka na cestách, kterou můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL na jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 645d34961fb735542729091719dd55c42436db95
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244477"
---
# <a name="azure-database-for-postgresql---single-server-use-go-language-to-connect-and-query-data"></a>Azure Database for PostgreSQL – jeden server: připojení a dotazování dat pomocí jazyka přejít
Tento rychlý Start ukazuje, jak se připojit k Azure Database for PostgreSQL pomocí kódu napsaného v jazyce [Přejít](https://golang.org/) (golang). Ukazuje, jak používat příkazy SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi. V tomto článku se předpokládá, že máte zkušenosti s vývojem pomocí jazyka, ale začínáte pracovat s Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadované součásti
Tento rychlý Start používá jako výchozí bod prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření databáze – portál](quickstart-create-server-database-portal.md)
- [Vytvoření databáze – Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Nainstalovat konektor přejít a pq
Na svém počítači nainstalujte [Přejít](https://golang.org/doc/install) a [ovladač čistého přechodu na Postgres (pq)](https://github.com/lib/pq) . V závislosti na vaší platformě postupujte podle příslušných kroků:

### <a name="windows"></a>Windows
1. [Stáhněte si](https://golang.org/dl/) a nainstalujte systém Microsoft Windows, a to podle [pokynů k instalaci](https://golang.org/doc/install).
2. Spusťte příkazový řádek z nabídky Start.
3. Vytvořte složku pro váš projekt, například `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Změňte adresář na složku projektu, například `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Nastavte proměnnou prostředí pro GOPATH tak tak, aby odkazovala na adresář zdrojového kódu. `set GOPATH=%USERPROFILE%\go`.
6. Nainstalujte [ovladač Pure. Postgres (pq)](https://github.com/lib/pq) spuštěním příkazu `go get github.com/lib/pq`.

   V části Souhrn nainstalujte příkaz Přejít a potom spusťte tyto příkazy v příkazovém řádku:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Spusťte prostředí bash. 
2. Nainstalujte příkaz Přejít spuštěním `sudo apt-get install golang-go`.
3. Vytvořte složku pro váš projekt v domovském adresáři, například `mkdir -p ~/go/src/postgresqlgo/`.
4. Změňte adresář na složku, například `cd ~/go/src/postgresqlgo/`.
5. Nastavte proměnnou prostředí GOPATH tak tak, aby odkazovala na platný zdrojový adresář, jako je například složka přejít do aktuálního domovského adresáře. V prostředí bash spusťte `export GOPATH=~/go` a přidejte tak adresář přejít jako GOPATH tak pro aktuální relaci prostředí.
6. Nainstalujte [ovladač Pure. Postgres (pq)](https://github.com/lib/pq) spuštěním příkazu `go get github.com/lib/pq`.

   V souhrnu spusťte tyto příkazy bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Stáhněte a nainstalujte přejít podle pokynů k [instalaci](https://golang.org/doc/install) , které odpovídají vaší platformě. 
2. Spusťte prostředí bash. 
3. Vytvořte složku pro váš projekt v domovském adresáři, například `mkdir -p ~/go/src/postgresqlgo/`.
4. Změňte adresář na složku, například `cd ~/go/src/postgresqlgo/`.
5. Nastavte proměnnou prostředí GOPATH tak tak, aby odkazovala na platný zdrojový adresář, jako je například složka přejít do aktuálního domovského adresáře. V prostředí bash spusťte `export GOPATH=~/go` a přidejte tak adresář přejít jako GOPATH tak pro aktuální relaci prostředí.
6. Nainstalujte [ovladač Pure. Postgres (pq)](https://github.com/lib/pq) spuštěním příkazu `go get github.com/lib/pq`.

   V souhrnu, nainstalujte příkaz Přejít a potom spusťte tyto příkazy bash:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Získat informace o připojení
Získejte informace o připojení potřebné pro připojení k Azure Database for PostgreSQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje pro přihlášení.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce na levé straně Azure Portal klikněte na **všechny prostředky**a vyhledejte server, který jste vytvořili (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **název serveru** a **přihlašovací jméno správce serveru**. Pokud zapomenete heslo, můžete také resetovat heslo z tohoto panelu.
 @no__t – databáze 0Azure pro název serveru PostgreSQL @ no__t-1

## <a name="build-and-run-go-code"></a>Sestavit a spustit kód přechodu 
1. K psaní kódu golang můžete použít Editor prostého textu, jako je Poznámkový blok v Microsoft Windows, [VI](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) nebo [nano](https://www.nano-editor.org/) v Ubuntu nebo TextEdit v MacOS. Pokud dáváte přednost rozsáhlému interaktivnímu vývojovému prostředí (IDE), [Visual Studio Code](https://code.visualstudio.com/) [od společnosti](https://www.jetbrains.com/go/) Microsoft nebo [Atom](https://atom.io/).
2. Vložte kód golang z následujících částí do textových souborů a uložte do složky projektu s příponou souboru @no__t -0. přejít, například cesta Windows `%USERPROFILE%\go\src\postgresqlgo\createtable.go` nebo Linux @no__t cesta-2.
3. Vyhledejte v kódu konstanty `HOST`, `DATABASE`, `USER` a `PASSWORD` a nahraďte příklady hodnot vlastními hodnotami.  
4. Spusťte příkazový řádek nebo prostředí bash. Změňte adresář na složku vašeho projektu. Například ve Windows `cd %USERPROFILE%\go\src\postgresqlgo\`. V systému Linux `cd ~/go/src/postgresqlgo/`. Některá z výše uvedených prostředí IDE nabízejí možnosti ladění a běhu bez nutnosti příkazů prostředí.
5. Spusťte kód zadáním příkazu `go run createtable.go` pro zkompilování aplikace a spuštění. 
6. Případně pro sestavení kódu do nativní aplikace `go build createtable.go`, potom spusťte `createtable.exe` pro spuštění aplikace.

## <a name="connect-and-create-a-table"></a>Připojit a vytvořit tabulku
Pomocí následujícího kódu se připojte a vytvořte tabulku pomocí příkazu **Create Table** SQL, po kterém následují příkazy **INSERT INTO** jazyka SQL, které do tabulky přidají řádky.

Kód importuje tři balíčky: [balíček SQL](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem PostgreSQL a [balíček FMT](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [SQL. Otevřete ()](https://godoc.org/github.com/lib/pq#Open) pro připojení k Azure Database for PostgreSQL databázi a zkontrolujte připojení pomocí metody [DB. Otestujete test ()](https://golang.org/pkg/database/sql/#DB.Ping). V celém serveru se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB) , který uchovává fond připojení pro databázový server. Kód několikrát volá metodu [exec ()](https://golang.org/pkg/database/sql/#DB.Exec) pro spuštění několika příkazů SQL. Pokaždé, když vlastní metoda metody checkError () zkontroluje, jestli došlo k chybě, a nenouzově se ukončí, pokud dojde k chybě.

Nahraďte parametry `HOST`, `DATABASE`, `USER` a `PASSWORD` vlastními hodnotami. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **Select** jazyka SQL. 

Kód importuje tři balíčky: [balíček SQL](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem PostgreSQL a [balíček FMT](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [SQL. Otevřete ()](https://godoc.org/github.com/lib/pq#Open) pro připojení k Azure Database for PostgreSQL databázi a zkontrolujte připojení pomocí metody [DB. Otestujete test ()](https://golang.org/pkg/database/sql/#DB.Ping). V celém serveru se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB) , který uchovává fond připojení pro databázový server. Dotaz SELECT se spustí voláním metody [DB. Dotaz ()](https://golang.org/pkg/database/sql/#DB.Query)a výsledné řádky jsou uchovávány v proměnné typu [řádků](https://golang.org/pkg/database/sql/#Rows). Kód přečte hodnoty dat sloupce v aktuálním řádku pomocí [řádků metody. Naskenujte ()](https://golang.org/pkg/database/sql/#Rows.Scan) a cykly přes řádky pomocí řádků iterátoru [. Next ()](https://golang.org/pkg/database/sql/#Rows.Next) , dokud neexistují žádné další řádky. Hodnoty ve sloupcích každého řádku jsou vytištěny na konzolu. Pokaždé, když se použije vlastní metoda metody checkError () ke kontrole, jestli došlo k chybě, a při výskytu chyby se nejedná o nouzové ukončení.

Nahraďte parametry `HOST`, `DATABASE`, `USER` a `PASSWORD` vlastními hodnotami. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)
    defer rows.Close()

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Aktualizovat data
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **Update** jazyka SQL.

Kód importuje tři balíčky: [balíček SQL](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem Postgres a [balíček FMT](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [SQL. Otevřete ()](https://godoc.org/github.com/lib/pq#Open) pro připojení k Azure Database for PostgreSQL databázi a zkontrolujte připojení pomocí metody [DB. Otestujete test ()](https://golang.org/pkg/database/sql/#DB.Ping). V celém serveru se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB) , který uchovává fond připojení pro databázový server. Kód volá metodu [exec ()](https://golang.org/pkg/database/sql/#DB.Exec) pro spuštění příkazu jazyka SQL, který aktualizuje tabulku. Vlastní metoda metody checkError () slouží ke kontrole, zda došlo k chybě a je nenouzové ukončit, pokud dojde k chybě.

Nahraďte parametry `HOST`, `DATABASE`, `USER` a `PASSWORD` vlastními hodnotami. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Odstranit data
Pomocí následujícího kódu se připojte a odstraňte data s využitím příkazu **Delete** jazyka SQL. 

Kód importuje tři balíčky: [balíček SQL](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem Postgres a [balíček FMT](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [SQL. Otevřete ()](https://godoc.org/github.com/lib/pq#Open) pro připojení k Azure Database for PostgreSQL databázi a zkontrolujte připojení pomocí metody [DB. Otestujete test ()](https://golang.org/pkg/database/sql/#DB.Ping). V celém serveru se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB) , který uchovává fond připojení pro databázový server. Kód volá metodu [exec ()](https://golang.org/pkg/database/sql/#DB.Exec) pro spuštění příkazu jazyka SQL, který odstraní řádek z tabulky. Vlastní metoda metody checkError () slouží ke kontrole, zda došlo k chybě a je nenouzové ukončit, pokud dojde k chybě.

Nahraďte parametry `HOST`, `DATABASE`, `USER` a `PASSWORD` vlastními hodnotami. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
