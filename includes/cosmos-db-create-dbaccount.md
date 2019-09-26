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
ms.openlocfilehash: d21fd6c4848a4806039d5cbed5c7ed8ab4112405
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71307604"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.

   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Na stránce **vytvořit Azure Cosmos DB účet** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Subscription|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nové**a zadejte jedinečný název nové skupiny prostředků. |
    |Název účtu|Jedinečný název|Zadejte název, který identifikuje váš účet Azure Cosmos. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné ID.<br><br>ID může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí mít délku 3-31 znaků.|
    |rozhraní API|Typ účtu, který se má vytvořit|Vyberte **Core (SQL)** pro vytvoření databáze dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentů, Gremlin pro data grafu, tabulku Azure a Cassandra. V současné době musíte vytvořit samostatný účet pro každé rozhraní API. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    |Location|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.|

   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit oddíly **síť** a **značky** .

1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. 

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet. 

    ![Stránka Azure Cosmos DB účtu](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
