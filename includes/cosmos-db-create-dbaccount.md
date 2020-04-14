---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: 9c6ce482a4f6464008f4be0ecd94455f0239ede2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275120"
---
1. Přejděte na [portál Azure](https://portal.azure.com/) a vytvořte účet Azure Cosmos DB. Vyhledejte a vyberte **Azure Cosmos DB**.

   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Vyberte **Přidat**.
1. Na stránce **Vytvořit účet Azure Cosmos DB** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **Vytvořit nový**a zadejte jedinečný název nové skupiny prostředků. |
    |Název účtu|Jedinečný název|Zadejte název k identifikaci svého účtu Azure Cosmos. Vzhledem k tomu, že *documents.azure.com* je připojen k názvu, který zadáte k vytvoření identifikátoru URI, použijte jedinečný název.<br><br>Název může obsahovat pouze malá písmena, čísla a pomlčku (-) znak. Musí být mezi 3-31 znaků na délku.|
    |rozhraní API|Typ účtu, který chcete vytvořit|Vyberte **Jádro (SQL),** chcete-li vytvořit databázi dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentu, Gremlin pro data grafu, Azure Table a Cassandra. V současné době je nutné vytvořit samostatný účet pro každé rozhraní API. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    |Použít slevu na úrovni zdarma|Použít nebo Neuplatňovat|S bezplatnou vrstvou Azure Cosmos DB získáte prvních 400 RU/s a 5 GB úložiště zdarma na účtu. Přečtěte si další informace o [bezplatné úrovni](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je uživatelům nejblíže, a poučte jim k datům co nejrychlejší přístup.|
    |Typ účtu|Výroba nebo nevýroba|Vyberte **Výroba,** pokud bude účet použit pro produkční pracovní vytížení. Vyberte **neprodukční,** pokud bude účet použit pro neprodukční, například vývoj, testování, qa nebo pracovní. Toto je nastavení značky prostředků Azure, které ladí prostředí portálu, ale nemá vliv na základní účet Azure Cosmos DB. Tuto hodnotu můžete kdykoli změnit.|

    > [!NOTE]
    > Můžete mít až jednu bezplatnou úroveň účtu Azure Cosmos DB na jedno předplatné Azure a musíte se přihlásit při vytváření účtu. Pokud nevidíte možnost použít slevu na bezplatnou úroveň, znamená to, že jiný účet v předplatném již byl povolen s bezplatnou úrovní.
   
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Vyberte **Zkontrolovat a vytvořit**. Části **Síť** a **značky** můžete přeskočit.

1. Zkontrolujte nastavení účtu a vyberte **Vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, až se zobrazí stránka portálu **Vaše nasazení je dokončeno**. 

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Vyberte **Přejít na prostředek** a přejděte na stránku účtu Azure Cosmos DB. 

    ![Stránka účtu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
