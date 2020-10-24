---
title: Připojení aplikace v jazyce přejít k rozhraní API Azure Cosmos DB pro MongoDB
description: Tento rychlý Start ukazuje, jak připojit existující aplikaci pro Azure Cosmos DB k rozhraní API pro MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 6b3d01445eb5624addb5edec3ccb9cd8e4a9b6d9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476021"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Rychlý Start: připojení aplikace typu přejít k rozhraní Azure Cosmos DB API pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování. V tomto rychlém startu vytvoříte a spravujete účet Azure Cosmos DB pomocí Azure Cloud Shell, naklonete existující ukázkovou aplikaci z GitHubu a nakonfigurujete ji tak, aby fungovala s Azure Cosmos DB. 

Ukázková aplikace je nástroj pro správu založený na příkazovém řádku `todo` napsaný v části přejít. Rozhraní API pro MongoDB je [kompatibilní s MongoDBm přenosovým protokolem](./mongodb-introduction.md#wire-protocol-compatibility), takže je možné, že se k němu připojí kterýkoli klientský ovladač MongoDB. Azure Cosmos DB Tato aplikace používá [ovladač cestách pro MongoDB](https://github.com/mongodb/mongo-go-driver) způsobem, který je transparentní pro aplikaci, že jsou data uložena v databázi Azure Cosmos DB.

## <a name="prerequisites"></a>Předpoklady
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) spolu s připojovacím řetězcem `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- V počítači [se nainstaluje a získáte praktické](https://golang.org/) znalosti o cestách.
- [Git](https://git-scm.com/downloads).
- Pokud nechcete používat Azure Cloud Shell, [Azure CLI 2.0 +](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Spuštěním následujících příkazů naklonujte ukázkové úložiště.

1. Otevřete příkazový řádek, vytvořte novou složku s názvem `git-samples` a pak zavřete příkazový řádek.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak aplikace funguje, můžete zkontrolovat následující fragmenty kódu. V opačném případě můžete přeskočit ke [spuštění aplikace](#run-the-application). Rozložení aplikace je následující:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Všechny následující fragmenty kódu pocházejí ze souboru `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Připojení aplikace v jazyce Go ke službě Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) Zapouzdřuje připojovací řetězec pro Azure Cosmos DB, který se předává pomocí proměnné prostředí (podrobnosti najdete v nadcházející části). Připojení je inicializováno pomocí, [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) ke kterému `clientOptions` je instance předána. vyvolaná [ `Ping` funkce](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) pro potvrzení úspěšného připojení (Jedná se o strategii s chybou pro rychlé obnovení)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Použití [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) Konfigurace je důležité, bez toho, aby se zobrazila následující chyba připojení: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Vytvořit `todo` položku

Chcete-li vytvořit `todo` , získáme popisovač [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) a vyvolá [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) funkci. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Předáte do `Todo` struktury, která obsahuje popis a stav (který je zpočátku nastavený na hodnotu `pending` ).

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo`Položky seznamu

Na základě kritérií můžeme seznam TODO. Vytvoří se [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) pro zapouzdření kritérií filtru.

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) slouží k hledání dokumentů na základě filtru a výsledek je převeden na řez `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Nakonec se informace vykreslí v tabulkovém formátu.

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Aktualizace `todo` položky

`todo`Může být aktualizován na základě jeho `_id` . Vytvoří se [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) filtr založený na `_id` a druhý vytvoří aktualizované informace, což je nový stav ( `completed` nebo `pending` ) v tomto případě. Nakonec [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) je funkce vyvolána s filtrem a aktualizovaným dokumentem.

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Odstraní `todo`

A `todo` je odstraněn v závislosti na jeho `_id` a zapouzdřený ve formě [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) instance. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) je vyvolána pro odstranění dokumentu.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Sestavení aplikace

Přejděte do adresáře, kam jste naklonováni aplikaci, a sestavte ji (pomocí `go build` ).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Pro potvrzení, že aplikace byla správně sestavena.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Instalační Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace rozhraní příkazového řádku Azure]. 

Pokud používáte nainstalované rozhraní příkazového řádku Azure, přihlaste se ke svému předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) a postupujte podle pokynů na obrazovce. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Přidání modulu služby Azure Cosmos DB

Pokud používáte nainstalované rozhraní příkazového řádku Azure, zkontrolujte pomocí příkazu `az`, zda je komponenta `cosmosdb` už nainstalovaná. Pokud se komponenta `cosmosdb` v seznamu základních příkazů nachází, pokračujte k dalšímu příkazu. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

Pokud se komponenta `cosmosdb` v seznamu základních příkazů nenachází, přeinstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md) pomocí nástroje [AZ Group Create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například webové aplikace, databáze a účty úložiště. 

Následující příklad vytvoří skupinu prostředků pro oblast Západní Evropa. Pro skupinu prostředků vyberte jedinečný název.

Pokud používáte Azure Cloud Shell, vyberte **vyzkoušet**, postupujte podle pokynů na obrazovce pro přihlášení a pak zkopírujte příkaz do příkazového řádku.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Pomocí příkazu [AZ cosmosdb Create](/cli/azure/cosmosdb#az-cosmosdb-create) vytvořte účet Cosmos.

V následujícím příkazu nahraďte zástupný symbol vlastním jedinečným názvem účtu Cosmos `<cosmosdb-name>` . Tento jedinečný název se použije jako součást Cosmos DBho koncového bodu ( `https://<cosmosdb-name>.documents.azure.com/` ), takže název musí být jedinečný ve všech účtech Cosmos v Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr `--kind MongoDB` umožňuje klientská připojení MongoDB.

Po vytvoření účtu služby Azure Cosmos DB zobrazí rozhraní příkazového řádku Azure podobné informace jako v následujícím příkladu. 

> [!NOTE]
> Tento příklad jako formát výstupu Azure CLI používá výchozí JSON. Pokud chcete použít jiný formát výstupu, přečtěte si téma [Formáty výstupu pro příkazy Azure CLI](/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Načtení klíče databáze

Aby bylo možné se připojit k databázi Cosmos, potřebujete klíč databáze. K načtení primárního klíče použijte příkaz [AZ cosmosdb Keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) .

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Výstupní informace rozhraní příkazového řádku Azure jsou podobné jako v následujícím příkladu. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Konfigurace aplikace 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportujte připojovací řetězec, MongoDB databáze a názvy kolekcí jako proměnné prostředí. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true`Možnost je důležitá z důvodu Cosmos DB požadavků. Další informace najdete v tématu [požadavky na připojovací řetězec](connect-mongodb-account.md#connection-string-requirements).
>

Pro `MONGODB_CONNECTION_STRING` proměnnou prostředí Nahraďte zástupné symboly pro `<COSMOSDB_ACCOUNT_NAME>` a. `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: Název účtu Azure Cosmos DB, který jste vytvořili.
2. `<COSMOSDB_PASSWORD>`: Klíč databáze extrahovaný v předchozím kroku

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Můžete zvolit preferované hodnoty pro `MONGODB_DATABASE` a `MONGODB_COLLECTION` nebo je nechat.

## <a name="run-the-application"></a>Spuštění aplikace

Vytvoření `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

V případě úspěchu by se měl zobrazit výstup s MongoDB `_id` nově vytvořeného dokumentu:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Vytvořit další `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Zobrazit seznam `todo` s

```bash
./todo --list all
```

Měli byste vidět ty, které jste právě přidali v tabulkovém formátu, jako např.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Pokud chcete aktualizovat stav `todo` (třeba změnit na `completed` stav), použijte `todo` ID.

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Vypsat pouze dokončené `todo` s

```bash
./todo --list completed
```

Měla by se zobrazit ta, kterou jste právě aktualizovali.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Zobrazení dat v Průzkumníku dat

Data uložená v Azure Cosmos DB jsou k dispozici pro zobrazení a dotazování v Azure Portal.

Pokud chcete zobrazovat uživatelská data vytvořená v předchozím kroku, zadávat na ně dotazy a pracovat s nimi, přihlaste se k portálu [Azure Portal](https://portal.azure.com) ve webovém prohlížeči.

Do vyhledávacího pole nahoře zadejte **Azure Cosmos DB**. Když se otevře okno účtu Cosmos, vyberte svůj účet Cosmos. V levém navigačním panelu vyberte **Průzkumník dat**. Rozbalte kolekci v podokně Kolekce. Pak můžete zobrazovat dokumenty v kolekci, dotazovat se na data a dokonce vytvářet a spouštět uložené procedury, triggery a funkce UDF. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Průzkumník dat zobrazující nově vytvořený dokument&quot;:::


Odstranit `todo` pomocí ID IT

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Výpis `todo` s potvrzením

```bash
./todo --list all
```

`todo`Právě odstraněné by nemělo být přítomno.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| &quot;5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit Azure Cosmos DB účet rozhraní API MongoDB pomocí Azure Cloud Shell a vytvořit a spustit aplikaci příkazového řádku přejít pro správu `todo` s. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)