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
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521151"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Úvodní příručka: Vytvoření účtu, databáze, kontejneru a položek Azure Cosmos z webu Azure Portal

> [!div class="op_single_selector"]
> * [portál Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Azure Cosmos DB můžete použít k rychlému vytváření a dotazování databází klíčů a hodnot, databází dokumentů a databází grafů, které využívají globální distribuční a horizontální škálovací funkce v jádru Azure Cosmos DB. 

Tento rychlý start ukazuje, jak pomocí portálu Azure k vytvoření účtu [SQL API](sql-api-introduction.md) Azure Cosmos DB, vytvoření databáze dokumentů a kontejneru a přidání dat do kontejneru. 

## <a name="prerequisites"></a>Požadavky

Předplatné Azure nebo bezplatný zkušební účet Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Přejděte na [portál Azure](https://portal.azure.com/) a vytvořte účet Azure Cosmos DB. Vyhledejte a vyberte **Azure Cosmos DB**.

   ![Podokno databází portálu Azure Portal](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Vyberte **Přidat**.
1. Na stránce **Vytvořit účet Azure Cosmos DB** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **Vytvořit nový**a zadejte jedinečný název nové skupiny prostředků. |
    |Název účtu|Jedinečný název|Zadejte název k identifikaci svého účtu Azure Cosmos. Vzhledem k tomu, že *documents.azure.com* je připojen k názvu, který zadáte k vytvoření identifikátoru URI, použijte jedinečný název.<br><br>Název může obsahovat pouze malá písmena, čísla a pomlčku (-) znak. Musí být mezi 3-31 znaků na délku.|
    |rozhraní API|Typ účtu, který chcete vytvořit|Vyberte **Jádro (SQL),** chcete-li vytvořit databázi dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentu, Gremlin pro data grafu, Azure Table a Cassandra. V současné době je nutné vytvořit samostatný účet pro každé rozhraní API. <br><br>[Další informace o rozhraní SQL API](introduction.md).|
    |Použít slevu na úrovni zdarma|Použít nebo Neuplatňovat|S bezplatnou vrstvou Azure Cosmos DB získáte prvních 400 RU/s a 5 GB úložiště zdarma na účtu. Přečtěte si další informace o [bezplatné úrovni](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je uživatelům nejblíže, a poučte jim k datům co nejrychlejší přístup.|
    |Typ účtu|Výroba nebo nevýroba|Vyberte **Výroba,** pokud bude účet použit pro produkční pracovní vytížení. Vyberte **neprodukční,** pokud bude účet použit pro neprodukční, například vývoj, testování, qa nebo pracovní. Toto je nastavení značky prostředků Azure, které ladí prostředí portálu, ale nemá vliv na základní účet Azure Cosmos DB. Tuto hodnotu můžete kdykoli změnit.|


> [!NOTE]
> Můžete mít až jednu bezplatnou úroveň účtu Azure Cosmos DB na jedno předplatné Azure a musíte se přihlásit při vytváření účtu. Pokud nevidíte možnost použít slevu na bezplatnou úroveň, znamená to, že jiný účet v předplatném již byl povolen s bezplatnou úrovní.
   
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Vyberte **Zkontrolovat a vytvořit**. Části **Síť** a **značky** můžete přeskočit.

1. Zkontrolujte nastavení účtu a vyberte **Vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, až se zobrazí stránka portálu **Vaše nasazení je dokončeno**. 

    ![Podokno Oznámení portálu Azure Portal](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Vyberte **Přejít na prostředek** a přejděte na stránku účtu Azure Cosmos DB. 

    ![Stránka účtu Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

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
