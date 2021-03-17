---
title: Rychlý Start – použití Node.js k dotazování z Azure Cosmos DB účtu SQL API
description: Jak použít Node.js k vytvoření aplikace, která se připojuje k Azure Cosmos DB účtu SQL API a dotazování na data.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: b4ed27e1515e898a71fb503bb0f260c608ef9f6f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090201"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Rychlý Start: použití Node.js k připojení a dotazování dat z Azure Cosmos DB účtu SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> - [Sada Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete Azure Cosmos DB účet rozhraní SQL API z Azure Portal a pomocí Node.js aplikace naklonované z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="walkthrough-video"></a>Video s návody

V tomto videu získáte kompletní návod k obsahu v tomto článku.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) můžete použít také s identifikátorem URI `https://localhost:8081` a klíčem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Vytvoření účtu Azure Cosmos

Pro účely tohoto rychlého startu můžete pomocí možnosti [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) vytvořit účet Azure Cosmos.

1. Přejděte na stránku [vyzkoušet Azure Cosmos DB pro bezplatnou](https://azure.microsoft.com/try/cosmosdb/) stránku.

1. Zvolte účet rozhraní **SQL** API a vyberte **vytvořit** . Přihlaste se pomocí svého účet Microsoft.

1. Po úspěšném přihlášení by měl být váš účet Azure Cosmos připravený. Vyberte **otevřít v Azure Portal** pro otevření nově vytvořeného účtu.

Možnost "vyzkoušet Azure Cosmos DB zdarma" nevyžaduje předplatné Azure a nabízí vám účet Azure Cosmos po dobu delší než 30 dnů. Pokud chcete účet Azure Cosmos použít po delší dobu, měli byste [účet vytvořit](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) v rámci předplatného Azure.

## <a name="add-a-container"></a>Přidání kontejneru

Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření databáze a kontejneru.

1. Vyberte **Průzkumník dat**  >  **Nový kontejner** .

   Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

   | Nastavení           | Navrhovaná hodnota | Popis                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **ID databáze**   | Úlohy           | Jako název nové databáze zadejte _Tasks_ . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
   | **Propustnost**    | 400             | Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.                                                                                                                                                                                                                                                    |
   | **ID kontejneru**  | Položky           | Jako název nového kontejneru zadejte _položky_ . Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.                                                                                                                                                                                                                                                               |
   | **Klíč oddílu** | /kategorie       | Ukázka popsaná v tomto článku používá jako klíč oddílu _/Category_ .                                                                                                                                                                                                                                                                                                           |

   Kromě předchozích nastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením jedinečné zásady klíčů při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).

   Vyberte **OK** . Průzkumník dat zobrazí novou databázi a kontejner.

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní naklonujte Node.js aplikaci z GitHubu, nastavíme připojovací řetězec a spustíme ji.

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se v kódu vytvářejí prostředky databáze Azure Cosmos, můžete zkontrolovat následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string).

Pokud jste obeznámeni s předchozí verzí sady SQL JavaScript SDK, můžete použít k zobrazení _kolekce_ a _dokumentu_ . Vzhledem k tomu, že Azure Cosmos DB podporuje [více modelů rozhraní API](introduction.md), [verze 2.0 + sady JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) používá obecný _kontejner_ , což může být kolekce, graf nebo tabulka a _položka_ pro popis obsahu kontejneru.

Sada Cosmos DB JavaScript SDK se nazývá " @azure/cosmos " a je možné ji nainstalovat z npm...

```bash
npm install @azure/cosmos
```

Všechny následující fragmenty kódu jsou pořízeny ze souboru _app.js_ .

- `CosmosClient`Je importován z `@azure/cosmos` balíčku npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- `CosmosClient`Byl inicializován nový objekt.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Vyberte databázi Tasks.

  ```javascript
  const database = client.database(databaseId);
  ```

- Vyberte kontejner položky a kolekci.

  ```javascript
  const container = database.container(containerId);
  ```

- Vyberte všechny položky v kontejneru Items (položky).

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Vytvořit novou položku

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Aktualizace položky

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Odstranění položky

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> V metodách "Update" i "Delete" musí být položka vybrána z databáze voláním `container.item()` . Dva předané parametry jsou ID položky a klíč oddílu položky. V tomto případě je klíč parition hodnotou pole kategorie.

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte na Azure Portal a Získejte podrobnosti o připojovacím řetězci účtu Azure Cosmos. Zkopírujte připojovací řetězec do aplikace, aby se mohl připojit k vaší databázi.

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)v levém navigačním panelu vyberte **klíče** a pak vyberte **klíče pro čtení i zápis** . Pomocí tlačítek pro kopírování na pravé straně obrazovky zkopírujte identifikátor URI a primární klíč do souboru _app.js_ v dalším kroku.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::

2. Otevřete soubor _config.js_ .

3. Z portálu Zkopírujte hodnotu identifikátoru URI (pomocí tlačítka kopírování) a nastavte ji jako hodnotu klíče koncového bodu v _config.js_ .

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Pak z portálu Zkopírujte hodnotu primárního klíče a nastavte ji jako hodnotu `config.key` v _config.js_ . Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Spuštění aplikace

1. Spusťte `npm install` v terminálu, aby se nainstaloval @azure/cosmos balíček npm.

2. Spuštění v terminálu `node app.js`, aby se spustila aplikace uzlu.

3. Dvě položky, které jste vytvořili dříve v tomto rychlém startu, jsou vypsány. Vytvoří se nová položka. Příznak "Nedokončený" na této položce se aktualizuje na "true" a nakonec se odstraní položka.

Můžete pokračovat v experimentování s touto ukázkovou aplikací nebo se vrátit do Průzkumník dat, upravit a pracovat s daty.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB, vytvořit kontejner pomocí Průzkumník dat a spustit Node.js aplikaci. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [Import dat do služby Azure Cosmos DB](import-data.md)
