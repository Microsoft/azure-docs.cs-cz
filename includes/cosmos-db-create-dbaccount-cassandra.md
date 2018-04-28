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
ms.openlocfilehash: 0ba379fb14945a59a6a1897118fd21d5f5dee758
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. Na stránce **Nový účet** zadejte nastavení nového účtu služby Azure Cosmos DB. 
 
    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID|*Zadejte jedinečný název*|Zadejte jedinečný název, který identifikuje tento účet služby Azure Cosmos DB. Vzhledem k tomu, že se váš kontaktní bod vytvoří připojením řetězce *documents.azure.com* k ID, které zadáte, použijte jedinečné, ale snadno rozpoznatelné ID.<br><br>Toto ID může obsahovat pouze malá písmena, číslice a znak spojovníku (-) a musí se skládat ze 3 až 50 znaků.
    Rozhraní API|Cassandra|Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pro splnění požadavků vaší aplikace pět rozhraní API: SQL (databáze dokumentů), Gremlin (databáze grafu), MongoDB (databáze dokumentů), Tabulka Azure a Cassandra, z nichž každé v současné době vyžaduje samostatný účet. <br><br>Vyberte **Cassandra**, protože v tomto rychlém startu vytváříte databázi širokých sloupců, která umožňuje dotazování s použitím syntaxe CQL.<br><br>Pokud se v seznamu možnost Cassandra (široké sloupce) nezobrazí, musíte [požádat o zapojení](../articles/cosmos-db/cassandra-introduction.md#sign-up-now) do programu Cassandra API Preview.<br><br> [Další informace o rozhraní Cassandra API](../articles/cosmos-db/cassandra-introduction.md)|
    Předplatné|*Vaše předplatné*|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít. 
    Skupina prostředků|Vytvořit nový<br><br>*Potom zadejte stejný jedinečný název, jako jste zadali výše pro ID*|Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID. 
    Umístění|*Vyberte oblast nejbližší vašim uživatelům*|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.
    Připnutí na řídicí panel | Vyberte | Zaškrtněte toto políčko, aby se váš nový účet databáze přidal na řídicí panel portálu, kde k němu budete mít snadný přístup.

    Poté klikněte na **Vytvořit**.

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí zprávu **Blahopřejeme! Váš účet služby Azure Cosmos DB byl vytvořen**.

