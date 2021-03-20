---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994538"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **vytvořit prostředek**.
   
   ![Vytvoření prostředku na portálu Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stránce **Nový** vyberte **databáze**  >  **Azure Cosmos DB**.
   
   ![Podokno Databáze na webu Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stránce **vytvořit Azure Cosmos DB účet** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>Pak zadejte stejný název jako název účtu.|Vyberte, že chcete **vytvořit novou** IP adresu. Pak zadejte nový název skupiny prostředků pro váš účet. Pro jednoduchost použijte stejný název jako název účtu Azure Cosmos DB. 
    Account Name|Zadejte jedinečný název.|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB. Identifikátor URI vašeho účtu se *Mongo.Cosmos.Azure.com* připojí k jedinečnému názvu účtu.<br><br>Název účtu může obsahovat jenom malá písmena, číslice a spojovníky (-) a musí mít délku 3 až 31 znaků.
    Rozhraní API|Azure Cosmos DB pro rozhraní API služby Mongo DB|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) pro databáze dokumentů, Gremlin pro databáze grafů, Azure Cosmos DB pro rozhraní API služby Mongo DB pro databáze dokumentů, tabulku Azure a Cassandra. V současné době musíte pro každé rozhraní API vytvořit samostatný účet. <br><br>Vyberte **Azure Cosmos DB pro rozhraní API služby MONGO DB**  , protože v tomto rychlém startu vytváříte kolekci, která pracuje s MongoDB.<br><br>[Přečtěte si další informace o Azure Cosmos DB API pro MongoDB](../articles/cosmos-db/mongodb-introduction.md).|
    Umístění|Vyberte oblast nejbližší vašim uživatelům.|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.|
    Režim kapacity|Zřízená propustnost nebo bez serveru|Vyberte **zřízenou propustnost** a vytvořte účet v režimu [zřízené propustnosti](../articles/cosmos-db/set-throughput.md) . Pokud chcete vytvořit účet v režimu bez [serveru](../articles/cosmos-db/serverless.md) , vyberte možnost bez **serveru** .<br><br>**Poznámka**: účty bez serveru podporují jenom rozhraní MongoDB API verze 3,6. Zvolíte-li možnost 3,2, bude tato verze vynutit účet v režimu zřízené propustnosti.

    Vyberte **zkontrolovat + vytvořit**. Můžete přeskočit část **síť** a **značky** . 

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až se na portálu zobrazí **blahopřání. Stránka Azure Cosmos DB pro účet rozhraní API služby Mongo DB je připravena** .

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
