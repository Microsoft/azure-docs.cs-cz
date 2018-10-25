---
title: Připojení k Azure Database for PostgreSQL pomocí jazyka Go
description: V tomto rychlém startu najdete ukázku programovacího jazyka Go, kterou můžete použít k připojení a dotazování dat ze služby Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: eef5d9b6878b46d402a91f3adbfd965c47493c25
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986473"
---
# <a name="azure-database-for-postgresql-use-go-language-to-connect-and-query-data"></a>Azure Database for PostgreSQL: Použití jazyka Go k připojení a dotazování dat
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for PostgreSQL pomocí kódu napsaného v jazyce [Go](https://golang.org/) (Golang). Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. V tomto článku se předpokládá, že máte zkušenosti s vývojem pomocí jazyka Go, ale teprve začínáte pracovat se službou Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření databáze – portál](quickstart-create-server-database-portal.md)
- [Vytvoření databáze – rozhraní příkazového řádku Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Instalace jazyka Go a konektoru pq
Nainstalujte na svém počítači jazyk [Go](https://golang.org/doc/install) a [ovladač Pure Go Postgres (pq)](https://github.com/lib/pq). V závislosti na vaší platformě postupujte podle příslušných kroků:

### <a name="windows"></a>Windows
1. [Stáhněte](https://golang.org/dl/) a nainstalujte jazyk Go pro Microsoft Windows podle [pokynů k instalaci](https://golang.org/doc/install).
2. Z nabídky Start spusťte příkazový řádek.
3. Vytvořte složku pro váš projekt, například `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Změňte adresář na složku projektu, například `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Nastavte proměnnou prostředí GOPATH tak, aby odkazovala na adresář se zdrojovým kódem. `set GOPATH=%USERPROFILE%\go`.
6. Nainstalujte [ovladač Pure Go Postgres (pq)](https://github.com/lib/pq) spuštěním příkazu `go get github.com/lib/pq`.

   Stručně řečeno, nainstalujte jazyk Go a potom na příkazovém řádku spusťte následující příkazy:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Spusťte prostředí Bash. 
2. Nainstalujte jazyk Go spuštěním příkazu `sudo apt-get install golang-go`.
3. Ve svém domovském adresáři vytvořte složku pro projekt, například `mkdir -p ~/go/src/postgresqlgo/`.
4. Změňte adresář na tuto složku, například `cd ~/go/src/postgresqlgo/`.
5. Nastavte proměnnou prostředí GOPATH tak, aby odkazovala na platný zdrojový adresář, jako je například aktuální složka go ve vašem domovském adresáři. Spuštěním příkazu `export GOPATH=~/go` v prostředí Bash přidejte adresář go jako hodnotu proměnné GOPATH pro aktuální relaci prostředí.
6. Nainstalujte [ovladač Pure Go Postgres (pq)](https://github.com/lib/pq) spuštěním příkazu `go get github.com/lib/pq`.

   Stručně řečeno, spusťte tyto příkazy Bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Stáhněte a nainstalujte jazyk Go podle [pokynů k instalaci](https://golang.org/doc/install) odpovídajících vaší platformě. 
2. Spusťte prostředí Bash. 
3. Ve svém domovském adresáři vytvořte složku pro projekt, například `mkdir -p ~/go/src/postgresqlgo/`.
4. Změňte adresář na tuto složku, například `cd ~/go/src/postgresqlgo/`.
5. Nastavte proměnnou prostředí GOPATH tak, aby odkazovala na platný zdrojový adresář, jako je například aktuální složka go ve vašem domovském adresáři. Spuštěním příkazu `export GOPATH=~/go` v prostředí Bash přidejte adresář go jako hodnotu proměnné GOPATH pro aktuální relaci prostředí.
6. Nainstalujte [ovladač Pure Go Postgres (pq)](https://github.com/lib/pq) spuštěním příkazu `go get github.com/lib/pq`.

   Stručně řečeno, nainstalujte jazyk Go a potom spusťte tyto příkazy Bash:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for PostgreSQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for PostgreSQL](./media/connect-go/1-connection-string.png)

## <a name="build-and-run-go-code"></a>Sestavení a spuštění kódu jazyka Go 
1. K psaní kódu jazyka Go můžete použít editor prostého textu, jako je Poznámkový blok v systému Microsoft Windows, [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) nebo [Nano](https://www.nano-editor.org/) v systému Ubuntu nebo TextEdit v systému macOS. Pokud dáváte přednost bohatšímu integrovanému vývojovému prostředí (IDE), vyzkoušejte [Gogland](https://www.jetbrains.com/go/) od JetBrains, [Visual Studio Code](https://code.visualstudio.com/) od Microsoftu nebo [Atom](https://atom.io/).
2. Kód jazyka Go z následujících částí vložte do textových souborů a tyto soubory uložte s příponou \*.go do složky vašeho projektu, například `%USERPROFILE%\go\src\postgresqlgo\createtable.go` (cesta ve Windows) nebo `~/go/src/postgresqlgo/createtable.go` (cesta v Linuxu).
3. V kódu vyhledejte konstanty `HOST`, `DATABASE`, `USER` a `PASSWORD` a příklady hodnot nahraďte vlastními hodnotami.  
4. Spusťte příkazový řádek nebo prostředí Bash. Změňte adresář na složku vašeho projektu. Například ve Windows pomocí příkazu `cd %USERPROFILE%\go\src\postgresqlgo\`. V Linuxu pomocí příkazu `cd ~/go/src/postgresqlgo/`. Některá ze zmíněných integrovaných vývojových prostředí (IDE) nabízejí možnosti ladění a modulu runtime, které nevyžadují příkazy prostředí.
5. Spusťte kód zadáním příkazu `go run createtable.go`, který aplikaci zkompiluje a spustí. 
6. Alternativně, pokud z kódu chcete sestavit nativní aplikaci, spusťte příkaz `go build createtable.go` a pak aplikaci spusťte pomocí souboru `createtable.exe`.

## <a name="connect-and-create-a-table"></a>Připojení a vytvoření tabulky
Pomocí následujícího kódu se připojte a vytvořte tabulku s využitím příkazu **CREATE TABLE** jazyka SQL, po kterém následují příkazy **INSERT INTO** jazyka SQL, které do tabulky přidají řádky.

Kód importuje tři balíčky: [balíček sql](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem PostgreSQL a [balíček fmt](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [sql.Open()](https://godoc.org/github.com/lib/pq#Open) pro připojení k databázi Azure Database for PostgreSQL a pomocí metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) zkontroluje stav připojení. Po celou dobu se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB), který uchovává fond připojení pro databázový server. Kód několikrát volá metodu [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) pro spuštění několika příkazů jazyka SQL. Pokaždé vlastní metoda checkError() zkontroluje, jestli nedošlo k chybě, a pokud k nějaké dojde, nouzově kód ukončí.

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
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

Kód importuje tři balíčky: [balíček sql](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem PostgreSQL a [balíček fmt](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [sql.Open()](https://godoc.org/github.com/lib/pq#Open) pro připojení k databázi Azure Database for PostgreSQL a pomocí metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) zkontroluje stav připojení. Po celou dobu se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB), který uchovává fond připojení pro databázový server. Dotaz SELECT se spustí zavoláním metody [db.Query()](https://golang.org/pkg/database/sql/#DB.Query) a výsledné řádky se uloží do proměnné typu [rows](https://golang.org/pkg/database/sql/#Rows). Kód čte hodnoty dat sloupců na aktuálním řádku pomocí metody [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) a ve smyčce prochází řádky pomocí iterátoru [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next), dokud nějaké řádky existují. Hodnoty sloupců každého řádku se vytisknou na výstup konzoly. Pokaždé se pomocí vlastní metody checkError() zkontroluje, jestli nedošlo k chybě, a pokud k nějaké dojde, kód se nouzově ukončí.

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

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL.

Kód importuje tři balíčky: [balíček sql](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem Postgres a [balíček fmt](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [sql.Open()](https://godoc.org/github.com/lib/pq#Open) pro připojení k databázi Azure Database for PostgreSQL a pomocí metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) zkontroluje stav připojení. Po celou dobu se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB), který uchovává fond připojení pro databázový server. Kód volá metodu [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) pro spuštění příkazu jazyka SQL, který aktualizuje tabulku. Pomocí vlastní metody checkError() se zkontroluje, jestli nedošlo k chybě, a pokud k nějaké dojde, kód se nouzově ukončí.

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

## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a odstraňte data s využitím příkazu **DELETE** jazyka SQL. 

Kód importuje tři balíčky: [balíček sql](https://golang.org/pkg/database/sql/), [balíček pq](https://godoc.org/github.com/lib/pq) jako ovladač pro komunikaci se serverem Postgres a [balíček fmt](https://golang.org/pkg/fmt/) pro tisk vstupů a výstupů na příkazovém řádku.

Kód volá metodu [sql.Open()](https://godoc.org/github.com/lib/pq#Open) pro připojení k databázi Azure Database for PostgreSQL a pomocí metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) zkontroluje stav připojení. Po celou dobu se používá [popisovač databáze](https://golang.org/pkg/database/sql/#DB), který uchovává fond připojení pro databázový server. Kód volá metodu [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) pro spuštění příkazu jazyka SQL, který odstraní řádek z tabulky. Pomocí vlastní metody checkError() se zkontroluje, jestli nedošlo k chybě, a pokud k nějaké dojde, kód se nouzově ukončí.

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
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
