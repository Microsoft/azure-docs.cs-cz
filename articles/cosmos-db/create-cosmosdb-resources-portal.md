---
title: Úvodní příručka – vytvoření prostředků Azure Cosmos DB z webu Azure Portal
description: Tento rychlý start ukazuje, jak vytvořit databázi, kontejner a položky Azure Cosmos pomocí portálu Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: bc7e77cc498958b2f8f0c5b2d5ab2d59db97a235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240405"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Úvodní příručka: Vytvoření účtu, databáze, kontejneru a položek Azure Cosmos z webu Azure Portal

> [!div class="op_single_selector"]
> * [Portál Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytváření a dotazování databází klíčů a hodnot, databází dokumentů a databází grafů, které využívají globální distribuční a horizontální škálovací funkce v jádru Azure Cosmos DB. 

Tento rychlý start ukazuje, jak pomocí portálu Azure k vytvoření účtu [SQL API](sql-api-introduction.md) Azure Cosmos DB, vytvoření databáze dokumentů a kontejneru a přidání dat do kontejneru. 

## <a name="prerequisites"></a>Požadavky

Předplatné Azure nebo bezplatný zkušební účet Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Přidání databáze a kontejneru 

Průzkumníka dat na webu Azure Portal můžete použít k vytvoření databáze a kontejneru. 

1.  V levém navigačním panelu na stránce účtu Azure Cosmos DB vyberte **Průzkumník dat** a pak vyberte **Nový kontejner**. 
    
    Možná budete muset posunout doprava, aby se zobcelo **přidat kontejner.**
    
    ![Průzkumník dat na portálu Azure Portal – podokno Přidat kontejner](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  V podokně **Přidat kontejner** zadejte nastavení nového kontejneru.
    
    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|ToDoList|Jako název nové databáze zadejte *ToDoList.* Názvy databází musí obsahovat 1 až 255 znaků a nemohou obsahovat `/, \\, #, ?`nebo koncové mezery. Zkontrolujte možnost **Propustnost zřizování databáze,** umožňuje sdílet propustnost zřízenou do databáze ve všech kontejnerech v databázi. Tato možnost také pomáhá s úsporami nákladů. |
    |**Propustnost**|400|Ponechte propustnost na 400 jednotek požadavku za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Items|Jako název nového kontejneru zadejte *položky.* Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá */category* jako klíč oddílu.|

    
    Nepřidávejte **jedinečné klíče** pro tento příklad. Jedinečné klíče umožňují přidat vrstvu integrity dat do databáze zajištěním jedinečnosti jedné nebo více hodnot na klíč oddílu. Další informace najdete [v tématu Jedinečné klíče v Azure Cosmos DB](unique-keys.md).
    
1.  Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner, který jste vytvořili.

## <a name="add-data-to-your-database"></a>Přidání dat do databáze

Přidejte data do nové databáze pomocí Průzkumníka dat.

1. V **Průzkumníku dat**rozbalte databázi **ToDoList** a rozbalte kontejner **Items.** Dále vyberte **Položky**a pak vyberte **Nová položka**. 
   
   ![Vytváření nových dokumentů v Průzkumníku dat na webu Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Přidejte do dokumentu na pravé straně podokna **Dokumenty** následující strukturu:

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
   
   ![Kopírování v datech jsonu a výběr Uložit v Průzkumníkovi dat na webu Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Znovu vyberte **Nový dokument** a vytvořte `id`a uložte jiný dokument s jedinečnými vlastnostmi a dalšími požadovanými vlastnostmi a hodnotami. Vaše dokumenty mohou mít libovolnou strukturu, protože Azure Cosmos DB neukládá žádné schéma na vaše data.

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Pokud chcete odstranit pouze databázi a v budoucnu použít účet Azure Cosmos, můžete databázi odstranit pomocí následujících kroků:

* Dostal jsem se ke svému účtu Azure Cosmos.
* Otevřete **Průzkumníka dat**, klikněte pravým tlačítkem myši na databázi, kterou chcete odstranit, a vyberte **příkaz Odstranit databázi**.
* Zadejte ID databáze/název databáze a potvrďte operaci odstranění. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit účet Azure Cosmos DB, vytvořit databázi a kontejner pomocí Průzkumníka dat. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
