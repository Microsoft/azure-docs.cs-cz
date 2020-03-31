---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/22/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 8f7a69b81430d964d1aade26ed179354171e4164
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134664"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).

2. V levé nabídce vyberte **Vytvořit zdroj**.
   
   ![Vytvoření prostředku na webu Azure Portal](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na **stránce Nový** vyberte **databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stránce **Vytvořit účet Azure Cosmos DB** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>Pak zadejte stejný název jako název účtu|Vyberte, že chcete **vytvořit novou** IP adresu. Potom zadejte nový název skupiny prostředků pro svůj účet. Pro jednoduchost použijte stejný název jako název účtu Azure Cosmos. 
    Název účtu|Zadejte jedinečný název.|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB. Identifikátor URI účtu bude *cassandra.cosmos.azure.com* připojen k vašemu jedinečnému názvu účtu.<br><br>Název účtu může používat pouze malá písmena, čísla a pomlčky (-) a musí mít dlouhou 3 až 31 znaků.
    rozhraní API|Cassandra|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) pro databáze dokumentů, Gremlin pro databáze grafů, MongoDB pro databáze dokumentů, Azure Table a Cassandra. Je nutné vytvořit samostatný účet pro každé rozhraní API. <br><br>Vyberte **Možnost Cassandra**, protože v tomto rychlém startu vytváříte tabulku, která funguje s rozhraním CASSANDRA API. <br><br>[Další informace o rozhraní API Cassandra](../articles/cosmos-db/cassandra-introduction.md)|
    Umístění|Vyberte oblast nejbližší vašim uživatelům.|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.

    Vyberte **Zkontrolovat+Vytvořit**. Část **Síť** a **značky** můžete přeskočit. 

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až se na portálu zobrazí stránka s nápisem **Gratulujeme! Váš účet Azure Cosmos DB byl vytvořen**.

