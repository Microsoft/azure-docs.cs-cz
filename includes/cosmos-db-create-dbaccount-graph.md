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
ms.openlocfilehash: 88751efdd5aaceddeed490c95d15d82a263fa81e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733812"
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
   ![Podokno Databáze na webu Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Na stránce **Nový účet** zadejte nastavení nového účtu služby Azure Cosmos DB. 

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID|*Zadejte jedinečný název*|Zadejte jedinečný název, který identifikuje tento účet služby Azure Cosmos DB. Jelikož je řetězec *documents.azure.com* připojený k ID, které poskytnete k vytvoření identifikátoru URI, použijte jedinečné, ale snadno rozpoznatelné ID.<br><br>Toto ID může obsahovat pouze malá písmena, číslice a znak spojovníku (-) a musí se skládat ze 3 až 50 znaků.
    Rozhraní API|Gremlin (graf)|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pro splnění požadavků vaší aplikace pět rozhraní API: SQL (databáze dokumentů), Gremlin (databáze grafu), MongoDB (databáze dokumentů), Tabulka Azure a Cassandra, z nichž každé v současné době vyžaduje samostatný účet. <br><br>Vyberte **Gremlin (graf)**, protože v tomto rychlém startu vytváříte graf, který umožňuje dotazování pomocí syntaxe Gremlin.<br><br>[Další informace o rozhraní Graph API](../articles/cosmos-db/graph-introduction.md)
    Předplatné|*Vaše předplatné*|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>*Potom zadejte stejný jedinečný název, jako jste zadali výše pro ID*|Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID.
    Umístění|*Vyberte oblast nejbližší vašim uživatelům*|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.
    Povolit geografickou redundanci| Ponechte prázdné | Tím se vytvoří replikovaná verze vaší databáze ve druhé (spárované) oblasti. Ponechte toto nastavení prázdné.  
    Připnutí na řídicí panel | Vyberte | Zaškrtněte toto políčko, aby se váš nový účet databáze přidal na řídicí panel portálu, kde k němu budete mít snadný přístup.

    Poté klikněte na **Vytvořit**.

    ![Okno Nový účet pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí zprávu **Blahopřejeme! Váš účet služby Azure Cosmos DB byl vytvořen**.

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)