---
title: Vytvoření aplikace v cestách pomocí Azure Cosmos DB rozhraní API Cassandra pomocí klienta gocql
description: V tomto rychlém startu se dozvíte, jak používat klienta na cestách pro interakci s Azure Cosmos DB rozhraní API Cassandra
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93076397"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Rychlý Start: Vytvoření aplikace v cestách pomocí `gocql` klienta pro správu dat Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování. V tomto rychlém startu se začnete vytvořením účtu Azure Cosmos DB rozhraní API Cassandra. Pak spustíte aplikaci přejít a vytvoříte Cassandra prostor, tabulku a spustíte několik operací. Tato aplikace v cestách používá [gocql](https://github.com/gocql/gocql), což je klient Cassandra pro jazyk přejít. 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) bez předplatného Azure.
- V počítači [se nainstaluje a získáte praktické](https://golang.org/) znalosti o cestách.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi, musíte vytvořit účet Cassandra s Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Začněte klonováním aplikace z GitHubu.

1. Otevřete příkazový řádek a vytvořte novou složku s názvem `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například git bash. Pomocí `cd` příkazu přejděte do nové složky a nainstalujte ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak kód vytvoří databázové prostředky, můžete si projít následující fragmenty kódu. V opačném případě můžete přeskočit ke [spuštění aplikace](#run-the-application) .

`GetSession`Funkce (součást `utils\utils.go` ) vrátí hodnotu [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) , která se používá ke spouštění operací clusteru, jako je například vložení, hledání atd.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

Do funkce se předává hostitel Azure Cosmos DB Cassandra, [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) který získá strukturu, která je pak nakonfigurovaná tak, aby používala uživatelské jméno, heslo, port a odpovídající verzi TLS ([požadavek zabezpečení šifrování HTTPS/SSL/TLS](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database)).

`GetSession`Funkce je pak volána z `main` funkce ( `main.go` ).

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

Informace o připojení a přihlašovací údaje jsou přijímány ve formě proměnných prostředí (vyřešených v `init` metodě).

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Pak se pak používá ke spuštění různých operací (součást `operations\setup.go` ) na Azure Cosmos DB od a po `keyspace` `table` vytvoření.

Jak název navrhuje, `DropKeySpaceIfExists` funkce zruší pouze v případě, že `keyspace` existuje.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` funkce se používá k vytvoření `keyspace` ( `user_profile` )

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Toto je následováno vytvořením tabulky ( `user` ), což se zabere v potaz `CreateUserTable` funkce.

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Po vytvoření prostoru klíčů a tabulky vyvolá operace CRUD (součást `operations\crud.go` ). 

`InsertUser` slouží k vytvoření `User` . Nastaví informace o uživateli (ID, název a město) jako argumenty dotazu pomocí [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` slouží k vyhledání uživatele ( `model\user.go` ) pomocí konkrétního ID uživatele a při [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) vázání atributů uživatele (vrácený Cassandra) k jednotlivým proměnným ( `userid` , `name` , `city` ) – je jedním ze způsobů, jak můžete použít výsledek získaný jako výsledek vyhledávacího dotazu.

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` slouží k načtení všech uživatelů. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) slouží jako zkrácený způsob, jak získat všechny informace o uživateli ve formě řezu `map` s. Každou z nich si můžete představit `map` jako páry klíč-hodnota, kde název sloupce (například `user_id` ) je klíč spolu s příslušnou hodnotou.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Každá `map` informace o uživateli je převedena na `User` `mapToUser` funkci using, která jednoduše extrahuje hodnotu z příslušného sloupce a použije ji k vytvoření instance `User` struktury.

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Jak už jsme uvedli, aplikace přijímá připojení a přihlašovací údaje ve formě proměnných prostředí. 

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **připojovací řetězec**. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Zobrazení a zkopírování podrobností na stránce připojovací řetězec v Azure Portal":::

Zkopírujte hodnoty následujících atributů ( `CONTACT POINT` , a `PORT` `USERNAME` `PRIMARY PASSWORD` ) a nastavte je na příslušné proměnné prostředí.

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

V okně terminálu přejděte do správné složky. Například:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. V terminálu spusťte následující příkaz, kterým aplikaci spustíte.

```shell
go run main.go
```

3. V okně terminálu se zobrazí oznámení o různých operacích, včetně prostoru klíčů a nastavení tabulky, vytvoření uživatele atd.

4. Na portálu Azure Portal otevřete **Data Explorer**, abyste se mohli na tato nová data dotazovat, měnit je a pracovat s nimi. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Zobrazení dat v Průzkumník dat-Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit účet Azure Cosmos DB pomocí rozhraní API Cassandra a spustit aplikaci, která vytvoří databázi Cassandra a kontejner. Teď můžete do svého účtu Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)