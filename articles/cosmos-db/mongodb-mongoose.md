---
title: Připojit aplikaci Node.js využívající Mongoose ke službě Azure Cosmos DB
description: Informace o používání rozhraní Mongoose k ukládání a správě dat ve službě Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: ec47c265c3d6f826bc1b7e7d8038e284c6981b7a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134926"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Připojit aplikaci Node.js využívající Mongoose ke službě Azure Cosmos DB

Tento kurz ukazuje, jak použít [rozhraní Mongoose](https://mongoosejs.com/) při ukládání dat ve službě Azure Cosmos DB. V tomto návodu používáme rozhraní MongoDB API pro službu Azure Cosmos DB. Pokud Mongoose ještě neznáte, jedná se o rozhraní pro modelování objektů pro MongoDB v Node.js, které poskytuje jednoduché řešení modelování dat aplikací založené na schématu.

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) verze 0.10.29 nebo vyšší

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit k části [Nastavení aplikace Node.js](#SetupNode). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení aplikace Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Nastavení aplikace Node.js

>[!Note]
> Pokud si místo nastavování samotné aplikace chcete jenom projít vzorový kód, naklonujte [ukázku](https://github.com/Azure-Samples/Mongoose_CosmosDB) použitou v tomto kurzu a sestavte svou aplikaci Node.js využívající Mongoose ve službě Azure Cosmos DB.

1. Pokud chcete vytvořit aplikaci Node.js ve složce podle vašeho výběru, spusťte na příkazovém řádku node následující příkaz.

    ```npm init```

    Stačí odpovědět na otázky a váš projekt bude připravený k použití.

1. Přidejte do složky nový soubor a pojmenujte ho ```index.js```.
1. Pomocí některé z možností příkazu ```npm install``` nainstalujte potřebné balíčky:
    * Mongoose: ```npm install mongoose@5 --save```

    > [!Note]
    > Připojení Mongoose příkladu níže je založená na Mongoose 5 +, které se změnily od předchozích verzí.
    
    * Dotenv (pokud chcete načíst tajné kódy ze souboru .env): ```npm install dotenv --save```

    >[!Note]
    > Příznak ```--save``` přidá závislost do souboru package.json.

1. Importujte závislosti v souboru index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Do souboru ```.env``` přidejte váš připojovací řetězec služby Cosmos DB a název služby Cosmos DB.

    ```JavaScript
    COSMOSDB_CONNSTR=mongodb://{cosmos-user}.documents.azure.com:10255/{dbname}
    COSMODDB_USER=cosmos-user
    COSMOSDB_PASSWORD=cosmos-secret
    ```

1. Připojte se pomocí rozhraní Mongoose ke službě Azure Cosmos DB přidáním následujícího kódu na konec souboru index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Proměnné prostředí se tady načítají pomocí příkazu process.env.{název_proměnné} s použitím balíčku npm dotenv.

    Po připojení ke službě Azure Cosmos DB můžete v Mongoose začít nastavovat objektové modely.

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Výhody a rizika používání Mongoose se službou Azure Cosmos DB

Pro každý model, který vytvoříte, vytvoří Mongoose na pozadí novou kolekci MongoDB. Vzhledem k modelu fakturace služby Azure Cosmos DB podle počtu kolekcí to však nemusí být cenově nejvýhodnější přístup, pokud máte více řídce naplněných objektových modelů.

Tento návod popisuje oba modely. Nejprve se zaměříme na návod na ukládání jednoho typu dat na kolekci. Takto se Mongoose chová standardně.

V Mongoose existuje také koncept označovaný jako [Diskriminátory](https://mongoosejs.com/docs/discriminators.html). Diskriminátory představují mechanismus dědičnosti schématu. Umožňují existenci více modelů s překrývajícími se schématy nad stejnou základní kolekcí MongoDB.

Ve stejné kolekci můžete ukládat různé datové modely a následně v době zpracování dotazu můžete pomocí klauzule filtru stáhnout pouze data, která potřebujete.

### <a name="one-collection-per-object-model"></a>Model jedné kolekce na objekt

Výchozím chováním Mongoose je vytvořit kolekci MongoDB při každém vytvoření objektového modelu. Tato část popisuje, jak toho dosáhnout pomocí MongoDB pro službu Azure Cosmos DB. Tuto metodu doporučujeme použít se službou Azure Cosmos DB v případě, že máte objektové modely s velkými objemy dat. Toto je výchozí provozní model Mongoose, takže ho pravděpodobně znáte, pokud už znáte Mongoose.

1. Znovu otevřete soubor ```index.js```.

1. Vytvořte definici schématu Family (Rodina).

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Vytvořte objekt Family.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Nakonec objekt uložte do služby Azure Cosmos DB. Tím se na pozadí vytvoří kolekce.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Teď vytvoříme další schéma a objekt. Tentokrát vytvoříme definici schématu Vacation Destinations (Prázdninové destinace), které můžou rodiny zajímat.
    1. Stejně jako předtím vytvoříme schéma.
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Vytvořte ukázkový objekt (do tohoto schématu můžete přidat více objektů) a uložte ho.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Teď přejděte na web Azure Portal a všimněte si, že se ve službě Azure Cosmos DB vytvořily dvě kolekce.

    ![Kurz k Node.js – Snímek obrazovky webu Azure Portal se zobrazeným účtem služby Azure Cosmos DB a několika zvýrazněnými názvy kolekcí – databáze Node][mutiple-coll]

1. Nakonec načteme data ze služby Azure Cosmos DB. Vzhledem k tomu, že používáme výchozí provozní model Mongoose, probíhá čtení stejným způsobem jako jakákoli jiná čtení pomocí Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Použití diskriminátorů Mongoose k ukládání dat v jedné kolekci

V této metodě použijeme [Diskriminátory Mongoose](https://mongoosejs.com/docs/discriminators.html) k optimalizaci nákladů na jednotlivé kolekce služby Azure Cosmos DB. Diskriminátory umožňují definovat odlišující klíč, pomocí kterého můžete ukládat, odlišovat a filtrovat různé objektové modely.

Tady vytvoříme základní objektový model, nadefinujeme odlišující klíč a do základního modelu přidáme jako rozšíření modely Family a VacationDestinations.

1. Nastavíme základní konfiguraci a nadefinujeme klíč diskriminátoru.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Dále nadefinujeme společný objektový model.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Teď nadefinujeme model Family. Všimněte si, že tady místo příkazu ```mongoose.model``` používáme příkaz ```commonModel.discriminator```. Kromě toho také přidáváme základní konfiguraci do schématu mongoose. Proto tady má DiscriminatorKey (Klíč diskriminátoru) hodnotu ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Podobným způsobem přidáme další schéma, tentokrát pro model VacationDestinations. Tady má DiscriminatorKey hodnotu ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Nakonec pro model vytvoříme objekty a uložíme ho.
    1. Přidáme objekty do modelu Family.
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. Dále přidáme objekty do modelu VacationDestinations a uložíme ho.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Pokud se teď vrátíte na web Azure Portal, zjistíte, že máte pouze jednu kolekci ```alldata``` obsahující data modelů Family i VacationDestinations.

    ![Kurz k Node.js – Snímek obrazovky webu Azure Portal se zobrazeným účtem služby Azure Cosmos DB a zvýrazněným názvem kolekce – databáze Node][alldata]

1. Všimněte si také, že každý objekt má jiný atribut ```__type```, který pomáhá s odlišováním těchto dvou různých objektových modelů.

1. Nakonec načteme data uložená ve službě Azure Cosmos DB. Mongoose zajišťuje filtrování dat na základě modelu. Proto při čtení dat nemusíte nic dělat jinak. Stačí zadat model (v tomto případě ```Family_common```) a Mongoose se postará o filtrování podle hodnoty DiscriminatorKey.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Jak vidíte, pracovat s diskriminátory Mongoose je snadné. Takže pokud máte aplikaci využívající rozhraní Mongoose, pomocí tohoto kurzu můžete svou aplikaci zprovoznit s použitím rozhraní MongoDB API ve službě Azure Cosmos DB bez nutnosti příliš mnoha změn.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

Další informace o operacích, operátorech, fázích, příkazech a možnostech MongoDB podporovaných rozhraním MongoDB API služby Azure Cosmos DB najdete v tématu [Podpora funkcí a syntaxe MongoDB v rozhraní MongoDB API](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
