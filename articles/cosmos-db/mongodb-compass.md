---
title: Připojení k Azure Cosmos DB pomocí compass
description: Zjistěte, jak pomocí MongoDB Compass ukládat a spravovat data v Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 5a3ec79b27231f781b5e4104922993de38c7fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063645"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Připojení k rozhraní API Azure Cosmos DB pro MongoDB pomocí aplikace MongoDB Compass

Tento kurz ukazuje, jak používat [MongoDB Compass](https://www.mongodb.com/products/compass) při ukládání nebo správě dat v Cosmos DB. Pro tento návod používáme rozhraní API Azure Cosmos DB pro MongoDB. Pro ty z vás neznámé, Compass je GUI pro MongoDB. Běžně se používá k vizualizaci dat, spouštění ad hoc dotazů spolu se správou dat.

Cosmos DB je globálně distribuovaná databázová služba společnosti Microsoft s více modely. Můžete rychle vytvářet a dotazovat document, key/value a graph databases, které využívají globální distribuční a horizontální škálovací funkce v jádru Cosmos DB.

## <a name="pre-requisites"></a>Požadavky

Chcete-li se připojit ke svému účtu Cosmos DB pomocí Robo 3T, musíte:

* Stažení a instalace [compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Vyřiďte si informace o [připojovacím řetězci](connect-mongodb-account.md) Cosmos DB

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Připojení k rozhraní API Cosmos DB pro MongoDB

Chcete-li svůj účet Cosmos DB připojit k kompasu, postupujte podle následujících kroků:

1. Načtěte informace o připojení pro váš účet Cosmos nakonfigurované pomocí MongoDB rozhraní API Služby Azure Cosmos DB pomocí pokynů [zde](connect-mongodb-account.md).

    ![Snímek obrazovky s čepelí připojovacího řetězce](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klikněte na tlačítko, které říká **Kopírovat do schránky** vedle **primárního/sekundárního připojovacího řetězce** v Cosmos DB. Kliknutím na toto tlačítko zkopírujete celý připojovací řetězec do schránky.

    ![Snímek obrazovky s tlačítkem kopírování do schránky](./media/mongodb-compass/mongodb-connection-copy.png)

3. Otevřete kompas na ploše / počítači a klikněte na **Připojit** a pak **Připojit k...**.

4. Kompas automaticky detekuje připojovací řetězec ve schránce a zobrazí výzvu k dotazu, zda jej chcete použít k připojení. Klikněte na **Ano,** jak je znázorněno na obrázku níže.

    ![Snímek obrazovky s výzvou kompasu pro připojení](./media/mongodb-compass/mongodb-compass-detect.png)

5. Po kliknutí na **tlačítko Ano** ve výše uvedeném kroku budou vaše údaje z připojovacího řetězce automaticky naplněny. Odeberte hodnotu automaticky vyplněnou v poli **Název sady replik,** abyste zajistili, že zůstane prázdná.

    ![Snímek obrazovky s výzvou kompasu pro připojení](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klikněte na **Připojit** v dolní části stránky. Váš účet Cosmos DB a databáze by nyní měly být viditelné v rámci MongoDB Compass.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.