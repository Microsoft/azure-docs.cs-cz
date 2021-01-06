---
title: Power BI a neserverový fond SQL pro analýzu Azure Cosmos DB dat pomocí odkazu synapse
description: Naučte se, jak vytvořit databázi fondu SQL bez serveru a zobrazení přes synapse odkaz pro Azure Cosmos DB, dotazovat kontejnery Azure Cosmos DB a pak vytvořit model pomocí Power BI nad těmito zobrazeními.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: acomet
ms.openlocfilehash: be2657d9606f260fcea06d2535be87fc6976577c
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915670"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-preview-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Použití Power BI a synapse fondu SQL (ve verzi Preview) k analýze Azure Cosmos DB dat pomocí propojení synapse 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

V tomto článku se dozvíte, jak vytvořit databázi fondu SQL bez serveru a zobrazení přes synapse odkaz pro Azure Cosmos DB. Provedete dotaz na kontejnery Azure Cosmos DB a potom sestavíte model pomocí Power BI nad těmito zobrazeními, aby odrážely dotaz.

V tomto scénáři použijete zástupné údaje o prodeji na Surface produktů v partnerském prodejnovém obchodě. Výnosy za obchod budete analyzovat na základě blízkosti velkých domácností a dopadu reklamy na konkrétní týden. V tomto článku vytvoříte dvě zobrazení s názvem **RetailSales** a **StoreDemographics** a dotaz mezi nimi. Z tohoto úložiště [GitHubu](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) můžete získat ukázková data produktu.

> [!IMPORTANT]
> Podpora synapse fondu SQL bez serveru pro odkaz na Azure synapse pro Azure Cosmos DB je v současnosti ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte vytvořit tyto prostředky:

* [Vytvořte Azure Cosmos DB účet druhu SQL (jádro) nebo MongoDB.](create-cosmosdb-resources-portal.md)

* Povolení odkazu Azure synapse pro váš [účet Azure Cosmos](configure-synapse-link.md#enable-synapse-link)

* Vytvořte databázi v rámci účtu Azure Cosmos a vytvořte dva kontejnery s [povoleným analytickým úložištěm.](configure-synapse-link.md#create-analytical-ttl)

* Načtěte data produktů do kontejnerů Azure Cosmos, jak je popsáno v tomto poznámkovém bloku pro příjem [dat služby Batch](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) .

* [Vytvořte pracovní prostor synapse](../synapse-analytics/quickstart-create-workspace.md) s názvem **SynapseLinkBI**.

* [Připojte databázi Azure Cosmos k pracovnímu prostoru synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Vytvoření databáze a zobrazení

V pracovním prostoru synapse otevřete kartu **vývoj** , vyberte **+** ikonu a vyberte **skript SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Přidání skriptu SQL do pracovního prostoru analýzy synapse":::

Každý pracovní prostor přichází s koncovým bodem SQL bez serveru. Po vytvoření skriptu SQL na panelu nástrojů v horní části se připojte k **předdefinovanému**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Povolit skriptu SQL používat koncový bod SQL bez serveru v pracovním prostoru":::

Vytvoří novou databázi s názvem **RetailCosmosDB** a zobrazení SQL nad kontejnery s povoleným odkazem synapse. Následující příkaz ukazuje, jak vytvořit databázi:

```sql
-- Create database
Create database RetailCosmosDB
```

Dále vytvořte více zobrazení napříč různými synapse odkazy s povolenými kontejnery Azure Cosmos. Zobrazení vám umožní použít T-SQL k připojení a dotazování Azure Cosmos DB dat v různých kontejnerech.  Při vytváření zobrazení nezapomeňte vybrat databázi **RetailCosmosDB** .

Následující skripty ukazují, jak vytvořit zobrazení na každém kontejneru. Pro jednoduchost použijte funkci [automatického odvození schématu](analytical-store-introduction.md#analytical-schema) pro fond SQL bez serveru přes kontejnery s povoleným odkazem synapse:


### <a name="retailsales-view"></a>RetailSales zobrazení:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Ujistěte se, že jste do předchozího skriptu SQL vložili oblast Azure Cosmos DB a primární klíč. Všechny znaky v názvu oblasti by měly být v malých případech bez mezer. Na rozdíl od ostatních parametrů `OPENROWSET` příkazu by měl být parametr názvu kontejneru uveden bez uvozovek.

### <a name="storedemographics-view"></a>StoreDemographics zobrazení:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Nyní spusťte skript SQL výběrem příkazu **Spustit** .

## <a name="query-the-views"></a>Dotazování zobrazení

Teď, když jsou tato dvě zobrazení vytvořená, definujte dotaz pro připojení těchto dvou zobrazení následujícím způsobem:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Vyberte **příkaz Spustit** , který jako výsledek poskytne následující tabulku:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Výsledky dotazu po spojení zobrazení StoreDemographics a RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Zobrazení modelu přes kontejnery pomocí Power BI

Potom otevřete Power BI plochu a připojte se k koncovému bodu SQL bez serveru pomocí následujících kroků:

1. Otevřete aplikaci Power BI Desktop. Vyberte **získat data** a vyberte **Další**.

1. V seznamu možností připojení vyberte **Azure synapse Analytics (SQL DW)** .

1. Zadejte název koncového bodu SQL, kde se nachází databáze. `SynapseLinkBI-ondemand.sql.azuresynapse.net`Do pole **Server** zadejte. V tomto příkladu je  **SynapseLinkBI** název pracovního prostoru. Pokud jste přidali jiný název k pracovnímu prostoru, nahraďte ho. Vyberte možnost **přímý dotaz** pro režim připojení dat a pak klikněte na **tlačítko OK**.

1. Vyberte upřednostňovanou metodu ověřování, například Azure AD.

1. Vyberte databázi **RetailCosmosDB** a zobrazení **RetailSales**, **StoreDemographics** .

1. Vyberte **načíst** , pokud chcete načíst dvě zobrazení do režimu přímých dotazů.

1. Vyberte **model** a vytvořte relaci mezi těmito dvěma zobrazeními prostřednictvím sloupce **StoreID** .

1. Přetáhněte sloupec **StoreID** ze zobrazení **RetailSales** směrem ke sloupci **StoreID** v zobrazení **StoreDemographics** .

1. Vyberte vztah M:n (*: 1), protože v zobrazení **RetailSales** existuje více řádků se stejným ID úložiště. **StoreDemographics** má pouze jeden řádek ID úložiště (Jedná se o tabulku dimenzí).

Teď přejděte do okna **sestavy** a vytvořte sestavu s cílem porovnat relativní důležitost velikosti domácnosti s průměrnou tržbou na obchod na základě bodové reprezentace výnosů a indexu LargeHH:

1. Vyberte **bodový graf**.

1. Přetáhněte **LargeHH** ze zobrazení **StoreDemographics** na osu X.

1. Přetáhněte **výnosy** ze zobrazení **RetailSales** na osu Y. Vyberte **průměr** , pokud chcete získat průměrnou cenu za produkt na jeden obchod a za týden.

1. Přetažením pole **productCode** ze zobrazení **RetailSales** do legendy vyberte konkrétní produktovou řadu.
Po výběru těchto možností by se měl zobrazit graf podobný následujícímu snímku obrazovky:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Sestava, která porovnává relativní důležitost velikosti domácnosti s průměrnou tržbou na obchod":::

## <a name="next-steps"></a>Další kroky

[Použití T-SQL k dotazování na data Azure Cosmos DB pomocí odkazu Azure synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Použití fondu SQL bez serveru k [analýze otevřených datových sad Azure a vizualizaci výsledků v Azure synapse studiu](../synapse-analytics/sql/tutorial-data-analyst.md)
