---
title: Připojení aplikace Rust k rozhraní API služby Azure Cosmos DB pro MongoDB
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci Rust s rozhraním API Azure Cosmos DB pro MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: b5dbb8498157096c8e3178175f827eb47591c9de
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659915"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Rychlý Start: připojení aplikace v Rust k rozhraní API služby Azure Cosmos DB pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování. Ukázka uvedená v tomto článku představuje jednoduchou aplikaci založenou na příkazovém řádku, která používá [ovladač Rust pro MongoDB](https://github.com/mongodb/mongo-rust-driver). Azure Cosmos DB vzhledem k tomu, že rozhraní API pro MongoDB je [kompatibilní s MongoDB přenosovým protokolem](./mongodb-introduction.md#wire-protocol-compatibility), je možné, že se k němu připojí kterýkoli klientský ovladač MongoDB.

Naučíte se, jak pomocí ovladače MongoDB Rust komunikovat s rozhraním API Azure Cosmos DB pro MongoDB zkoumáním operací CRUD (vytváření, čtení, aktualizace a odstraňování) implementovaných v ukázkovém kódu. Nakonec můžete aplikaci spustit místně, abyste ji viděli v akci.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) spolu s připojovacím řetězcem `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Rust](https://www.rust-lang.org/tools/install) (verze 1,39 nebo vyšší)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Nastavit Azure Cosmos DB

Pokud chcete nastavit účet Azure Cosmos DB, postupujte podle [pokynů uvedených tady](create-mongodb-dotnet.md). Aplikace bude potřebovat připojovací řetězec MongoDB, který můžete načíst pomocí Azure Portal. Podrobnosti najdete v tématu věnovaném [získání připojovacího řetězce MongoDB k přizpůsobení](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Spuštění aplikace

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Spuštěním následujících příkazů naklonujte ukázkové úložiště.

1. Otevřete příkazový řádek, vytvořte novou složku s názvem `git-samples` a pak zavřete příkazový řádek.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Sestavení aplikace

Sestavení binárního souboru:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Konfigurace aplikace 

Exportujte připojovací řetězec, databázi MongoDB a názvy kolekcí jako proměnné prostředí. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true`Možnost je důležitá z důvodu Cosmos DB požadavků. Další informace najdete v tématu [požadavky na připojovací řetězec](connect-mongodb-account.md#connection-string-requirements).
>

Pro `MONGODB_URL` proměnnou prostředí Nahraďte zástupné symboly pro `<COSMOSDB_ACCOUNT_NAME>` a. `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: Název účtu Azure Cosmos DB, který jste vytvořili.
- `<COSMOSDB_PASSWORD>`: Klíč databáze extrahovaný v předchozím kroku

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Můžete zvolit preferované hodnoty pro `MONGODB_DATABASE` a `MONGODB_COLLECTION` nebo je nechat.

Chcete-li spustit aplikaci, přejděte do správné složky (kde existuje binární soubor aplikace):

```bash
cd target/release
```

Vytvoření `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

V případě úspěchu by se měl zobrazit výstup s MongoDB `_id` nově vytvořeného dokumentu:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Vytvořit další `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Zobrazit seznam `todo` s

```bash
./todo list all
```

Měli byste vidět ty, které jste právě přidali:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Chcete-li aktualizovat stav `todo` (například změnit na `completed` stav), použijte `todo` ID jako:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Vypsat pouze dokončené `todo` s

```bash
./todo list completed
```

Měla by se zobrazit ta, kterou jste právě aktualizovali.

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Odstranit `todo` pomocí ID IT

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Výpis `todo` s potvrzením

```bash
./todo list all
```

`todo`Právě odstraněné by neměl být přítomen.

### <a name="view-data-in-data-explorer"></a>Zobrazení dat v Průzkumníku dat

Data uložená v Azure Cosmos DB jsou k dispozici pro zobrazení a dotazování v Azure Portal.

Pokud chcete zobrazovat uživatelská data vytvořená v předchozím kroku, zadávat na ně dotazy a pracovat s nimi, přihlaste se k portálu [Azure Portal](https://portal.azure.com) ve webovém prohlížeči.

Do vyhledávacího pole nahoře zadejte **Azure Cosmos DB**. Když se otevře okno účtu Cosmos, vyberte svůj účet Cosmos. V levém navigačním panelu vyberte **Průzkumník dat**. Rozbalte kolekci v podokně Kolekce. Pak můžete zobrazovat dokumenty v kolekci, dotazovat se na data a dokonce vytvářet a spouštět uložené procedury, triggery a funkce UDF.

## <a name="review-the-code-optional"></a>Kontrola kódu (volitelné)

Pokud vás zajímá, jak aplikace funguje, můžete si projít fragmenty kódu v této části. Následující fragmenty kódu jsou pořízeny ze `src/main.rs` souboru.

`main`Funkce je vstupním bodem pro `todo` aplikaci. Očekává, že adresa URL připojení Azure Cosmos DB rozhraní API pro MongoDB má být poskytnuta `MONGODB_URL` proměnnou prostředí. Vytvoří se nová instance `TodoManager` , následovaná [ `match` výrazem](https://doc.rust-lang.org/book/ch06-02-match.html) , který se deleguje k příslušné `TodoManager` metodě na základě operace zvolené uživatelem,,, `create` `update` `list` nebo `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` je `struct` , který zapouzdřuje [MongoDB:: Sync:: Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Při pokusu o vytvoření instance `TodoManager` pomocí `new` funkce inicializuje připojení k rozhraní Azure Cosmos DB API pro MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

Nejdůležitější je, `TodoManager` že obsahuje metody, které vám pomůžou se správou `todo` s. Pojďme na ně přecházet po jednom.

`add_todo`Metoda přebírá v `todo` popisu poskytnutém uživatelem a vytvoří instanci `Todo` struktury, která vypadá jako níže. Rozhraní [serde](https://github.com/serde-rs/serde) se používá k mapování (serializace/deserializace) bson dat do instancí `Todo` struktur. Všimněte si, jak `serde` se atributy polí používají k přizpůsobení procesu serializace/de-serialzation. Například `todo_id` pole v TODO `struct` je `ObjectId` a je uloženo v MongoDB jako `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) přijímá [dokument](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) , který představuje `todo` Podrobnosti, které se mají přidat. Všimněte si, že převod z `Todo` na `Document` je proces se dvěma kroky, který byl dosažen pomocí kombinace [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) a [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection. Find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) slouží k načtení *všech* `todo` filtrů s nebo k jejich filtrování na základě stavu zadaného uživatelem ( `pending` nebo `completed` ). Všimněte si, jak ve `while` smyčce `Document` je každá získaná jako výsledek hledání převedena do `Todo` struktury pomocí [bson:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). To je opak toho, co bylo provedeno v `add_todo` metodě.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

`todo`Stav lze aktualizovat (od `pending` do `completed` nebo naopak) pomocí. `todo`Je převeden na [bson:: OID:: objectID](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) , který pak používá metoda[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) k vyhledání dokumentu, který je třeba aktualizovat.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

Odstranění a `todo` je jednoduché pomocí metody [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) .


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit Azure Cosmos DB účet rozhraní API MongoDB pomocí Azure Cloud Shell a vytvořit a spustit aplikaci příkazového řádku Rust pro správu `todo` s. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)