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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77212765"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **vytvořit prostředek**.
   
   ![Vytvoření prostředku v Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stránce **Nový** vyberte **databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stránce **vytvořit Azure Cosmos DB účet** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|Vaše předplatné|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|**Vytvořit nový**a potom název účtu|Vyberte, že chcete **vytvořit novou** IP adresu. Pak zadejte nový název skupiny prostředků pro váš účet. Pro jednoduchost použijte stejný název jako název účtu Azure Cosmos DB. 
    Název účtu|Jedinečný název|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB.<br><br>Název účtu může obsahovat jenom malá písmena, číslice a spojovníky (-) a musí mít délku 3 až 31 znaků.
    Rozhraní API|Table|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) pro databáze dokumentů, Gremlin pro databáze grafů, MongoDB pro databáze dokumentů, tabulku Azure a Cassandra. Pro každé rozhraní API musíte vytvořit samostatný účet. <br><br>Vyberte **tabulka Azure**, protože v tomto rychlém startu vytváříte tabulku, která funguje s rozhraní API pro tabulky. <br><br>[Přečtěte si další informace o rozhraní API pro tabulky](../articles/cosmos-db/table-introduction.md).|
    Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.

    Můžete ponechat možnosti pro zápis **geografické redundance** a **více oblastí** na **Zakázat** , abyste se vyhnuli dalším poplatkům, a přeskočíte části **síť** a **značky** .

5. Vyberte **zkontrolovat + vytvořit**. Po dokončení ověření vyberte **vytvořit** a vytvořte účet. 
 
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Vytvoření účtu trvá několik minut. Zobrazí se zpráva s informací o tom, že **probíhá vaše nasazení**. Počkejte, až se nasazení dokončí, a pak vyberte **Přejít k prostředku**.

    ![Podokno oznámení Azure Portal](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

