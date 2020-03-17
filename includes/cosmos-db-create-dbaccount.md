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
ms.openlocfilehash: bfce10b0c9c55012c3ba2fb25121b28157f203b8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672089"
---
1. Přejděte na [Azure Portal](https://portal.azure.com/) a vytvořte si účet služby Azure Cosmos DB. Vyhledejte a vyberte **Azure Cosmos DB**.

   ![Podokno databází portálu Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Vyberte **Přidat**.
1. Na stránce **Vytvořit účet Azure Cosmos DB** zadejte základní nastavení nového účtu služby Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos použít. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **Vytvořit novou**a zadejte jedinečný název nové skupiny prostředků. |
    |Název účtu|Jedinečný název|Zadejte název identifikující váš účet služby Azure Cosmos. Vzhledem k tomu, že se váš identifikátor URI vytvoří připojením řetězce *documents.azure.com* k názvu, který zadáte, použijte jedinečný název.<br><br>Tento název může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí mít délku 3 až 31 znaků.|
    |Rozhraní API|Typ účtu, který se má vytvořit|Pokud chcete vytvořit databázi dokumentů a k dotazování používat syntaxi SQL, vyberte **Core (SQL)** . <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentů, Gremlin pro data grafů, Azure Table a Cassandru. Aktuálně musíte pro každé rozhraní API vytvořit samostatný účet. <br><br>[Další informace o rozhraní SQL API](../articles/cosmos-db/documentdb-introduction.md)|
    |Použít slevu založenou na úrovni Free|Použít nebo Nepoužívat|V úrovně Free služby Azure Cosmos DB získáte ve vašem účtu prvních 400 RU/s a 5 GB úložiště zdarma. Další informace o [úrovni Free](https://azure.microsoft.com/pricing/details/cosmos-db/)|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.|
    |Typ účtu|Produkční nebo Neprodukční|Pokud se účet bude využívat pro produkční úlohy, vyberte **Produkční**. Pokud se účet bude využívat pro neprodukční prostředí, jako je vývoj, testování, kontrola kvality nebo přípravné prostředí, vyberte **Neprodukční**. Jde o nastavení značky prostředku Azure, které optimalizuje prostředí portálu, ale nemá vliv na příslušný účet Azure Cosmos DB. Tuto hodnotu můžete kdykoli změnit.|


> [!NOTE]
> Pro každé předplatné Azure můžete mít maximálně jeden účet Azure Cosmos DB úrovně Free a při vytváření tohoto účtu se musíte přihlásit. Pokud se vám nezobrazuje možnost použít slevu založenou na úrovni Free, znamená to, že v předplatném už je povolený jiný účet s úrovní Free.
   
   ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit oddíly **Síť** a **Značky**.

1. Zkontrolujte nastavení účtu a pak vyberte **Vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, až se na portálu zobrazí stránka s textem **Vaše nasazení je hotové**. 

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Pokud chcete přejít na stránku účtu Azure Cosmos DB, vyberte **Přejít k prostředku**. 

    ![Stránka účtu Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
