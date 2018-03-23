---
title: 'Azure Cosmos DB: Vytvoření webové aplikace s využitím .NET a rozhraní SQL API | Dokumentace Microsoftu'
description: Tento článek představuje vzorový kód .NET, který můžete použít k připojení a dotazování služby Azure Cosmos DB přes rozhraní SQL API.
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/15/2017
ms.author: mimig
ms.openlocfilehash: b5ab66371b47bdd0f3bd7a4c9e86e419efebe902
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-cosmos-db-build-a-sql-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB: Vytvoření webové aplikace SQL API s využitím .NET a webu Azure Portal

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)] 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

Tento rychlý start popisuje způsob vytvoření účtu služby Azure Cosmos DB, databáze dokumentů a kolekce pomocí portálu Azure Portal. Potom budete moci sestavit a nasadit webovou aplikaci seznamu úkolů založenou na rozhraní [SQL .NET API](sql-api-sdk-dotnet.md), jak znázorňuje následující snímek obrazovky. 

![Aplikace seznamu úkolů s ukázkovými daty](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection"></a>
## <a name="add-a-collection"></a>Přidání kolekce

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Přidání ukázkových dat

Teď můžete do nové kolekce přidávat data pomocí Průzkumníka dat.

1. V Průzkumníku dat se nová databáze zobrazí v podokně Kolekce. Rozbalte databázi **Tasks**, rozbalte kolekci **Items**, klikněte na **Dokumenty** a potom klikněte na **Nové dokumenty**. 

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. Teď do kolekce přidejte dokument s následující strukturou.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Po přidání formátu json na kartu **Dokumenty** klikněte na **Uložit**.

    ![Zkopírujte data json a v Průzkumníku dat na webu Azure Portal klikněte na Uložit.](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  Vytvořte a uložte ještě jeden dokument, ve kterém vložíte jedinečnou hodnotu pro vlastnost `id` a změníte ostatní vlastnosti podle svých potřeb. Nové dokumenty můžou mít jakoukoli strukturu, protože Azure Cosmos DB neuplatňuje pro data žádné schéma.

     Teď můžete k načítání dat používat dotazy v Průzkumníku dat. Ve výchozím nastavení používá Průzkumník dat pro načtení všech dokumentů v kolekci příkaz `SELECT * FROM c`. Ten však můžete změnit na jiný [příkaz jazyka SQL](sql-api-sql-query.md), například `SELECT * FROM c ORDER BY c._ts DESC`, který vrátí všechny dokumenty v sestupném pořadí podle časového razítka.
 
     Průzkumník dat můžete použít také pro vytváření uložených procedur, funkcí UDF a triggerů pro provádění obchodní logiky a také propustnosti škálování na straně serveru. Průzkumník dat zpřístupní všechna integrovaná programová data v rozhraních API, ale zajistí jednoduchý přístup k vašim datům na portálu Azure Portal.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Naklonujeme aplikaci SQL API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete okno terminálu Git, jako je třeba Git Bash, a pomocí `CD` přejděte do pracovního adresáře.  

2. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Potom otevřete soubor řešení todo v sadě Visual Studio. 

## <a name="review-the-code"></a>Kontrola kódu

Ještě jednou se stručně podívejme na to, co se v aplikaci děje. Otevřete soubor DocumentDBRepository.cs a zjistíte, že tyto řádky kódu vytvářejí prostředky Azure Cosmos DB. 

* Na řádku 76 se inicializuje DocumentClient.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* Na řádku 91 se vytvoří nová databáze.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Na řádku 110 se vytvoří nová kolekce.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new DocumentCollection
            {
               Id = CollectionId
            },
        new RequestOptions { OfferThroughput = 400 });
    ```

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](http://portal.azure.com/) klikněte v účtu databáze Azure Cosmos v levém navigačním panelu na možnost **Klíče** a potom klikněte na **Klíče pro čtení i zápis**. V dalším kroku zkopírujte pomocí tlačítek kopírování na pravé straně obrazovky identifikátor URI a primární klíč do souboru web.config.

    ![Zobrazení a zkopírování přístupového klíče na webu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)

2. V sadě Visual Studio 2017 otevřete soubor web.config. 

3. Z portálu zkopírujte hodnotu identifikátoru URI (pomocí tlačítka kopírování) a nastavte ji jako hodnotu klíče koncového bodu v souboru web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Pak z portálu zkopírujte hodnotu PRIMÁRNÍHO KLÍČE a nastavte ji jako hodnotu authKey v souboru web.config. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

    `<add key="authKey" value="FILLME" />`
    
## <a name="run-the-web-app"></a>Spuštění webové aplikace
1. V sadě Visual Studio klikněte v **Průzkumníku řešení** pravým tlačítkem myši na projekt a potom klikněte na **Spravovat balíčky NuGet**. 

2. Do pole **Procházet** v NuGetu zadejte *DocumentDB*.

3. Z výsledků nainstalujte knihovnu **Microsoft.Azure.DocumentDB**. Tím se nainstaluje balíček Microsoft.Azure.DocumentDB a zároveň všechny závislosti.

4. Spusťte aplikaci stisknutím CTRL+F5. Aplikace se zobrazí v prohlížeči. 

5. V prohlížeči klikněte na **Vytvořit nový** a vytvořte v aplikaci seznamu úkolů několik nových úloh.

   ![Aplikace seznamu úkolů s ukázkovými daty](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

Teď se můžete vrátit do Průzkumníku dat a zobrazit dotaz nebo provést úpravy a pracovat s těmito novými daty. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikace nadále používat, odstraňte na základě následujícího postupu z portálu Azure Portal všechny prostředky vytvořené podle tohoto rychlého startu:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos DB, vytvoření kolekce pomocí Průzkumníka dat a spuštění webové aplikace. Teď můžete do účtu Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)


