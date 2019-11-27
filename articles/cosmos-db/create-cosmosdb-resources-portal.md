---
title: Vytvoří databázi Azure Cosmos z Azure Portal.
description: Vytvořte databázi, kontejner a položky Azure Cosmos pomocí Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/01/2019
ms.openlocfilehash: b42a442564812f4386eb94b9bd7b7c9aff9e9f29
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220734"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Rychlý Start: vytvoření účtu, databáze, kontejneru a položek Azure Cosmos z Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování databází typu klíč/hodnota, databází dokumentů a grafů, z nichž všechny využívají možnosti globální distribuce a horizontálního škálování v jádru Azure Cosmos DB. 

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit účet Azure Cosmos DB [rozhraní SQL API](sql-api-introduction.md) , vytvořit databázi dokumentů a kontejner a přidat data do kontejneru. 

## <a name="prerequisites"></a>Požadavky

Předplatné Azure nebo bezplatný zkušební účet služby Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Přidání databáze a kontejneru 

K vytvoření databáze a kontejneru můžete použít Průzkumník dat v Azure Portal. 

1.  V levém navigačním panelu na stránce Azure Cosmos DB účtu vyberte **Průzkumník dat** a pak vyberte **Nový kontejner**. 
    
    Možná se budete muset posunout doprava, aby se zobrazilo okno **Přidat kontejner** .
    
    ![Azure Portal Průzkumník dat, přidat podokno kontejneru](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  V podokně **Přidat kontejner** zadejte nastavení pro nový kontejner.
    
    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|ToDoList|Jako název nové databáze zadejte *ToDoList* . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat `/, \\, #, ?`ani mezeru. Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Items|Jako název nového kontejneru zadejte *položky* . ID kontejnerů mají stejné požadavky na znaky jako názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* .|

    
    Nepřidávat **jedinečné klíče** pro tento příklad. Jedinečné klíče umožňují přidat do databáze vrstvu integrity dat tím, že zajistí jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v tématu [jedinečné klíče v Azure Cosmos DB](unique-keys.md).
    
1.  Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner, který jste vytvořili.

## <a name="add-data-to-your-database"></a>Přidat data do databáze

Přidejte data do nové databáze pomocí Průzkumník dat.

1. V **Průzkumník dat**rozbalte databázi **ToDoList** a rozbalte kontejner **položky** . V dalším kroku vyberte **položky**a pak vyberte **Nová položka**. 
   
   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Přidejte následující strukturu do dokumentu na pravé straně podokna **dokumenty** :

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Vyberte **Uložit**.
   
   ![Zkopírujte data JSON a vyberte Uložit v Průzkumník dat Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Znovu vyberte **Nový dokument** a vytvořte a uložte jiný dokument s jedinečným `id`a dalšími vlastnostmi a hodnotami, které chcete. Vaše dokumenty můžou mít jakoukoli strukturu, protože Azure Cosmos DB do vašich dat neukládají žádné schéma.

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB, vytvořit databázi a kontejner pomocí Průzkumník dat. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)