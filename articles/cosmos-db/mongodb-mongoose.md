---
title: Připojení aplikace Node.js Mongoose k Azure Cosmos DB
description: Naučte se používat Mongoose Framework k ukládání a správě dat v Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 8958699ae279d2613f8dbadca802ee2137407e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442391"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Připojení aplikace Node.js Mongoose k Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento kurz ukazuje, jak používat [rozhraní Mongoose](https://mongoosejs.com/) při ukládání dat v Cosmos DB. Pro tento návod používáme rozhraní API Azure Cosmos DB pro MongoDB. Pokud Mongoose ještě neznáte, jedná se o rozhraní pro modelování objektů pro MongoDB v Node.js, které poskytuje jednoduché řešení modelování dat aplikací založené na schématu.

Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, klíčů a hodnot a grafů, z nichž všechno přináší výhody funkcí globální distribuce a horizontálního škálování v jádru Cosmos DB.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) verze 0.10.29 nebo vyšší

## <a name="create-a-cosmos-account"></a>Vytvoření účtu Cosmos

Pojďme vytvořit účet Cosmos. Pokud již máte účet, který chcete použít, můžete přeskočit k části Nastavení aplikace Node.js. Pokud používáte emulátor Azure Cosmos DB, nastavte emulátor pomocí postupu v [Azure Cosmos DB emulátoru](local-emulator.md) a přejděte k části nastavení Node.js aplikace.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Vytvoření databáze 
V této aplikaci se budeme zabývat dvěma způsoby vytváření kolekcí v Azure Cosmos DB: 
- **Ukládání každého objektového modelu do samostatné kolekce**: doporučujeme [vytvořit databázi s vyhrazenou propustností](set-throughput.md#set-throughput-on-a-database). Použití tohoto modelu kapacity vám poskytne lepší náklady na efektivitu.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text=" KurzNode.js – snímek obrazovky Azure Portal, který ukazuje, jak vytvořit databázi v Průzkumník dat pro účet Azure Cosmos DB pro použití s modulem Mongoose Node":::

- **Ukládání všech objektových modelů do jedné kolekce Cosmos DB**: Chcete-li uložit všechny modely do jedné kolekce, stačí vytvořit novou databázi bez výběru možnosti pro zajištění propustnosti. Použití tohoto modelu kapacity vytvoří každou kolekci s vlastní kapacitou propustnosti pro každý objektový model.

Po vytvoření databáze použijete název v `COSMOSDB_DBNAME` proměnné prostředí níže.

## <a name="set-up-your-nodejs-application"></a>Nastavení aplikace Node.js

>[!Note]
> Pokud si místo nastavování samotné aplikace chcete jenom projít vzorový kód, naklonujte [ukázku](https://github.com/Azure-Samples/Mongoose_CosmosDB) použitou v tomto kurzu a sestavte svou aplikaci Node.js využívající Mongoose ve službě Azure Cosmos DB.

1. Pokud chcete vytvořit aplikaci Node.js ve složce podle vašeho výběru, spusťte na příkazovém řádku node následující příkaz.

    ```npm init```

    Stačí odpovědět na otázky a váš projekt bude připravený k použití.

2. Přidejte do složky nový soubor a pojmenujte ho ```index.js```.
3. Pomocí některé z možností příkazu ```npm install``` nainstalujte potřebné balíčky:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > Následující příklad připojení Mongoose je založen na Mongoose 5 +, který se od předchozích verzí změnil.
    
   * Dotenv (pokud chcete načíst tajné kódy ze souboru .env): ```npm install dotenv --save```

     >[!Note]
     > Příznak ```--save``` přidá závislost do souboru package.json.

4. Importujte závislosti v souboru index.js.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Do souboru ```.env``` přidejte váš připojovací řetězec služby Cosmos DB a název služby Cosmos DB. Zástupné symboly {Cosmos-Account-Name} a {dbname} nahraďte vlastním názvem účtu Cosmos a názvem databáze bez symbolů složených závorek.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMOSDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Připojte se k Cosmos DB pomocí Mongoose architektury přidáním následujícího kódu na konec index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMOSDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      },
    useNewUrlParser: true,
    useUnifiedTopology: true,
    retryWrites: false
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Proměnné prostředí se tady načítají pomocí příkazu process.env.{název_proměnné} s použitím balíčku npm dotenv.

    Po připojení ke službě Azure Cosmos DB můžete v Mongoose začít nastavovat objektové modely.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Osvědčené postupy pro použití Mongoose s Cosmos DB

Pro každý model, který vytvoříte, Mongoose vytvoří novou kolekci. To se nejlépe řeší pomocí [Možnosti propustnosti na úrovni databáze](set-throughput.md#set-throughput-on-a-database), která byla dříve diskutována. Chcete-li použít jednu kolekci, je nutné použít Mongoose [diskriminátory](https://mongoosejs.com/docs/discriminators.html). Diskriminátory představují mechanismus dědičnosti schématu. Umožňují existenci více modelů s překrývajícími se schématy nad stejnou základní kolekcí MongoDB.

Ve stejné kolekci můžete ukládat různé datové modely a následně v době zpracování dotazu můžete pomocí klauzule filtru stáhnout pouze data, která potřebujete. Pojďme si projít každý z modelů.

### <a name="one-collection-per-object-model"></a>Model jedné kolekce na objekt

V této části se seznámíte s tím, jak toho dosáhnout pomocí rozhraní Azure Cosmos DB API pro MongoDB. Tato metoda je doporučeným přístupem, protože umožňuje řídit náklady a kapacitu. V důsledku toho množství jednotek požadavků v databázi nezávisí na počtu objektových modelů. Toto je výchozí operační model pro Mongoose, takže ho možná budete znát.

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
            { givenName: "Buddy" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Nakonec uložte objekt do Cosmos DB. Tím se na pozadí vytvoří kolekce.

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

1. Nyní se v Azure Portal stanete dvěma kolekcemi vytvořenými v Cosmos DB.

   :::image type="content" source="./media/mongodb-mongoose/mongo-mutliple-collections.png" alt-text=" KurzNode.js – snímek obrazovky Azure Portal se zobrazeným účtem Azure Cosmos DB se zvýrazněnými názvy více kolekcí – databáze Node":::

1. Nakonec načteme data z Cosmos DB. Vzhledem k tomu, že používáme výchozí provozní model Mongoose, probíhá čtení stejným způsobem jako jakákoli jiná čtení pomocí Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Použití diskriminátorů Mongoose k ukládání dat v jedné kolekci

V této metodě používáme [Mongoose diskriminátory](https://mongoosejs.com/docs/discriminators.html) , které vám pomůžou optimalizovat náklady na každou kolekci. Diskriminátory umožňují definovat odlišující klíč, pomocí kterého můžete ukládat, odlišovat a filtrovat různé objektové modely.

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
           { givenName: "Buddy" }
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

   :::image type="content" source="./media/mongodb-mongoose/mongo-collections-alldata.png" alt-text=" KurzNode.js – snímek obrazovky Azure Portal se zobrazeným účtem Azure Cosmos DB s názvem kolekce zvýrazněný – databáze Node":::

1. Všimněte si také, že každý objekt má jiný atribut ```__type```, který pomáhá s odlišováním těchto dvou různých objektových modelů.

1. Nakonec načteme data uložená ve službě Azure Cosmos DB. Mongoose zajišťuje filtrování dat na základě modelu. Proto při čtení dat nemusíte nic dělat jinak. Stačí zadat model (v tomto případě ```Family_common```) a Mongoose se postará o filtrování podle hodnoty DiscriminatorKey.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Jak vidíte, pracovat s diskriminátory Mongoose je snadné. Takže pokud máte aplikaci, která používá Mongoose Framework, tento kurz představuje způsob, jak zajistit, aby vaše aplikace byla spuštěná pomocí rozhraní API Azure Cosmos pro MongoDB, aniž by bylo potřeba příliš mnoho změn.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.

[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
