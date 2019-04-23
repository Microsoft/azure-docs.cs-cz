---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5d57d7e18befba175a5a8a825494ce512644b5a2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803735"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Na **vytvořit účet služby Azure Cosmos DB** zadejte základního nastavení pro nový účet Azure Cosmos. 
 
    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, kterou chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nový**, pak zadejte jedinečný název pro novou skupinu prostředků. |
    | Název účtu|Zadejte jedinečný název.|Zadejte název pro identifikaci účtu Azure Cosmos. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné ID.<br><br>ID může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí být dlouhý 3-31 znaků.|
    | Rozhraní API|Jádro (SQL)|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB nabízí pět rozhraní API: Jádro (SQL) a MongoDB pro data dokumentu, Gremlin pro data grafu, tabulky Azure a Cassandra. V současné době musíte vytvořit samostatný účet pro každé rozhraní API. <br><br>Vyberte **Core (SQL)** k vytvoření databáze dokumentů a dotazování pomocí syntaxe SQL. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    | Umístění|Vyberte oblast nejbližší vašim uživatelům.|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění co nejblíže vašim uživatelům jim zajistili nejrychlejší přístup k datům.|
   
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit **sítě** a **značky** oddíly. 

1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Trvá několik minut pro vytvoření účtu. Počkejte na stránce portálu zobrazit **vaše nasazení je kompletní**. 

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Vyberte **přejít k prostředku** přejděte na stránku účtu služby Azure Cosmos DB. 

    ![Stránka účtu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
