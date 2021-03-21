---
title: Rychlý Start – vytvoření prostředků Azure Cosmos DB z Azure Portal
description: V tomto rychlém startu se dozvíte, jak vytvořit databázi Azure Cosmos, kontejner a položky pomocí Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: 041b930a0c2fa82727216b4ca0c8367387d0d801
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367247"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Rychlý Start: vytvoření účtu, databáze, kontejneru a položek Azure Cosmos z Azure Portal
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování databází typu klíč/hodnota, databází dokumentů a grafů, z nichž všechny využívají možnosti globální distribuce a horizontálního škálování v jádru Azure Cosmos DB. 

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit Azure Cosmos DB účet [rozhraní SQL API](./introduction.md) , vytvořit databázi dokumentů a kontejner a přidat data do kontejneru. 

## <a name="prerequisites"></a>Předpoklady

Předplatné Azure nebo bezplatný zkušební účet služby Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Azure Cosmos DB účet vytvoříte tak, že přejdete na [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **Azure Cosmos DB**.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Podokno Databáze na webu Azure Portal":::

1. Vyberte **Přidat**.
1. Na stránce **vytvořit Azure Cosmos DB účet** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nové** a zadejte jedinečný název nové skupiny prostředků. |
    |Account Name|Jedinečný název|Zadejte název, který identifikuje váš účet Azure Cosmos. Vzhledem k tomu, že *Documents.Azure.com* je připojen k názvu, který zadáte k vytvoření identifikátoru URI, použijte jedinečný název.<br><br>Název může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí mít délku 3-31 znaků.|
    |Rozhraní API|Typ účtu, který se má vytvořit|Vyberte **Core (SQL)** pro vytvoření databáze dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentů, Gremlin pro data grafu, tabulku Azure a Cassandra. V současné době musíte pro každé rozhraní API vytvořit samostatný účet. <br><br>[Přečtěte si další informace o rozhraní SQL API](introduction.md).|
    |Režim kapacity|Zřízená propustnost nebo bez serveru|Vyberte **zřízenou propustnost** a vytvořte účet v režimu [zřízené propustnosti](set-throughput.md) . Pokud chcete vytvořit účet v režimu bez [serveru](serverless.md) , vyberte možnost bez **serveru** .|
    |Použít Azure Cosmos DB slevu úrovně Free|Použít nebo nepoužít|U Azure Cosmos DB úrovně Free získáte v účtu první 400 RU/s a 5 GB úložiště zdarma. Přečtěte si další informace o [bezplatné úrovni](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.|
    |Typ účtu|Produkční nebo neprodukční|Vyberte možnost **produkce** , pokud se účet bude používat pro produkční úlohy. Vyberte **neprodukční** , pokud se účet bude používat pro neprodukční, třeba pro vývoj, testování, kontrolu kvality nebo přípravu. Toto je nastavení značky prostředku Azure, které vystavuje možnosti portálu, ale nemá vliv na příslušný účet Azure Cosmos DB. Tuto hodnotu můžete kdykoli změnit.|
    |Geografická redundance|Povolit nebo zakázat|Povolte nebo zakažte globální distribuci na účtu spárováním oblasti s oblastí dvojice. Později můžete do svého účtu přidat další oblasti.|
    |Zápisy do více oblastí|Povolit nebo zakázat|Funkce pro zápis ve více oblastech vám umožní využít zřízené propustnosti pro vaše databáze a kontejnery po celém světě.|
    |Zóny dostupnosti|Povolit nebo zakázat|Zóny dostupnosti vám pomůžou lépe zlepšit dostupnost a odolnost vaší aplikace.|

> [!NOTE]
> V rámci předplatného Azure můžete mít až jednu úroveň bezplatného Azure Cosmos DB účtu a při vytváření účtu musíte souhlasit. Pokud nevidíte možnost použít slevu úrovně Free, znamená to, že v předplatném už je povolený jiný účet s úrovní Free.

> [!NOTE]
> Pokud jako **režim kapacity** vyberete možnost bez **serveru** , nejsou dostupné následující možnosti:
> - Použít slevu založenou na bezplatné úrovni
> - Geografická redundance
> - Zápisy do více oblastí
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Stránka nového účtu pro službu Azure Cosmos DB":::

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit oddíly **síť** a **značky** .

1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Podokno Oznámení portálu Azure Portal":::

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="Stránka Azure Cosmos DB účtu":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Přidání databáze a kontejneru 

K vytvoření databáze a kontejneru můžete použít Průzkumník dat v Azure Portal. 

1.  V levém navigačním panelu na stránce Azure Cosmos DB účtu vyberte **Průzkumník dat** a pak vyberte **Nový kontejner**. 
    
    Možná se budete muset posunout doprava, aby se zobrazilo okno **Přidat kontejner** .
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::
    
1.  V podokně **Přidat kontejner** zadejte nastavení pro nový kontejner.
    
    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|ToDoList|Jako název nové databáze zadejte *ToDoList* . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.<br><br>**Poznámka**: Toto nastavení není k dispozici při vytváření nového kontejneru v účtu bez serveru.| 
    |**ID kontejneru**|Položky|Jako název nového kontejneru zadejte *položky* . Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* .|

    
    Nepřidávat **jedinečné klíče** pro tento příklad. Jedinečné klíče umožňují přidat do databáze vrstvu integrity dat tím, že zajistí jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v tématu [jedinečné klíče v Azure Cosmos DB](unique-keys.md).
    
1.  Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner, který jste vytvořili.

## <a name="add-data-to-your-database"></a>Přidat data do databáze

Přidejte data do nové databáze pomocí Průzkumník dat.

1. V **Průzkumník dat** rozbalte databázi **ToDoList** a rozbalte kontejner **položky** . V dalším kroku vyberte **položky** a pak vyberte **Nová položka**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Vytváření nových dokumentů v Průzkumníku dat na webu Azure Portal":::
   
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
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Zkopírujte data JSON a vyberte Uložit v Průzkumník dat Azure Portal":::
   
1. Znovu vyberte **Nový dokument** a vytvořte a uložte jiný dokument s jedinečnými `id` a dalšími vlastnostmi a hodnotami, které chcete. Vaše dokumenty můžou mít jakoukoli strukturu, protože Azure Cosmos DB do vašich dat neukládají žádné schéma.

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Pokud chcete odstranit jenom databázi a používat účet Azure Cosmos v budoucnu, můžete databázi odstranit pomocí následujících kroků:

* Máte účet Azure Cosmos.
* Otevřete **Průzkumník dat** klikněte pravým tlačítkem na databázi, kterou chcete odstranit, a vyberte **odstranit databázi**.
* Zadáním ID databáze nebo databáze potvrďte operaci odstranění. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB, vytvořit databázi a kontejner pomocí Průzkumník dat. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)