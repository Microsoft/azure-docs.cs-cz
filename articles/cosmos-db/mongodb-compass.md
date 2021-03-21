---
title: Připojení k Azure Cosmos DB pomocí kompasu
description: Naučte se používat MongoDB kompas k ukládání a správě dat v Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 43bcd54955cb1a8aaf08785368faf13c14f8322c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413050"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Použití MongoDB kompasu pro připojení k rozhraní API Azure Cosmos DB pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

V tomto kurzu se dozvíte, jak pomocí [MongoDB kompasu](https://www.mongodb.com/products/compass) ukládat a spravovat data v Cosmos DB. Pro tento návod používáme rozhraní API Azure Cosmos DB pro MongoDB. U těch, které neznáte, je kompas pro MongoDB grafické uživatelské rozhraní. Často se používá k vizualizaci dat, spouštění ad-hoc dotazů spolu se správou vašich dat.

Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, klíčů a hodnot a grafů, z nichž všechno přináší výhody funkcí globální distribuce a horizontálního škálování v jádru Cosmos DB.

## <a name="pre-requisites"></a>Požadavky

Pokud se chcete k účtu Cosmos DB připojit pomocí MongoDB kompasu, musíte:

* Stažení a instalace [kompasu](https://www.mongodb.com/download-center/compass?jmp=hero)
* Informace o [připojovacím řetězci](connect-mongodb-account.md) Cosmos DB

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Připojení k rozhraní API Cosmos DB pro MongoDB

Pokud chcete účet Cosmos DB připojit k kompasu, můžete postupovat podle následujících kroků:

1. Načtěte informace o připojení pro účet Cosmos nakonfigurovaný pomocí rozhraní API služby Azure Cosmos DB MongoDB pomocí pokynů [zde](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Snímek obrazovky okna připojovací řetězec":::

2. Klikněte na tlačítko, které říká **kopírování do schránky** vedle vašeho **primárního a sekundárního připojovacího řetězce** v Cosmos DB. Kliknutím na toto tlačítko se zkopíruje celý připojovací řetězec do schránky.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Snímek obrazovky s tlačítkem kopírovat do schránky":::

3. Otevřete kompas na ploše nebo počítači a klikněte na **připojit** a pak **se připojte k...**.

4. Kompas automaticky zjistí připojovací řetězec ve schránce a zobrazí výzvu k dotazování, jestli chcete použít, aby se připojil. Klikněte na **Ano** , jak je znázorněno na snímku obrazovky níže.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Snímek obrazovky se zobrazí dialogové okno s vysvětlením, že vaše schránka obsahuje připojovací řetězec.":::

5. Po kliknutí na tlačítko **Ano** ve výše uvedeném kroku budou automaticky vyplněny podrobnosti z připojovacího řetězce. Odeberte hodnotu automaticky naplněnou v poli **název sady replik** , abyste zajistili, že zůstane prázdné.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Snímek obrazovky se zobrazí v textovém poli název sady replik.":::

6. V dolní části stránky klikněte na **připojit** . Váš účet Cosmos DB a databáze by se teď měly zobrazovat v rámci MongoDB kompasu.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
