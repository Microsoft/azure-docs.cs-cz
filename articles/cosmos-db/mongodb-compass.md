---
title: Připojení k Azure Cosmos DB s použitím Compass
description: Zjistěte, jak pomocí MongoDB Compass můžete ukládat a spravovat data ve službě Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: roaror
ms.author: roaror
ms.openlocfilehash: ad302df3144787fd231166c7052ecfde268f4086
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451035"
---
# <a name="use-compass-to-connect-to-your-cosmos-db-account"></a>Použít Compass pro připojení k účtu služby Cosmos DB

Tento kurz ukazuje, jak používat [MongoDB Compass](https://www.mongodb.com/products/compass) při ukládání a správu dat v databázi Cosmos DB. Tohoto návodu používáme rozhraní API Azure Cosmos DB pro MongoDB. Neznáte, Compass je grafické uživatelské rozhraní pro MongoDB. Běžně slouží k vizualizaci dat, spouštění dotazů ad-hoc, spolu s spravovat vaše data. 

Cosmos DB je globálně distribuovaná vícemodelová databázová služba od Microsoftu. Můžete rychle vytvořit a dotazovat dokument, klíč/hodnota a databáze grafů, které můžou využívat výhody globální distribuce a možností horizontálního škálování v jádru služby Cosmos DB.


## <a name="pre-requisites"></a>Požadavky 
Pro připojení k účtu služby Cosmos DB pomocí Robo 3T, musíte mít:

* Stáhněte a nainstalujte [Compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Cosmos DB mají [připojovací řetězec](connect-mongodb-account.md) informace

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Připojit k rozhraní API Cosmos DB pro MongoDB 
Připojení účtu Cosmos DB k Compass, můžete postupovat podle následujících kroků:

1. Načíst informace o připojení účtu Cosmos nakonfigurovanou API MongoDB služby Azure Cosmos DB pomocí pokynů [tady](connect-mongodb-account.md).

    ![Snímek obrazovky okna připojovací řetězec](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klikněte na tlačítko s textem **kopírování do schránky** vedle vašeho **primárního a sekundárního připojovacího řetězce** ve službě Cosmos DB. Klepnutím na toto tlačítko bude zkopírujte celý připojovací řetězec do schránky. 

    ![Snímek obrazovky kopírovat do schránky tlačítko](./media/mongodb-compass/mongodb-connection-copy.png)

3. Na ploše nebo počítač, otevřete Compass a klikněte na **připojit** a potom **připojení k...** . 

4. Compass automaticky zjistí připojovací řetězec do schránky a zobrazí výzvu pro dotaz, zda chcete použít pro připojení. Klikněte na **Ano** jak je znázorněno v následujícím snímku obrazovky.

    ![Snímek obrazovky Compass výzvy pro připojení](./media/mongodb-compass/mongodb-compass-detect.png)

5. Po kliknutí na tlačítko **Ano** v předchozím kroku, podrobnosti o vašem z připojovacího řetězce automaticky doplněno. Odebrat hodnotu v vyplní automaticky **nastavte název repliky** pole k zajištění, který je ponecháno prázdné. 

    ![Snímek obrazovky Compass výzvy pro připojení](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klikněte na **připojit** v dolní části stránky. Účet služby Cosmos DB a databáze by měla nyní být viditelné v rámci MongoDB Compass.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použití studia 3T](mongodb-mongochef.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Prozkoumejte MongoDB [ukázky](mongodb-samples.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.