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
ms.openlocfilehash: d6eeb62ce990c24138f14e7f3b8a1ce4048e0174
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50746235"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Na stránce **Nový účet** zadejte nastavení nového účtu služby Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    ID|*Zadejte jedinečný název*|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné ID.<br><br>Toto ID může obsahovat pouze malá písmena, číslice a znak spojovníku (-) a musí se skládat ze 3 až 50 znaků.
    Rozhraní API|SQL|Rozhraní API určuje typ účtu, který se má vytvořit. Služba Azure Cosmos DB poskytuje pět rozhraní API: SQL (databáze dokumentů), Gremlin (databáze grafů), MongoDB (databáze dokumentů), rozhraní API pro tabulky a rozhraní API Cassandra. Každé rozhraní API v současnosti vyžaduje vytvoření samostatného účtu. <br><br>Vyberte **SQL**, protože v tomto článku vytvoříte databázi dokumentů a dotaz s využitím syntaxe SQL. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md)|
    Předplatné|*Vaše předplatné*|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>*Potom zadejte stejný jedinečný název, jako jste zadali výše pro ID*|Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID. 
    Umístění|*Vyberte oblast nejbližší vašim uživatelům*|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.
    Povolit geografickou redundanci| Ponechte prázdné | Tím se vytvoří replikovaná verze vaší databáze ve druhé (spárované) oblasti. Ponechte toto nastavení prázdné.  

    Poté klikněte na **Vytvořit**.

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí zprávu **Blahopřejeme! Váš účet služby Azure Cosmos DB byl vytvořen**.

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

