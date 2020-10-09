---
title: Připojte Qlik smysl pro Azure Cosmos DB a vizualizaci dat
description: Tento článek popisuje kroky potřebné k připojení Azure Cosmos DB k Qlik smyslům a vizualizaci dat.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 986db85ddbf741dc459625dbd763b9cbbba1c429
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85263343"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Připojte Qlik smysl pro Azure Cosmos DB a vizualizaci dat

Qlik Sense je nástroj pro vizualizaci dat, který kombinuje data z různých zdrojů do jednoho zobrazení. Qlik Sense indexuje každou možnou relaci vašich dat, abyste mohli získat okamžité přehledy o datech. Azure Cosmos DB data můžete vizualizovat pomocí Qlikového rozpoznávání. Tento článek popisuje kroky potřebné k připojení Azure Cosmos DB k Qlik smyslům a vizualizaci dat. 

> [!NOTE]
> Připojení Qlikového smyslu k Azure Cosmos DB se v současnosti podporuje pro rozhraní SQL API a rozhraní Azure Cosmos DB API pro MongoDB pro účty.

Qlik je možné připojit k Azure Cosmos DB pomocí:

* Cosmos DB rozhraní SQL API pomocí konektoru ODBC.

* Rozhraní API pro MongoDB Azure Cosmos DB pomocí konektoru MongoDB Qlik (aktuálně ve verzi Preview).

* Azure Cosmos DB rozhraní API pro MongoDB a SQL API pomocí konektoru REST API ve smyslu Qlik.

* Cosmos DB rozhraní API Mongo DB pomocí konektoru gRPC pro Qlik Core.
Tento článek popisuje podrobnosti o připojení k rozhraní Cosmos DB SQL API pomocí konektoru ODBC.

Tento článek popisuje podrobnosti o připojení k rozhraní Cosmos DB SQL API pomocí konektoru ODBC.

## <a name="prerequisites"></a>Požadavky

Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte připravené následující prostředky:

* Stáhněte si [desktopový Qlik s smyslem](https://www.qlik.com/us/try-or-buy/download-qlik-sense) nebo nastavte v Azure smysl Qlik tím, že [nainstalujete položku tržiště pro Qlik](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Stáhněte si [data z videohry](https://www.kaggle.com/gregorut/videogamesales), Tato ukázková data jsou ve formátu CSV. Tato data budete ukládat do účtu Cosmos DB a vizualizujete je v Qlik smyslu.

* Vytvořte Azure Cosmos DB účet rozhraní SQL API pomocí postupu popsaného v části [Vytvoření účtu](create-sql-api-dotnet.md#create-account) v článku rychlý Start.

* [Vytvoření databáze a kolekce](create-sql-api-java.md#add-a-container) – můžete použít hodnotu nastavit propustnost kolekce na 1000 ru/s. 

* Načtěte ukázková data pro video hry do účtu Cosmos DB. Data můžete importovat pomocí nástroje pro migraci dat Azure Cosmos DB a můžete provádět [sekvenční](import-data.md#SQLSeqTarget) nebo [Hromadný import](import-data.md#SQLBulkTarget) dat. Import dat do účtu Cosmos DB trvá přibližně 3-5 minut.

* Stáhněte, nainstalujte a nakonfigurujte ovladač ODBC pomocí postupu v článku [připojení k Cosmos DB pomocí ovladače ODBC](odbc-driver.md) . Data hry pro video jsou jednoduchá sada dat a nemusíte upravovat schéma, stačí použít výchozí schéma mapování kolekce.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Připojte Qlik smysl k Cosmos DB

1. Otevřete Qlik smysl a vyberte **vytvořit novou aplikaci**. Zadejte název vaší aplikace a vyberte **vytvořit**.

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Vytvoření nové aplikace pro rozpoznávání Qlik":::

2. Po úspěšném vytvoření nové aplikace vyberte **otevřít aplikaci** a zvolte možnost **Přidat data ze souborů a dalších zdrojů**. 

3. V části zdroje dat vyberte **ODBC** a otevřete tak nové okno nastavení připojení. 

4. Přepněte na **uživatelské DSN** a vyberte připojení ODBC, které jste vytvořili dříve. Zadejte název připojení a vyberte **vytvořit**. 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Vytvoření nové aplikace pro rozpoznávání Qlik":::

5. Po vytvoření připojení můžete zvolit databázi, kolekci, ve které se nachází data hry, a pak je zobrazíte ve verzi Preview.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="Vytvoření nové aplikace pro rozpoznávání Qlik"::: 

6. V dalším kroku vyberte **Přidat data** , aby se data načetla do Qlik. Po načtení dat do Qlikového rozpoznávání můžete vygenerovat přehledy a provádět analýzy dat. Můžete buď použít přehledy nebo sestavit vlastní aplikaci a prozkoumat si prodejní hry. Následující obrázek ukazuje 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Vytvoření nové aplikace pro rozpoznávání Qlik":::

### <a name="limitations-when-connecting-with-odbc"></a>Omezení při připojování pomocí rozhraní ODBC 

Cosmos DB je distribuovaná databáze bez schématu s ovladači modelované pro potřeby vývojářů. Ovladač ODBC vyžaduje pro odvození sloupců, jejich datových typů a dalších vlastností databázi se schématem. Regulární dotaz SQL nebo syntaxe DML s relační schopností se nevztahují na Cosmos DB SQL API, protože rozhraní SQL API není ANSI SQL. Z tohoto důvodu jsou příkazy SQL vydané prostřednictvím ovladače ODBC přeloženy do syntaxe SQL specifické pro Cosmos DB, která nemá ekvivalenty pro všechny konstruktory. Chcete-li zabránit těmto potížím s překladem, je nutné při nastavování připojení ODBC použít schéma. Článek [připojit s ovladačem ODBC](odbc-driver.md) obsahuje návrhy a metody, které vám pomůžou s konfigurací schématu. Nezapomeňte toto mapování vytvořit pro každou databázi nebo kolekci v rámci Cosmos DB účtu.

## <a name="next-steps"></a>Další kroky

Pokud používáte jiný nástroj pro vizualizaci, například Power BI, můžete se k němu připojit pomocí pokynů v následujícím dokumentu:

* [Vizualizace Cosmos DB dat pomocí konektoru Power BI](powerbi-visualize.md)
