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
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67174698"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. V levém navigačním podokně vyberte **vytvořit prostředek**. Vyberte **databází** a pak vyberte **služby Azure Cosmos DB**.
   
   ![Snímek obrazovky webu Azure portal, zvýrazněním položek Další služby a služby Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Na **vytvořit účet služby Azure Cosmos DB** zadejte nastavení pro nový účet Azure Cosmos DB:
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>Potom zadejte stejný jedinečný název, jako jste zadali v ID|Vyberte, že chcete **vytvořit novou** IP adresu. Zadejte nový název skupiny prostředků pro váš účet. Pro jednoduchost použijte stejný název jako vaše ID. 
    Název účtu|Zadejte jedinečný název.|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB.<br><br>ID můžete použít jenom malá písmena, číslice a znak spojovníku (-). Musí být dlouhý 3 až 31 znaků.
    Rozhraní API|Tabulka Azure|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB nabízí pět rozhraní API: Core(SQL) pro databáze dokumentů, Gremlin databáze grafů, MongoDB, databáze dokumentů, tabulka Azure a Cassandra. V současné době musíte vytvořit samostatný účet pro každé rozhraní API. <br><br>Vyberte **Azure Table** vzhledem k tomu, že v tomto rychlém startu vytváříte tabulku pracující s rozhraním Table API. <br><br>[Další informace o rozhraní Table API](../articles/cosmos-db/table-introduction.md).|
    Location|Vyberte oblast nejbližší vašim uživatelům.|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění co nejblíže vašim uživatelům jim zajistili nejrychlejší přístup k datům.

    Můžete nechat **geografickou redundancí** a **zapíše více oblastí** možnosti výchozí hodnoty (**zakázat**) se vyhnout poplatkům za další RU. Můžete přeskočit **sítě** a **značky** oddíly.

5. Vyberte **zkontrolovat a vytvořit**. Po dokončení ověřování vyberte **vytvořit** k vytvoření účtu. 
 
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Trvá několik minut pro vytvoření účtu. Zobrazí se vám zobrazí zpráva **nasazení probíhá**. Počkejte na dokončení a potom vyberte nasazení **přejít k prostředku**.

    ![Podokno oznámení portálu Azure portal](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
