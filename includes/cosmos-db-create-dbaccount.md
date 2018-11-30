---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a144433e1ff6c0f7ca4e0ed72704d339150f2d56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643447"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Na **nový účet** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    ID|Zadejte jedinečný název.|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné ID.<br><br>ID můžete použít jenom malá písmena, číslice a znak spojovníku (-). Musí být dlouhý 3 až 50 znaků.
    Rozhraní API|SQL|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB nabízí pět rozhraní API: databáze SQL pro databáze dokumentů, Gremlin pro graf, databáze dokumentů, rozhraní Table API a rozhraní Cassandra API MongoDB. V současné době musíte vytvořit samostatný účet pro každé rozhraní API. <br><br>Vyberte **SQL** vzhledem k tomu, že v tomto článku vytvoříte databázi dokumentů a dotazování pomocí syntaxe SQL. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>Potom zadejte stejný jedinečný název, jako jste zadali v ID|Vyberte, že chcete **vytvořit novou** IP adresu. Zadejte nový název skupiny prostředků pro váš účet. Pro jednoduchost použijte stejný název jako vaše ID. 
    Umístění|Vyberte oblast nejbližší vašim uživatelům.|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.
    Povolit geografickou redundanci| Ponechte prázdné | Tato možnost se vytvoří replikovaná verze vaší databáze ve druhé (spárované) oblasti. Toto zaškrtávací políčko nechte prázdné. 

    Vyberte **Vytvořit**.

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí zprávu **Blahopřejeme! Váš účet služby Azure Cosmos DB byl vytvořen**.

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

