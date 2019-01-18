---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5695dbf020a72860bd86a8d55a59a5e5b67e3741
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54363923"
---
1. V novém okně se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. V levé nabídce klikněte na **Vytvořit prostředek**, pak na **Databáze** a nakonec v části **Azure Cosmos DB** klikněte na **Vytvořit**.
   
   ![Snímek obrazovky webu Azure Portal se zvýrazněním položek Další služby a Databáze Azure Cosmos](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. V **vytvořit účet služby Azure Cosmos DB** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>Potom zadejte stejný jedinečný název, jako jste zadali v ID|Vyberte, že chcete **vytvořit novou** IP adresu. Zadejte nový název skupiny prostředků pro váš účet. Pro jednoduchost použijte stejný název jako vaše ID. 
    Název účtu|Zadejte jedinečný název.|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné ID.<br><br>ID můžete použít jenom malá písmena, číslice a znak spojovníku (-). Musí být dlouhý 3 až 31 znaků.
    Rozhraní API|Rozhraní API služby Azure Cosmos DB pro MongoDB|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB nabízí pět rozhraní API: Jádro (SQL) pro databáze dokumentů, Gremlin pro databáze grafů, Azure Cosmos DB: API MongoDB databáze dokumentů, tabulka Azure a Cassandra. V současné době musíte vytvořit samostatný účet pro každé rozhraní API. <br><br>Vyberte **MongoDB** vzhledem k tomu, že v tomto rychlém startu vytváříte tabulku pracující s MongoDB.|
    Umístění|Vyberte oblast nejbližší vašim uživatelům.|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.

    Vyberte **zkontrolovat a vytvořit**. Můžete přeskočit **sítě** a **značky** oddílu. 

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí zprávu **Blahopřejeme! Váš účet Cosmos s kompatibilitou při přenosu protokolu pro MongoDB je připravený** stránky.

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
