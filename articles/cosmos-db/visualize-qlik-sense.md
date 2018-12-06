---
title: Připojení ke službě Azure Cosmos DB Qlik Sense a vizualizace dat
description: Tento článek popisuje kroky potřebné pro připojení služby Azure Cosmos DB k Qlik Sense a vizualizace dat služby.
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 57832b59d8b43f5aa157b278e67c50b25084c86c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962831"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Připojení ke službě Azure Cosmos DB Qlik Sense a vizualizace dat

Qlik Sense je nástroj pro vizualizaci dat, která kombinuje data z různých zdrojů do jednoho zobrazení. Qlik Sense indexuje všech možných relací ve vašich datech, takže můžete získat okamžitý přehled k datům. S použitím Qlik Sense můžete vizualizovat data služby Azure Cosmos DB. Tento článek popisuje kroky potřebné pro připojení služby Azure Cosmos DB k Qlik Sense a vizualizace dat služby. 

> [!NOTE]
> Připojení ke službě Azure Cosmos DB Qlik Sense aktuálně podporuje pouze účty SQL API služby Azure Cosmos DB a rozhraním API MongoDB.

Můžete připojit Qlik Sense ke službě Azure Cosmos DB pomocí:

* SQL API služby cosmos DB pomocí konektoru rozhraní ODBC.

* MongoDB API služby cosmos DB pomocí konektoru Qlik Sense MongoDB (aktuálně ve verzi preview).

* Rozhraní MongoDB API služby cosmos DB a rozhraní SQL API pomocí rozhraní REST API konektoru v Qlik Sense.

* Cosmos DB rozhraní Mongodb API s využitím konektoru gRPC Qlik jádra.
Tento článek popisuje podrobnosti o připojení k SQL API služby Cosmos DB pomocí konektoru rozhraní ODBC.

Tento článek popisuje podrobnosti o připojení k SQL API služby Cosmos DB pomocí konektoru rozhraní ODBC.

## <a name="prerequisites"></a>Požadavky

Před postupujte podle pokynů v tomto článku, ujistěte se, že máte připraveno následující prostředky:

* Stáhněte si [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) nebo nastavit Qlik Sense v Azure pomocí [instalace položky marketplace Qlik Sense](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Stáhněte si [videohru data](https://www.kaggle.com/gregorut/videogamesales), je tato ukázková data ve formátu CSV. Bude ukládání těchto dat v účtu služby Cosmos DB a vizualizovat v Qlik Sense.

* Vytvoření účtu rozhraní SQL API služby Azure Cosmos DB pomocí kroků popsaných v [vytvořit účet](create-sql-api-dotnet.md#create-a-database-account) část tohoto článku rychlý start.

* [Vytvořit databázi a kolekci](create-sql-api-dotnet.md#add-a-collection) – můžete použít hodnotu lze nastavit kolekci propustnosti na 1000 RU/s. 

* Načtěte videohru ukázkových prodejních dat do účtu služby Cosmos DB. Můžete importovat data pomocí služby Azure Cosmos DB nástroj pro migraci dat, vám pomůžou [sekvenční](import-data.md#SQLSeqTarget) nebo [hromadný import](import-data.md#SQLBulkTarget) data. Trvá přibližně 3 až 5 minut, než se data určená k importu do účtu služby Cosmos DB.

* Stáhnout, nainstalovat a nakonfigurovat ovladač ODBC pomocí kroků v [připojit ke službě Cosmos DB pomocí ovladače ODBC](odbc-driver.md) článku. Data videohru jednoduché datové sady a není potřeba upravit schéma, použijte výchozí schéma mapování kolekce.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Připojení ke službě Cosmos DB Qlik Sense

1. Otevřete Qlik Sense a vyberte **vytvořit novou aplikaci**. Zadejte název pro vaši aplikaci a vyberte **vytvořit**.

   ![Vytvořit novou aplikaci Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Po úspěšném vytvoření nové aplikace vyberte **otevřít aplikaci** a zvolte **přidat data ze souborů a dalších zdrojů**. 

3. Ze zdroje dat vyberte **ODBC** otevřete nové okno nastavení připojení. 

4. Přepnout na **uživatelské DSN** a zvolte připojení rozhraní ODBC, které jste vytvořili dříve. Zadejte název připojení a vyberte **vytvořit**. 

   ![Vytvoření nového připojení](./media/visualize-qlik-sense/create-new-connection.png)

5. Po vytvoření připojení můžete vybrat databáze, kolekce, ve kterém se nachází data videohru a zobrazit jeho náhled.

   ![Zvolte databázi a kolekci](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Dále vyberte **přidat data** k načítání dat do Qlik Sense. Po načtení dat do Qlik Sense můžete vygenerovat přehledy a provádět analýzu na datech. Můžete používat přehledy, nebo vytvořte svou vlastní aplikaci zkoumání videohry prodeje. Na následujícím obrázku 

   ![Vizualizace dat](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Omezení při připojení k rozhraní ODBC 

Cosmos DB je distribuované databáze bez schématu s ovladači modelovat kolem vývojáře potřebám. Ovladač ODBC vyžaduje, aby databáze se schématem k odvození sloupce, jejich datové typy a dalších vlastností. Pravidelné dotazů SQL nebo syntaxe DML s relační funkce není pro rozhraní SQL API služby Cosmos DB, protože rozhraní SQL API není ANSI SQL. Z tohoto důvodu jsou přeloženy SQL příkazy vydané prostřednictvím ovladače rozhraní ODBC do syntaxe SQL specifické pro službu Cosmos DB, který nemá ekvivalenty pro všechny konstruktory. Aby se zabránilo tyto potíže s překladem, musíte použít schéma při nastavování připojení ODBC. [Připojení pomocí ovladače ODBC](odbc-driver.md) článek nabízí návrhy a metody, které vám pomohou nakonfigurovat schématu. Ujistěte se, že k vytvoření tohoto mapování pro každou databázi a kolekci v rámci účtu služby Cosmos DB.

## <a name="next-steps"></a>Další kroky

Pokud používáte jiné vizualizace nástroje, jako je Power BI, můžete připojit k němu pomocí pokynů v následujícím dokumentu:

* [Vizualizace dat služby Cosmos DB pomocí konektoru služby Power BI](powerbi-visualize.md)
