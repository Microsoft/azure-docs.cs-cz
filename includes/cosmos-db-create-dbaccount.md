---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 08/19/2020
ms.custom: include file
ms.openlocfilehash: efdd4a065e1eab55f5af420585a44754d42a43e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010343"
---
1. V nabídce Azure Portal nebo na **domovské stránce** vyberte **vytvořit prostředek**.

1. Na **nové** stránce vyhledejte a vyberte **Azure Cosmos DB**.

1. Na stránce **Azure Cosmos DB** vyberte **vytvořit**.

1. Na stránce **vytvořit Azure Cosmos DB účet** zadejte základní nastavení pro nový účet Azure Cosmos. 

    |Nastavení|Hodnota|Popis |
    |---|---|---|
    |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos. |
    |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nové** a zadejte jedinečný název nové skupiny prostředků. |
    |Account Name|Jedinečný název|Zadejte název, který identifikuje váš účet Azure Cosmos. Vzhledem k tomu, že *Documents.Azure.com* je připojen k názvu, který zadáte k vytvoření identifikátoru URI, použijte jedinečný název.<br><br>Název může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí mít délku 3-44 znaků.|
    |Rozhraní API|Typ účtu, který se má vytvořit|Vyberte **Core (SQL)** pro vytvoření databáze dokumentů a dotaz pomocí syntaxe SQL. <br><br>Rozhraní API určuje typ účtu, který se má vytvořit. Azure Cosmos DB poskytuje pět rozhraní API: Core (SQL) a MongoDB pro data dokumentů, Gremlin pro data grafu, tabulku Azure a Cassandra. V současné době musíte pro každé rozhraní API vytvořit samostatný účet. |
    |Režim kapacity|Zřízená propustnost nebo bez serveru|Vyberte **zřízenou propustnost** a vytvořte účet v režimu [zřízené propustnosti](../articles/cosmos-db/set-throughput.md) . Pokud chcete vytvořit účet v režimu bez [serveru](../articles/cosmos-db/serverless.md) , vyberte možnost bez **serveru** .|
    |Použít slevu založenou na bezplatné úrovni|Použít nebo nepoužít|U Azure Cosmos DB úrovně Free získáte v účtu první 400 RU/s a 5 GB úložiště zdarma. Přečtěte si další informace o [bezplatné úrovni](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Umístění|Oblast nejbližší vašim uživatelům|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.|
    |Typ účtu|Produkční nebo neprodukční|Vyberte možnost **produkce** , pokud se účet bude používat pro produkční úlohy. Vyberte **neprodukční** , pokud se účet bude používat pro neprodukční, třeba pro vývoj, testování, kontrolu kvality nebo přípravu. Toto je nastavení značky prostředku Azure, které vystavuje možnosti portálu, ale nemá vliv na příslušný účet Azure Cosmos DB. Tuto hodnotu můžete kdykoli změnit.|

    > [!NOTE]
    > V rámci předplatného Azure můžete mít až jednu úroveň bezplatného Azure Cosmos DB účtu a při vytváření účtu musíte souhlasit. Pokud nevidíte možnost použít slevu úrovně Free, znamená to, že v předplatném už je povolený jiný účet s úrovní Free.
   
    > [!NOTE]
    > Pokud jako **režim kapacity** vyberete možnost bez **serveru** , nejsou dostupné následující možnosti:
    > - Použít slevu založenou na bezplatné úrovni
    > - Geografická redundance
    > - Zápisy do více oblastí
    
    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Stránka nového účtu pro službu Azure Cosmos DB":::

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit oddíly **síť** a **značky** .

1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Podokno Oznámení portálu Azure Portal":::

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Stránka Azure Cosmos DB účtu":::
