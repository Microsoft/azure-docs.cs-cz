---
title: 'Rychlý Start: připojení pomocí Rust-Azure Database for PostgreSQL-Single server'
description: V tomto rychlém startu najdete ukázky kódu Rust, které můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL na jednom serveru.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505848"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rychlý Start: použití Rust k připojení a dotazování dat v Azure Database for PostgreSQL – jeden server

V tomto článku se dozvíte, jak pomocí [ovladače PostgreSQL pro Rust](https://github.com/sfackler/rust-postgres) pracovat s Azure Database for PostgreSQL prozkoumávání operací CRUD (vytváření, čtení, aktualizace a odstraňování) implementovaných v ukázkovém kódu. Nakonec můžete aplikaci spustit místně, abyste ji viděli v akci.

## <a name="prerequisites"></a>Požadavky
Pro tento rychlý Start budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- Je nainstalovaná nejnovější verze [Rust](https://www.rust-lang.org/tools/install) .
- Azure Database for PostgreSQL jeden server – vytvořte ho pomocí [Azure Portal](./quickstart-create-server-database-portal.md) <br/> nebo [Azure CLI](./quickstart-create-server-database-azure-cli.md).
- Na základě toho, jestli používáte veřejný nebo privátní přístup, proveďte **jednu** z následujících akcí, aby se povolilo připojení.

  |Akce| Metoda připojení|Praktičtí průvodci|
  |:--------- |:--------- |:--------- |
  | **Konfigurace pravidel brány firewall** | Veřejná | [Azure Portal](./howto-manage-firewall-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-firewall-using-cli.md)|
  | **Konfigurace koncového bodu služby** | Veřejná | [Azure Portal](./howto-manage-vnet-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-vnet-using-cli.md)|
  | **Konfigurace privátního odkazu** | Privátní | [Azure Portal](./howto-configure-privatelink-portal.md) <br/> [Rozhraní příkazového řádku](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) je nainstalovaný.

## <a name="get-database-connection-information"></a>Získat informace o připojení databáze
Připojení k databázi Azure Database for PostgreSQL vyžaduje plně kvalifikovaný název serveru a přihlašovací údaje. Tyto informace můžete získat z Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte název vašeho Azure Database for PostgreSQL serveru.
1. Na stránce **Přehled** serveru zkopírujte plně kvalifikovaný **název serveru** a **uživatelské jméno správce**. Plně kvalifikovaný **název serveru** je vždy ve tvaru *\<my-server-name> . Postgres.Database.Azure.com* a **uživatelské jméno správce** je vždy ve tvaru *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Kontrola kódu (volitelné)

Pokud vás zajímá, jak kód funguje, můžete zkontrolovat následující fragmenty kódu. V opačném případě můžete bez obav přeskočit ke [spuštění aplikace](#run-the-application).

### <a name="connect"></a>Připojit

`main`Funkce se spustí připojením k Azure Database for PostgreSQL a závisí na následujících proměnných prostředí pro informace o připojení `POSTGRES_HOST` , `POSTGRES_USER` , `POSTGRES_PASSWORD` a `POSTGRES_DBNAME` . Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurovaná tak, aby vyžadovala `TLS` připojení. Můžete zakázat vyžadování, `TLS` Pokud klientská aplikace nepodporuje `TLS` připojení. Podrobnosti najdete [v tématu Konfigurace připojení TLS v Azure Database for PostgreSQL-Single server](./concepts-ssl-connection-security.md).

Ukázková aplikace v tomto článku používá TLS pomocí programu [Postgres-OpenSSL](https://crates.io/crates/postgres-openssl/). [Postgres:: Client:: Connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) slouží k inicializaci připojení a program se ukončí v případě, že dojde k chybě.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Vyřazení a vytvoření tabulky

Ukázková aplikace používá jednoduchou `inventory` tabulku k předvedení operací CRUD (vytváření, čtení, aktualizace, odstranění).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

`drop_create_table`Funkce se zpočátku pokusí `DROP` `inventory` tabulku vytvořit novou. To usnadňuje učení a experimentování, protože vždy začnete se známým (čistým) stavem. Metoda [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) se používá pro operace Create a drop. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Vložení dat

`insert_data` Přidá položky do `inventory` tabulky. Vytvoří [připravený příkaz](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) s funkcí [Prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) . 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Všimněte si také použití metody [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) , která umožňuje explicitně zadat typy parametrů dotazu.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Nakonec se `for` smyčka používá k přidání `item-3` , `item-4` a `item-5` s náhodně vygenerovaným množstvím pro každý z nich.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Dotazování dat

`query_data` funkce ukazuje, jak načíst data z `inventory` tabulky. Metoda [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) slouží k získání položky podle jejího `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Všechny řádky v tabulce inventáře se načítají pomocí `select * from` dotazu s metodou [dotazu](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) . Vrácené řádky jsou iterované za účelem extrakce hodnoty pro každý sloupec pomocí [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Všimněte si `get` , jak je možné zadat sloupec podle jeho číselného indexu na řádku nebo podle jeho názvu sloupce.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Aktualizace dat

`update_date`Funkce náhodně aktualizuje množství pro všechny položky. Vzhledem k tomu `insert_data` , že funkce přidala `5` řádky, je stejná jako v úvahu ve `for` smyčce – `for n in 1..=5`

> [!TIP]
> Všimněte si, že používáme `query` místo toho, `execute` od kterého máme v úmyslu získat zpátky `id` a nově vygenerovanou `quantity` klauzuli (pomocí [klauzule vracení](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Odstranění dat

Nakonec `delete` funkce ukazuje, jak odebrat položku z `inventory` tabulky pomocí jejího `id` . `id`Je vybráno náhodně – jedná se o náhodné celé číslo `1` mezi `5` ( `5` včetně), protože `insert_data` funkce přidala `5` řádky, které mají začít. 

> [!TIP]
> Všimněte si, že `query` místo toho, co se chystáme `execute` získat zpět informace o položce, kterou jsme právě odstranili (pomocí [klauzule vracení](https://www.postgresql.org/docs/current/dml-returning.html)), se používáme.

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Spuštění aplikace

1. Pokud chcete začít, spusťte následující příkaz, který naklonuje ukázkové úložiště:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Nastavte požadované proměnné prostředí s hodnotami, které jste zkopírovali z Azure Portal:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Chcete-li spustit aplikaci, přejděte do adresáře, do kterého jste naklonováni a spusťte `cargo run` :

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Měl by se zobrazit výstup podobný tomuto:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Pokud to chcete potvrdit, můžete se také připojit k Azure Database for PostgreSQL [pomocí psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) a spouštět dotazy na databázi, například:

    ```sql
    select * from inventory;
    ```

[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Správa serveru Azure Database for PostgreSQL pomocí portálu](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Správa serveru Azure Database for PostgreSQL pomocí rozhraní příkazového řádku](./how-to-manage-server-cli.md)<br/>

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)
