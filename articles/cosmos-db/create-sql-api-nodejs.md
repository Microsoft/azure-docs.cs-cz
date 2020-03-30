---
title: Úvodní příručka– Použití souboru Node.js k dotazování z účtu SQL API Azure Cosmos DB
description: Jak pomocí node.js vytvořit aplikaci, která se připojí k účtu SQL API Azure Cosmos DB a dotazuje data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 0b29f9c1f395e079c97d5877d08bd7bd73c7ea53
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240316"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Úvodní příručka: Použití souboru Node.js k připojení a dotazování dat z účtu SQL API Azure Cosmos DB

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete účet Azure Cosmos DB SQL API z webu Azure Portal a pomocí aplikace Node.js naklonované z GitHubu. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="walkthrough-video"></a>Video z návodu

Podívejte se na toto video, kde najdete kompletní návod k obsahu v tomto článku.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) `https://localhost:8081` s identifikátorem URI a klíč `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Vytvoření účtu Azure Cosmos

Pro tento účel rychlého startu můžete použít [vyzkoušet Azure Cosmos DB pro bezplatnou](https://azure.microsoft.com/try/cosmosdb/) možnost vytvořit účet Azure Cosmos.

1. Přejděte na [stránku vyzkoušet Azure Cosmos DB zdarma.](https://azure.microsoft.com/try/cosmosdb/)

1. Zvolte účet **rozhraní SQL** API a vyberte **Vytvořit**. Přihlaste se pomocí účtu Microsoft.

1. Po úspěšném přihlášení by měl být připravený váš účet Azure Cosmos. Kliknutím **na Otevřít na webu Azure Portal** otevřete nově vytvořený účet.

Možnost "vyzkoušet Azure Cosmos DB zdarma" nevyžaduje předplatné Azure a nabízí vám účet Azure Cosmos po omezenou dobu 30 dnů. Pokud chcete účet Azure Cosmos používat delší dobu, měli byste místo toho [vytvořit účet v](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) rámci předplatného Azure.

## <a name="add-a-container"></a>Přidání kontejneru

Teď můžete použít nástroj Průzkumník dat na webu Azure Portal k vytvoření databáze a kontejneru.

1. Vyberte nový kontejner **průzkumníka** > **dat**.

   Oblast **Přidat kontejner** se zobrazí zcela vpravo, možná budete muset posunout doprava, abyste ji viděli.

   ![Průzkumník dat na portálu Azure Portal – podokno Přidat kontejner](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. Na stránce **Přidat kontejner** zadejte nastavení nového kontejneru.

   | Nastavení           | Navrhovaná hodnota | Popis                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **ID databáze**   | Úlohy           | Jako název nové databáze zadejte _Tasks_. Názvy databází musí obsahovat 1 až 255 znaků a nemohou obsahovat `/, \\, #, ?`nebo koncové mezery. Zkontrolujte možnost **Propustnost zřizování databáze,** umožňuje sdílet propustnost zřízenou do databáze ve všech kontejnerech v databázi. Tato možnost také pomáhá s úsporami nákladů. |
   | **Propustnost**    | 400             | Ponechte propustnost na 400 jednotek požadavku za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.                                                                                                                                                                                                                                                    |
   | **ID kontejneru**  | Items           | Jako název nového kontejneru zadejte _položky._ Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.                                                                                                                                                                                                                                                               |
   | **Klíč oddílu** | /kategorie       | Ukázka popsaná v tomto článku používá _/category_ jako klíč oddílu.                                                                                                                                                                                                                                                                                                           |

   Kromě předchozínastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením zásady jedinečného klíče při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).

   Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní naklonujeme aplikaci Node.js z GitHubu, nastavíme připojovací řetězec a spusťme ji.

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud máte zájem dozvědět se, jak se v kódu vytvářejí databázové prostředky Azure Cosmos, můžete zkontrolovat následující fragmenty. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string).

Pokud jste obeznámeni s předchozí verzí sady SQL JavaScript SDK, můžete být zvyklí na _zobrazení kolekce_ termínů a _dokumentu_. Vzhledem k tomu, že Azure Cosmos DB podporuje [více modelů rozhraní API](introduction.md), verze [2.0+ sady JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) používá obecný _termíny kontejneru_, což může být kolekce, graf nebo tabulka a _položka_ k popisu obsahu kontejneru.

Sada Cosmos DB JavaScript SDK se nazývá "@azure/cosmos" a může být instalována z npm ...

```bash
npm install @azure/cosmos
```

Z souboru _app.js_ jsou převzaty všechny následující úryvky.

- Je `CosmosClient` importován `@azure/cosmos` z balíčku npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Nový `CosmosClient` objekt je inicializován.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Vyberte databázi "Úkoly".

  ```javascript
  const database = client.database(databaseId);
  ```

- Vyberte kontejner/kolekci "Položky".

  ```javascript
  const container = database.container(containerId);
  ```

- Vyberte všechny položky v kontejneru Položky.

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
> V metodách "update" a "delete" musí být položka vybrána `container.item()`z databáze voláním . Dva předané parametry jsou id položky a klíč oddílu položky. V tomto případě je paritní klíč hodnotou pole kategorie.

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte na portál Azure a získejte podrobnosti o připojovacím řetězci vašeho účtu Azure Cosmos. Zkopírujte připojovací řetězec do aplikace, aby se mohl připojit k databázi.

1. Ve svém účtu Azure Cosmos DB na [webu Azure Portal](https://portal.azure.com/)vyberte **klíče** z levé navigace a pak vyberte Klíče pro čtení **a zápis**. Pomocí tlačítek kopírování na pravé straně obrazovky zkopírujte identifikátor URI a primární klíč do souboru _app.js_ v dalším kroku.

   ![Zobrazení a zkopírování přístupového klíče na portálu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)

2. V otevřete soubor _config.js._

3. Zkopírujte hodnotu IDENTIFIKÁTORURI z portálu (pomocí tlačítka kopírování) a znějte hodnotou klíče koncového bodu v _souboru config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Potom zkopírujte hodnotu primárního klíče z portálu a udělejte z ní hodnotu `config.key` in _config.js_. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Spuštění aplikace

1. Spuštění `npm install` v terminálu pro@azure/cosmosinstalaci balíčku " npm

2. Spuštění v terminálu `node app.js`, aby se spustila aplikace uzlu.

3. Dvě položky, které jste vytvořili dříve v tomto rychlém startu jsou uvedeny. Vytvoří se nová položka. Příznak "isComplete" na této položce je aktualizován na "true" a nakonec je položka odstraněna.

Můžete pokračovat v experimentování s touto ukázkovou aplikací nebo se vrátit do Průzkumníka dat, upravit data a pracovat s ním.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB, vytvořit kontejner pomocí Průzkumníka dat a spustit aplikaci Node.js. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [import dat do azure cosmos db](import-data.md)
