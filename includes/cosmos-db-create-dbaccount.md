---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: 8f8b8384b0c570e2b4925b0e84480b19a632187e
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75752182"
---
1. Azure Cosmos DB účet vytvoříte tak, že přejdete na [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **Azure Cosmos DB**.

   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Vyberte **Přidat**.
1. Na stránce **vytvořit Azure Cosmos DB účet** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nové**a zadejte jedinečný název nové skupiny prostředků. |
    |Název účtu|Jedinečný název|Zadejte název, který identifikuje váš účet Azure Cosmos. Vzhledem k tomu, že *Documents.Azure.com* je připojen k názvu, který zadáte k vytvoření identifikátoru URI, použijte jedinečný název.<br><br>Název může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí mít délku 3-31 znaků.|
    |API|Typ účtu, který se má vytvořit|Vyberte **Core (SQL)** pro vytvoření databáze dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentů, Gremlin pro data grafu, tabulku Azure a Cassandra. V současné době musíte vytvořit samostatný účet pro každé rozhraní API. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.|

   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit oddíly **síť** a **značky** .

1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. 

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet. 

    ![Stránka Azure Cosmos DB účtu](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
