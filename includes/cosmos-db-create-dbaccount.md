---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/10/2020
ms.custom: include file
ms.openlocfilehash: 2559b7d81a6f60b633d023a35081b54e1204268a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120180"
---
1. V nabídce Azure Portal nebo na **domovské stránce**vyberte **vytvořit prostředek**.

1. Na **nové** stránce vyhledejte a vyberte **Azure Cosmos DB**.

1. Na stránce **Azure Cosmos DB** vyberte **vytvořit**.

1. Na stránce **vytvořit Azure Cosmos DB účet** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nové**a zadejte jedinečný název nové skupiny prostředků. |
    |Název účtu|Jedinečný název|Zadejte název, který identifikuje váš účet Azure Cosmos. Vzhledem k tomu, že *Documents.Azure.com* je připojen k názvu, který zadáte k vytvoření identifikátoru URI, použijte jedinečný název.<br><br>Název může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí mít délku 3-31 znaků.|
    |Rozhraní API|Typ účtu, který se má vytvořit|Vyberte **Core (SQL)** pro vytvoření databáze dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentů, Gremlin pro data grafu, tabulku Azure a Cassandra. V současné době musíte pro každé rozhraní API vytvořit samostatný účet. <br><br>[Přečtěte si další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    |Použít slevu úrovně Free|Použít nebo nepoužít|U Azure Cosmos DB úrovně Free získáte v účtu první 400 RU/s a 5 GB úložiště zdarma. Přečtěte si další informace o [bezplatné úrovni](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.|
    |Typ účtu|Produkční nebo neprodukční|Vyberte možnost **produkce** , pokud se účet bude používat pro produkční úlohy. Vyberte **neprodukční** , pokud se účet bude používat pro neprodukční, třeba pro vývoj, testování, kontrolu kvality nebo přípravu. Toto je nastavení značky prostředku Azure, které vystavuje možnosti portálu, ale nemá vliv na příslušný účet Azure Cosmos DB. Tuto hodnotu můžete kdykoli změnit.|


    > [!NOTE]
    > V rámci předplatného Azure můžete mít až jednu úroveň bezplatného Azure Cosmos DB účtu a při vytváření účtu musíte souhlasit. Pokud nevidíte možnost použít slevu úrovně Free, znamená to, že v předplatném už je povolený jiný účet s úrovní Free.
   
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit oddíly **síť** a **značky** .

1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. 

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet. 

    ![Stránka Azure Cosmos DB účtu](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
