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
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 212dd243842a8bdacc8a77241f456795ef508d9e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731691"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Kurz: Vytvoření konzolové aplikace Node.js pomocí sady JavaScript SDK pro správu dat rozhraní SQL API Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají data dokumentu NoSQL. Účet rozhraní SQL API v Azure Cosmos DB můžete použít k ukládání a přístupu k těmto datům dokumentu. Tento kurz ukazuje, jak vytvořit konzolovou aplikaci Node.js k vytvoření prostředků Azure Cosmos DB a dotazování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvořte účet Azure Cosmos DB a připojte se k němu.
> * Nastavte aplikaci.
> * Vytvořte databázi.
> * Vytvořte kontejner.
> * Přidejte položky do kontejneru.
> * Proveďte základní operace s položkami, kontejnerem a databází.

## <a name="prerequisites"></a>Požadavky 

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 nebo vyšší.

## <a name="create-azure-cosmos-db-account"></a>Vytvoření účtu Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit k části [Nastavení aplikace Node.js](#SetupNode). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení aplikace Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Nastavení aplikace Node.js

Než začnete psát kód k vytvoření aplikace, můžete vytvořit rámec pro vaši aplikaci. Spuštěním následujících kroků nastavte aplikaci Node.js, která má kód architektury:

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

4. Vytvořte a inicializovat `package.json` soubor. Použijte následující příkaz:
   * ```npm init -y```

5. Nainstalujte přes npm modul @azure/cosmos. Použijte následující příkaz:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Nastavení konfigurací aplikace

Teď, když vaše aplikace existuje, musíte se ujistit, že může mluvit s Azure Cosmos DB. Aktualizací několika nastavení konfigurace, jak je znázorněno v následujících krocích, můžete nastavit, aby aplikace mluvila s Azure Cosmos DB:

1. Otevřete soubor *config.js* ve svém oblíbeném textovém editoru.

1. Zkopírujte a vložte následující fragment kódu do souboru *config.js* a nastavte vlastnosti `endpoint` a `key` identifikátor URI a primární klíč koncového bodu Azure Cosmos DB. Databáze, názvy kontejnerů jsou nastaveny na **úkoly** a **položky**. Klíč oddílu, který budete používat pro tuto aplikaci, je **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Podrobnosti o koncovém bodu a klíči najdete v podokně **Klíče** na [webu Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky se získáním klíčů z webu Azure Portal][keys]

Sada JavaScript SDK používá obecný *termíny kontejner* a *položku*. Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. V předchozím fragmentu kódu se `module.exports = config;` kód používá k exportu objektu config, takže na něj můžete odkazovat v rámci souboru *app.js.*

## <a name="create-a-database-and-a-container"></a>Vytvoření databáze a kontejneru

1. Otevřete soubor *databaseContext.js* ve svém oblíbeném textovém editoru.

1. Zkopírujte a vložte následující kód do souboru *databaseContext.js.* Tento kód definuje funkci, která vytvoří "Úkoly", "Položky" databáze a kontejneru, pokud ještě neexistují ve vašem účtu Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Databáze je logický kontejner položek rozdělených napříč kontejnery. Databázi vytvoříte pomocí `createIfNotExists` funkce nebo vytvoření **třídy Databases.** Kontejner se skládá z položek, které v případě rozhraní SQL API jsou dokumenty JSON. Kontejner vytvoříte pomocí funkce `createIfNotExists` nebo create z **třídy Containers.** Po vytvoření kontejneru můžete data uložit a zadat dotaz.

   > [!WARNING]
   > Vytvoření kontejneru má vliv na ceny. Navštivte naši [cenovou stránku,](https://azure.microsoft.com/pricing/details/cosmos-db/) abyste věděli, co můžete očekávat.

## <a name="import-the-configuration"></a>Import konfigurace

1. Otevřete soubor *app.js* ve svém oblíbeném textovém editoru.

1. Zkopírujte a vložte níže `@azure/cosmos` uvedený kód pro import modulu, konfigurace a databaseContext, které jste definovali v předchozích krocích. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Připojení k účtu Azure Cosmos

V souboru *app.js* zkopírujte a vložte následující kód, abyste použili dříve uložený koncový bod a klíč k vytvoření nového objektu CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Pokud se připojujete k **emulátoru Cosmos DB**, zakažte ověření TLS pro proces uzlu:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Teď, když máte kód pro inicializaci klienta Azure Cosmos DB, se budeme věnovat práci s prostředky Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Položky dotazu

Azure Cosmos DB podporuje bohaté dotazy na položky JSON uložené v každém kontejneru. Následující ukázkový kód zobrazuje dotaz, který můžete spustit proti položkám v kontejneru. Můžete dotaz položky pomocí funkce dotazu třídy. `Items` Přidejte do souboru *app.js* následující kód pro dotazování na položky z vašeho účtu Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Vytvoření položky

Položku lze vytvořit pomocí funkce create `Items` třídy. Při použití rozhraní SQL API jsou položky promítány jako dokumenty, které jsou uživatelem definované (libovolné) obsah JSON. V tomto kurzu vytvoříte novou položku v databázi úkolů.

1. V souboru app.js definujte definici položky:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Přidejte následující kód pro vytvoření dříve definované položky:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Aktualizace položky

Azure Cosmos DB podporuje nahrazení obsahu položek. Zkopírujte a vložte následující kód do souboru *app.js.* Tento kód získá položku z kontejneru a aktualizuje pole *isComplete* na hodnotu true.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Odstranění položky

Azure Cosmos DB podporuje odstraňování položek JSON. Následující kód ukazuje, jak získat položku podle jeho ID a odstranit ji. Zkopírujte a vložte do souboru *app.js* následující kód:

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

Chcete-li spustit řešení Začínáme, který obsahuje veškerý kód v tomto článku, budete potřebovat:

* [Účet služby Azure Cosmos DB][create-account].
* Řešení [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), které je k dispozici na GitHubu.

Nainstalujte závislosti projektu přes npm. Použijte následující příkaz:

* ```npm install``` 

Dále v ```config.js``` souboru aktualizujte hodnoty config.endpoint a config.key, jak je popsáno v [kroku 3: Nastavení konfigurací aplikace](#Config).  

Potom v terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už tyto prostředky nejsou potřeba, můžete odstranit skupinu prostředků, účet Azure Cosmos DB a všechny související prostředky. Pokud tak chcete provést, vyberte skupinu prostředků, kterou jste použili pro účet Azure Cosmos DB, vyberte **Odstranit**a potvrďte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování účtu Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
