---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/24/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 3915df46495e754843009a761e9a07aa2daddae2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53286173"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. V **vytvořit účet služby Azure Cosmos DB** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|*Vaše předplatné*|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>*Zadejte jedinečný název*|Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. Pro jednoduchost můžete použít stejný název jako název vašeho účtu. 
    Název účtu|*Zadejte jedinečný název*|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné ID.<br><br>ID může obsahovat jenom malá písmena, číslice a znak spojovníku (-) a musí být dlouhý 3 až 31 znaků...
    Rozhraní API|Jádro (SQL)|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB nabízí pět rozhraní API: SQL (databáze dokumentů), Gremlin (databáze grafu), MongoDB (databáze dokumentů), rozhraní Table API a rozhraní Cassandra API. Každé rozhraní API v současnosti vyžaduje vytvoření samostatného účtu. <br><br>Vyberte **Core (SQL)** vzhledem k tomu, že v tomto článku vytvoříte databázi dokumentů a dotazování pomocí syntaxe SQL. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md)|
    Umístění|*Vyberte oblast nejbližší vašim uživatelům*|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.
    Povolit geografickou redundanci| Ponechte prázdné | Tím se vytvoří replikovaná verze vaší databáze ve druhé (spárované) oblasti. Ponechte toto nastavení prázdné.  
    Zapíše více oblastí| Ponechte prázdné | To umožňuje všech oblastech vaší databáze a oblasti pro zápis i čtení. Ponechte toto nastavení prázdné.  

    Pak klikněte na **Zkontrolovat a vytvořit**. Můžete přeskočit **sítě** a **značky** oddílu. 

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-preview.png)

    Zkontrolujte souhrnné informace a klikněte na tlačítko **vytvořit**. 

    ![Na stránku Souhrn ověření účtu](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-summary-preview.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí **vaše nasazení je kompletní** zpráv a klikněte na tlačítko **přejít k prostředku**.     

    ![Na stránce účtu se úspěšně vytvořil](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-complete-preview.png)

5. Na portálu se teď budou zobrazovat **Blahopřejeme! Váš účet služby Azure Cosmos DB byl vytvořen**.

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

