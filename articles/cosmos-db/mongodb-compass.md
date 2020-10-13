---
title: Připojení k Azure Cosmos DB pomocí kompasu
description: Naučte se používat MongoDB kompas k ukládání a správě dat v Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: jasonwhowell
ms.author: jasonh
ms.openlocfilehash: 15b4e6515f98a4d5a65bb5a56bdd5a5f40f90fe8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91408607"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Použití MongoDB kompasu pro připojení k rozhraní API Azure Cosmos DB pro MongoDB

V tomto kurzu se dozvíte, jak pomocí [MongoDB kompasu](https://www.mongodb.com/products/compass) ukládat a spravovat data v Cosmos DB. Pro tento návod používáme rozhraní API Azure Cosmos DB pro MongoDB. U těch, které neznáte, je kompas pro MongoDB grafické uživatelské rozhraní. Často se používá k vizualizaci dat, spouštění ad-hoc dotazů spolu se správou vašich dat.

Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, klíčů a hodnot a grafů, z nichž všechno přináší výhody funkcí globální distribuce a horizontálního škálování v jádru Cosmos DB.

## <a name="pre-requisites"></a>Požadavky

Pokud se chcete k účtu Cosmos DB připojit pomocí MongoDB kompasu, musíte:

* Stažení a instalace [kompasu](https://www.mongodb.com/download-center/compass?jmp=hero)
* Informace o [připojovacím řetězci](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> V současné době je Azure Cosmos DB rozhraní API pro server MongoDB verze 3,2 podporováno nejnovější verzí MongoDB kompasu. MongoDB kompas ještě není ve verzi 3,6 serveru podporován. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Připojení k rozhraní API Cosmos DB pro MongoDB

Pokud chcete účet Cosmos DB připojit k kompasu, můžete postupovat podle následujících kroků:

1. Načtěte informace o připojení pro účet Cosmos nakonfigurovaný pomocí rozhraní API služby Azure Cosmos DB MongoDB pomocí pokynů [zde](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Snímek obrazovky okna připojovací řetězec":::

2. Klikněte na tlačítko, které říká **kopírování do schránky** vedle vašeho **primárního a sekundárního připojovacího řetězce** v Cosmos DB. Kliknutím na toto tlačítko se zkopíruje celý připojovací řetězec do schránky.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Snímek obrazovky okna připojovací řetězec":::

3. Otevřete kompas na ploše nebo počítači a klikněte na **připojit** a pak **se připojte k...**.

4. Kompas automaticky zjistí připojovací řetězec ve schránce a zobrazí výzvu k dotazování, jestli chcete použít, aby se připojil. Klikněte na **Ano** , jak je znázorněno na snímku obrazovky níže.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Snímek obrazovky okna připojovací řetězec":::

5. Po kliknutí na tlačítko **Ano** ve výše uvedeném kroku budou automaticky vyplněny podrobnosti z připojovacího řetězce. Odeberte hodnotu automaticky naplněnou v poli **název sady replik** , abyste zajistili, že zůstane prázdné.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Snímek obrazovky okna připojovací řetězec":::

6. V dolní části stránky klikněte na **připojit** . Váš účet Cosmos DB a databáze by se teď měly zobrazovat v rámci MongoDB kompasu.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
