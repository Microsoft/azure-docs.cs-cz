---
title: Připojení aplikace Node.js Mongoose k Azure Cosmos DB
description: Zjistěte, jak používat rozhraní Mongoose Framework k ukládání a správě dat v Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 7f4d955583b82b224e3c963431c234ef4690198a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063736"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Připojení aplikace Node.js Mongoose k Azure Cosmos DB

Tento kurz ukazuje, jak používat [Mongoose framework](https://mongoosejs.com/) při ukládání dat v Cosmos DB. Pro tento návod používáme rozhraní API Azure Cosmos DB pro MongoDB. Pokud Mongoose ještě neznáte, jedná se o rozhraní pro modelování objektů pro MongoDB v Node.js, které poskytuje jednoduché řešení modelování dat aplikací založené na schématu.

Cosmos DB je globálně distribuovaná databázová služba společnosti Microsoft s více modely. Můžete rychle vytvářet a dotazovat document, key/value a graph databases, které využívají globální distribuční a horizontální škálovací funkce v jádru Cosmos DB.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) verze 0.10.29 nebo vyšší

## <a name="create-a-cosmos-account"></a>Vytvoření účtu Cosmos

Pojďme vytvořit účet Cosmos. Pokud již máte účet, který chcete použít, můžete přeskočit k části Nastavení aplikace Node.js. Pokud používáte emulátor Azure Cosmos DB, postupujte podle pokynů v [emulátoru Azure Cosmos DB](local-emulator.md) a nastavte emulátor a přeskočte dopředu a nastavte aplikaci Node.js.

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
     > Mongoose příklad připojení níže je založen na Mongoose 5 +, který se změnil od předchozích verzí.
    
   * Dotenv (pokud chcete načíst tajné kódy ze souboru .env): ```npm install dotenv --save```

     >[!Note]
     > Příznak ```--save``` přidá závislost do souboru package.json.

1. Importujte závislosti v souboru index.js.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

1. Do souboru ```.env``` přidejte váš připojovací řetězec služby Cosmos DB a název služby Cosmos DB. Nahraďte zástupné symboly {cosmos-account-name} a {dbname} vlastním názvem účtu Cosmos a názvem databáze bez symbolů složených závorek.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMODDB_USER = "<Azure Cosmos account's user name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account passowrd>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

1. Připojte se k Cosmos DB pomocí rozhraní Mongoose přidáním následujícího kódu na konec index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
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

## <a name="caveats-to-using-mongoose-with-cosmos-db"></a>Upozornění na použití Mongoose s Cosmos DB

Pro každý model, který vytvoříte, mongoose vytvoří novou kolekci. Však vzhledem k modelu fakturace na kolekci Cosmos DB, nemusí být nákladově nejefektivnější způsob, jak jít, pokud máte více objektových modelů, které jsou řídce osídlené.

Tento návod popisuje oba modely. Nejprve se zaměříme na návod na ukládání jednoho typu dat na kolekci. Takto se Mongoose chová standardně.

V Mongoose existuje také koncept označovaný jako [Diskriminátory](https://mongoosejs.com/docs/discriminators.html). Diskriminátory představují mechanismus dědičnosti schématu. Umožňují existenci více modelů s překrývajícími se schématy nad stejnou základní kolekcí MongoDB.

Ve stejné kolekci můžete ukládat různé datové modely a následně v době zpracování dotazu můžete pomocí klauzule filtru stáhnout pouze data, která potřebujete.

### <a name="one-collection-per-object-model"></a>Model jedné kolekce na objekt

Výchozím chováním Mongoose je vytvořit kolekci MongoDB při každém vytvoření objektového modelu. Tato část popisuje, jak toho dosáhnout pomocí rozhraní API Azure Cosmos DB pro MongoDB. Tato metoda se doporučuje, pokud máte objektové modely s velkým množstvím dat. Toto je výchozí provozní model Mongoose, takže ho pravděpodobně znáte, pokud už znáte Mongoose.

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

1. Teď, když přejdete na portál Azure, všimnete si dvou kolekcí vytvořených v Cosmos DB.

    ![Kurz Node.js – snímek obrazovky portálu Azure zobrazujícíúčet Azure Cosmos DB se zvýrazněnými více názvy kolekcí – databáze uzlů][multiple-coll]

1. Nakonec si přečtěte data z Cosmos DB. Vzhledem k tomu, že používáme výchozí provozní model Mongoose, probíhá čtení stejným způsobem jako jakákoli jiná čtení pomocí Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Použití diskriminátorů Mongoose k ukládání dat v jedné kolekci

V této metodě používáme [Mongoose Discriminators](https://mongoosejs.com/docs/discriminators.html) pomoci optimalizovat náklady na každou kolekci. Diskriminátory umožňují definovat odlišující klíč, pomocí kterého můžete ukládat, odlišovat a filtrovat různé objektové modely.

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

    ![Kurz Node.js – snímek obrazovky portálu Azure zobrazujícíúčet Azure Cosmos DB se zvýrazněným názvem kolekce – databáze uzlů][alldata]

1. Všimněte si také, že každý objekt má jiný atribut ```__type```, který pomáhá s odlišováním těchto dvou různých objektových modelů.

1. Nakonec načteme data uložená ve službě Azure Cosmos DB. Mongoose zajišťuje filtrování dat na základě modelu. Proto při čtení dat nemusíte nic dělat jinak. Stačí zadat model (v tomto případě ```Family_common```) a Mongoose se postará o filtrování podle hodnoty DiscriminatorKey.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Jak vidíte, pracovat s diskriminátory Mongoose je snadné. Takže pokud máte aplikaci, která používá rozhraní Mongoose, tento kurz je způsob, jak získat aplikaci nahoru a spuštění pomocí rozhraní API Azure Cosmos pro MongoDB bez nutnosti příliš mnoho změn.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
