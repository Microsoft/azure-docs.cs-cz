---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212765"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).

2. V levé nabídce vyberte **Vytvořit zdroj**.
   
   ![Vytvoření prostředku na webu Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na **stránce Nový** vyberte **databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stránce **Vytvořit účet Azure Cosmos DB** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|**Vytvořit nový**, pak název účtu|Vyberte, že chcete **vytvořit novou** IP adresu. Potom zadejte nový název skupiny prostředků pro svůj účet. Pro jednoduchost použijte stejný název jako název účtu Azure Cosmos DB. 
    Název účtu|Jedinečný název|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB.<br><br>Název účtu může používat pouze malá písmena, čísla a pomlčky (-) a musí mít dlouhou 3 až 31 znaků.
    rozhraní API|Table|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) pro databáze dokumentů, Gremlin pro databáze grafů, MongoDB pro databáze dokumentů, Azure Table a Cassandra. Je nutné vytvořit samostatný účet pro každé rozhraní API. <br><br>Vyberte **Azure Table**, protože v tomto rychlém startu vytváříte tabulku, která funguje s rozhraním Table API. <br><br>[Další informace o rozhraní Table API](../articles/cosmos-db/table-introduction.md).|
    Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.

    Můžete ponechat **možnosti Geografická redundance** a **Více oblastí zápisů** na **Zakázat,** abyste se vyhnuli dalším poplatkům, a přeskočit části **Síť** a **značky.**

5. Vyberte **Zkontrolovat+Vytvořit**. Po dokončení ověření vyberte **Vytvořit** a vytvořte účet. 
 
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Vytvoření účtu trvá několik minut. Zobrazí se zpráva, že **vaše nasazení probíhá**. Počkejte na dokončení nasazení a pak vyberte **Přejít na prostředek**.

    ![Podokno oznámení na portálu Azure](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

