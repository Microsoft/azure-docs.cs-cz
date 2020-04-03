---
title: Konzolová aplikace využívající rozhraní API Azure Cosmos DB pro MongoDB a Golang SDK
description: Představuje ukázku kódu Golang, kterou můžete použít k připojení a dotazování pomocí rozhraní API Služby Azure Cosmos DB pro MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 94be0ec16aedc317f1be41998356bc52b66f7e86
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619212"
---
# <a name="quickstart-build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Úvodní příručka: Vytvoření konzolové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a Golang SDK

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Můžete rychle vytvářet a dotazovat document, key/value a graph databases, které využívají globální distribuční a horizontální škálovací funkce v jádru Cosmos DB.

Tento rychlý start ukazuje, jak vzít existující aplikaci MongoDB napsanou v [Golangu](https://golang.org/) a připojit ji k databázi Cosmos pomocí rozhraní API Azure Cosmos DB pro MongoDB.

Jinými slovy, vaše aplikace Golang pouze ví, že se připojuje pomocí klienta MongoDB. Je transparentní pro aplikaci, která jsou uložena v databázi Cosmos.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) a základní znalost jazyka [Go](https://golang.org/).
- IDE – [GoLand](https://www.jetbrains.com/go/) podle Jetbrains, [Visual Studio Kód](https://code.visualstudio.com/) od Microsoftu nebo [Atom](https://atom.io/). V tomto tutoriálu používám GoLand.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Naklonujte ukázkovou aplikaci a nainstalujte požadované balíčky.

1. Vytvořte složku s názvem CosmosDBSample ve složce GOROOT\src, což je ve výchozím nastavení C:\Go\.
2. V okně terminálu Git, jako je třeba Git Bash, spusťte následující příkaz, který naklonuje ukázkové úložiště do složky CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Spuštěním následujícího příkazu získejte balíček mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

Ovladač [mgo](https://labix.org/mgo) je ovladač [MongoDB](https://www.mongodb.com/) pro [jazyk Go,](https://golang.org/) který implementuje bohatý a dobře testovaný výběr funkcí pod velmi jednoduchým rozhraním API podle standardních Go idiomů.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Informace o připojovacím řetězci požadované aplikací v jazyce Go zobrazíte tak, že v navigační nabídce vlevo kliknete na **Rychlý start** a pak na **Jiné**.

2. V prostředí Gogland otevřete soubor main.go v adresáři GOROOT\CosmosDBSample a aktualizujte následující řádky kódu s použitím informací o připojovacím řetězci z webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky. 

    Název databáze je předpona hodnoty **Hostitel** v podokně připojovacího řetězce na webu Azure Portal. Pro účet znázorněný v obrázku níže je název databáze golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Podokno Rychlý start, karta Jiné na webu Azure Portal ukazující informace o připojovacím řetězci](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Uložte soubor main.go.

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Spuštění aplikace](#run-the-app). 

Všechny následující fragmenty kódu pocházejí ze souboru main.go.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Připojení aplikace Go k Cosmos DB

Rozhraní API Azure Cosmos DB pro MongoDB podporuje připojení s podporou TLS. Chcete-li se připojit, musíte definovat funkci **DialServer** v [mgo. DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo), a využít [tls.* Vytáčení* ](https://golang.org/pkg/crypto/tls#Dial) funkce k provedení připojení.

Následující fragment kódu Golang propojuje aplikaci Go s rozhraním API Azure Cosmos DB pro MongoDB. Třída *DialInfo* obsahuje možnosti pro vytvoření relace.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

**Mgo. Dial()** metoda se používá, pokud neexistuje žádné připojení TLS. Pro připojení TLS, **mgo. DialWithInfo()** je vyžadována metoda.

Instance objektu **DialWIthInfo{}** slouží k vytvoření objektu relace. Po vytvoření relace můžete je kolekci přistupovat pomocí následujícího fragmentu kódu:

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Vytvoření dokumentu

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Dotazování nebo čtení dokumentu

Cosmos DB podporuje bohaté dotazy proti datům uloženým v každé kolekci. Následující ukázka kódu obsahuje dotaz, který je možné spustit proti dokumentům v kolekci.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Aktualizace dokumentu

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Odstranění dokumentu

Cosmos DB podporuje odstranění dokumentů.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Spuštění aplikace

1. V Golangu zajistěte, aby gopath (k dispozici v části **Soubor**, **Nastavení**, **Přejít**, **GOPATH**) obsahoval umístění, ve kterém byla nainstalována gopkg, což je ve výchozím nastavení USERPROFILE\go. 
2. Okomentujte řádky pro odstranění dokumentu (řádky 103–107), abyste dokument po spuštění aplikace mohli zobrazit.
3. V golangu klikněte na **Spustit**a potom klikněte na **Spustit soubor Build main.go and run**.

    Aplikace dokončí dokument vytvořený v části [Vytvoření dokumentu](#create-document) a zobrazí jeho popis.
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Golang zobrazující výstup aplikace](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Kontrola dokumentu v Průzkumníku dat

Vraťte se na web Azure Portal a zobrazte dokument v Průzkumníku dat.

1. V navigační nabídce vlevo klikněte na **Průzkumník dat (Preview)**, rozbalte **golang-coach**, **balíček** a pak klikněte na **Dokumenty**. Na kartě **Dokumenty** klikněte na \_id a zobrazte dokument v pravém podokně. 

    ![Průzkumník dat zobrazující nově vytvořený dokument](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. S dokumentem pak můžete přímo pracovat a kliknutím na **Aktualizovat** ho uložit. Můžete také odstranit dokument nebo vytvořit nové dokumenty nebo dotazy.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Cosmos a spustit aplikaci Golang. Nyní můžete importovat další data do databáze Cosmos. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
