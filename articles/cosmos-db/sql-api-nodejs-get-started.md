---
title: Kurz Node.js pro rozhraní SQL API pro Azure Cosmos DB
description: Kurz Node.js ukazující připojení ke službě Azure Cosmos DB a její dotazování pomocí rozhraní SQL API
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: b1e0f8c301d40ff10dbf977731d457a31b096328
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477993"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Kurz: Vytvoření konzolové aplikace Node.js s využitím sady JavaScript SDK pro správu dat rozhraní SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají data dokumentů NoSQL. K ukládání a přístupu k datům v dokumentu můžete použít účet rozhraní SQL API v Azure Cosmos DB. V tomto kurzu se dozvíte, jak vytvořit konzolovou aplikaci Node.js k vytváření prostředků Azure Cosmos DB a dotazování na ně.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvořte účet Azure Cosmos DB a připojte se k němu.
> * Nastavte svoji aplikaci.
> * Vytvořte databázi.
> * Vytvořte kontejner.
> * Přidejte položky do kontejneru.
> * Provede základní operace s položkami, kontejnerem a databází.

## <a name="prerequisites"></a>Předpoklady 

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v 6.0.0 nebo novějším.

## <a name="create-azure-cosmos-db-account"></a>Vytvořit účet Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit k části [Nastavení aplikace Node.js](#SetupNode). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení aplikace Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Nastavení aplikace Node.js

Než začnete psát kód pro sestavení aplikace, můžete sestavit rozhraní pro vaši aplikaci. Spusťte následující postup pro nastavení Node.js aplikace s kódem rozhraní:

1. Otevřete svůj oblíbený terminál.
2. Vyhledejte složku nebo adresář, do kterého chcete uložit aplikaci Node.js.
3. Vytvořte prázdné soubory JavaScriptu pomocí následujících příkazů:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux nebo OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Vytvoří a inicializuje `package.json` soubor. Použijte následující příkaz:
   * ```npm init -y```

5. Nainstalujte přes npm modul @azure/cosmos. Použijte následující příkaz:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Nastavení konfigurací aplikace

Teď, když aplikace existuje, musíte se ujistit, že může komunikovat s Azure Cosmos DB. Aktualizací několika nastavení konfigurace, jak je znázorněno v následujících krocích, můžete nastavit, aby aplikace komunikovala s Azure Cosmos DB:

1. Otevřete *config.js* soubor ve svém oblíbeném textovém editoru.

1. Zkopírujte a vložte následující fragment kódu do souboru *config.js* a nastavte vlastnosti `endpoint` a `key` na identifikátor URI koncového bodu Azure Cosmos DB a primární klíč. Databáze, názvy kontejnerů jsou nastaveny na **úlohy** a **položky**. Klíč oddílu, který budete používat pro tuto aplikaci, je **/Category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Koncový bod a podrobnosti klíče najdete v podokně **klíče** [Azure Portal](https://portal.azure.com).

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Snímek obrazovky se získáním klíčů z webu Azure Portal":::

Sada JavaScript SDK používá obecný pojem *kontejner* a *položka*. Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. V předchozím fragmentu kódu se `module.exports = config;` kód používá k exportu konfiguračního objektu, abyste na něj mohli odkazovat v souboru *app.js* .

## <a name="create-a-database-and-a-container"></a>Vytvoření databáze a kontejneru

1. Otevřete *databaseContext.js* soubor ve svém oblíbeném textovém editoru.

1. Zkopírujte a vložte následující kód do souboru *databaseContext.js* . Tento kód definuje funkci, která vytvoří databázi Tasks, Items a Container, pokud ještě neexistují v účtu Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Databáze je logický kontejner položek rozdělených napříč kontejnery. Databázi vytvoříte pomocí `createIfNotExists` funkce nebo pro vytvoření třídy **databáze** . Kontejner se skládá z položek, které jsou v případě rozhraní SQL API dokumenty JSON. Kontejner vytvoříte pomocí `createIfNotExists` funkce nebo vytvořit z třídy **Containers** . Po vytvoření kontejneru můžete data ukládat a dotazovat na ně.

   > [!WARNING]
   > Vytvoření kontejneru má vliv na ceny. Navštivte naši [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) , abyste věděli, co očekávat.

## <a name="import-the-configuration"></a>Import konfigurace

1. Otevřete *app.js* soubor ve svém oblíbeném textovém editoru.

1. Zkopírováním a vložením následujícího kódu importujte `@azure/cosmos` modul, konfiguraci a databaseContext, který jste definovali v předchozích krocích. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Připojení k účtu Azure Cosmos

V souboru *app.js* zkopírujte a vložte následující kód pro použití dříve uloženého koncového bodu a klíče k vytvoření nového objektu CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Pokud se připojujete k **emulátoru Cosmos DB**, zakažte ověřování TLS pro váš proces uzlu:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Teď, když máte kód pro inicializaci klienta Azure Cosmos DB, se budeme věnovat práci s prostředky Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Položky dotazu

Azure Cosmos DB podporuje formátované dotazy proti položkám JSON uloženým v každém kontejneru. Následující vzorový kód ukazuje dotaz, který lze spustit pro položky ve vašem kontejneru. Můžete zadat dotaz na položky pomocí funkce dotazu `Items` třídy. Přidejte následující kód do souboru *app.js* pro dotazování položek z vašeho účtu Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Vytvořit položku

Položku lze vytvořit pomocí funkce Create `Items` třídy. Pokud používáte rozhraní API SQL, položky se procházejí jako dokumenty, které jsou uživatelsky definovaným (libovolným) obsahem JSON. V tomto kurzu vytvoříte novou položku v rámci databáze Tasks.

1. V app.js souboru definujte definici položky:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Přidejte následující kód pro vytvoření dříve definované položky:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Aktualizace položky

Azure Cosmos DB podporuje nahrazování obsahu položek. Zkopírujte a vložte následující kód do souboru *app.js* . Tento kód získá položku z kontejneru a aktualizuje pole po *dokončení* na hodnotu true.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Odstranění položky

Azure Cosmos DB podporuje odstraňování položek JSON. Následující kód ukazuje, jak získat položku podle jejího ID a odstranit ji. Zkopírujte a vložte následující kód do souboru *app.js* :

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Spuštění aplikace Node.js

Jako celek by váš kód měl vypadat přibližně takto:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:

```bash 
node app.js
```

Měl by se zobrazit výstup počáteční aplikace. Výstup by měl odpovídat ukázkovému textu níže.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Získání úplného řešení kurzu k Node.js

Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jenom chcete stáhnout kód, můžete ho získat z [GitHubu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Chcete-li spustit řešení Začínáme, které obsahuje veškerý kód v tomto článku, budete potřebovat:

* [Účet služby Azure Cosmos DB][create-account].
* Řešení [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), které je k dispozici na GitHubu.

Nainstalujte závislosti projektu přes npm. Použijte následující příkaz:

* ```npm install``` 

Dále v ```config.js``` souboru aktualizujte hodnoty config. Endpoint a config. Key, jak je popsáno v [kroku 3: nastavení konfigurací aplikace](#Config).  

Potom v terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už tyto prostředky nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos DB a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků, kterou jste použili pro účet Azure Cosmos DB, vyberte **Odstranit**a pak potvrďte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování účtu Azure Cosmos DB](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account