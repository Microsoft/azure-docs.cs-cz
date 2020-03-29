---
title: Připojte Qlik Sense k Azure Cosmos DB a vizualizovat svá data
description: Tento článek popisuje kroky potřebné k připojení Azure Cosmos DB k Qlik Sense a vizualizovat data.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67985549"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Připojte Qlik Sense k Azure Cosmos DB a vizualizovat svá data

Qlik Sense je nástroj pro vizualizaci dat, který kombinuje data z různých zdrojů do jednoho zobrazení. Qlik Sense indexuje všechny možné vztahy ve vašich datech, takže můžete získat okamžité přehledy o datech. Data Azure Cosmos DB můžete vizualizovat pomocí Qlik Sense. Tento článek popisuje kroky potřebné k připojení Azure Cosmos DB k Qlik Sense a vizualizovat data. 

> [!NOTE]
> Připojení Qlik Sense k Azure Cosmos DB je aktuálně podporované pro ROZHRANÍ SQL API a rozhraní API Azure Cosmos DB pro účty MongoDB.

Qlik Sense můžete připojit k Azure Cosmos DB pomocí:

* Cosmos DB SQL API pomocí konektoru ODBC.

* Rozhraní API Azure Cosmos DB pro MongoDB pomocí konektoru Qlik Sense MongoDB (aktuálně ve verzi preview).

* Rozhraní API Azure Cosmos DB pro MongoDB a ROZHRANÍ SQL API pomocí konektoru rozhraní REST API v Qlik Sense.

* Cosmos DB Mongo DB API pomocí gRPC konektoru pro Qlik Core.
Tento článek popisuje podrobnosti připojení k rozhraní SQL API Cosmos DB pomocí konektoru ODBC.

Tento článek popisuje podrobnosti připojení k rozhraní SQL API Cosmos DB pomocí konektoru ODBC.

## <a name="prerequisites"></a>Požadavky

Před provedením pokynů v tomto článku se ujistěte, že máte připraveny následující zdroje:

* Stáhněte si [plochu Qlik Sense](https://www.qlik.com/us/try-or-buy/download-qlik-sense) nebo nastavte Qlik Sense v Azure [instalací položky qlik sense marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Stáhněte si [data videohry](https://www.kaggle.com/gregorut/videogamesales), tato ukázková data jsou ve formátu CSV. Tato data uložíte do účtu Cosmos DB a vizualizujete je v Qlik Sense.

* Vytvořte účet Azure Cosmos DB SQL API pomocí kroků popsaných v části [vytvoření účtu](create-sql-api-dotnet.md#create-account) v článku rychlého startu.

* [Vytvořit databázi a kolekci](create-sql-api-java.md#add-a-container) – můžete použít nastavit hodnotu propustnost kolekce na 1000 RU/s. 

* Načtěte ukázková data o prodeji videoher do svého účtu Cosmos DB. Data můžete importovat pomocí nástroje pro migraci dat Azure Cosmos DB, můžete provést [sekvenční](import-data.md#SQLSeqTarget) nebo [hromadný import](import-data.md#SQLBulkTarget) dat. Trvá přibližně 3-5 minut pro import dat do účtu Cosmos DB.

* Stáhněte, nainstalujte a nakonfigurujte ovladač ODBC pomocí kroků v [článku o připojení k Cosmos DB s ovladačem ODBC.](odbc-driver.md) Data videohry je jednoduchá sada dat a nemusíte upravovat schéma, stačí použít výchozí schéma mapování kolekce.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Připojte Qlik Sense k Cosmos DB

1. Otevřete Qlik Sense a vyberte **Vytvořit novou aplikaci**. Zadejte název aplikace a vyberte **Vytvořit**.

   ![Vytvoření nové aplikace Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Po úspěšném vytvoření nové aplikace vyberte **Otevřít aplikaci** a zvolte **Přidat data ze souborů a dalších zdrojů**. 

3. Chcete-li otevřít nové okno nastavení připojení, vyberte ze zdrojů dat **možnost ODBC.** 

4. Přepněte na **dsn uživatele** a zvolte dříve vytvořené připojení ODBC. Zadejte název připojení a vyberte **vytvořit**. 

   ![Vytvoření nového připojení](./media/visualize-qlik-sense/create-new-connection.png)

5. Po vytvoření připojení můžete zvolit databázi, kolekci, kde jsou umístěna data videohry, a pak ji zobrazit náhled.

   ![Výběr databáze a kolekce](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Dále vyberte **Přidat data** pro načtení dat do Qlik Sense. Po načtení dat do aplikace Qlik Sense můžete generovat přehledy a provádět analýzy dat. Můžete buď použít přehledy, nebo vytvořit vlastní aplikaci, která zkoumá prodej videoher. Následující obrázek ukazuje 

   ![Vizualizace dat](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Omezení při připojování k rozhraní ODBC 

Cosmos DB je distribuovaná databáze bez schématu s ovladači modelovanými podle potřeb vývojářů. Ovladač ODBC vyžaduje databázi se schématem k odvodit sloupce, jejich datové typy a další vlastnosti. Běžný dotaz SQL nebo syntaxe DML s relační schopností se nevztahuje na rozhraní SQL API Cosmos DB, protože rozhraní SQL API není ANSI SQL. Z tohoto důvodu jsou příkazy SQL vydané prostřednictvím ovladače ODBC přeloženy do syntaxe SQL specifické pro Cosmos DB, která nemá ekvivalenty pro všechny konstrukce. Chcete-li těmto problémům s překladem zabránit, je nutné při nastavování připojení ODBC použít schéma. Článek [o spojení s ovladačem ODBC](odbc-driver.md) poskytuje návrhy a metody, které vám pomohou nakonfigurovat schéma. Ujistěte se, že vytvořit toto mapování pro každou databázi nebo kolekce v rámci účtu Cosmos DB.

## <a name="next-steps"></a>Další kroky

Pokud používáte jiný vizualizační nástroj, jako je Power BI, můžete se k němu připojit pomocí pokynů v následujícím dokumentu:

* [Vizualizace dat Cosmos DB pomocí konektoru Power BI](powerbi-visualize.md)
