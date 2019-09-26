---
title: Používejte integrované příkazy a funkce poznámkového bloku v Azure Cosmos DB
description: Naučte se používat integrované příkazy a funkce k provádění běžných operací pomocí integrovaných poznámkových bloků Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310344"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Používejte integrované příkazy a funkce poznámkového bloku v Azure Cosmos DB

Integrované poznámkové bloky Jupyter v Azure Cosmos DB umožňují analyzovat a vizualizovat data z Azure Portal. Tento článek popisuje, jak používat integrované příkazy a funkce poznámkového bloku k provádění běžných operací.

## <a name="install-a-new-package"></a>Nainstalovat nový balíček
Po povolení podpory poznámkových bloků pro účty Azure Cosmos můžete otevřít nový Poznámkový blok a nainstalovat balíček.

Do nové buňky kódu vložte a spusťte následující kód, který nahraďte ``PackageToBeInstalled`` požadovaným balíčkem Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Tento balíček bude k dispozici pro použití z libovolného poznámkového bloku v účtu Azure Cosmos. 

## <a name="run-a-sql-query"></a>Spustit dotaz SQL

Pomocí ``%%sql`` příkazu Magic můžete spustit [dotaz SQL](sql-query-getting-started.md) pro libovolný kontejner ve vašem účtu. Použijte syntaxi:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` Nahraďte ``{container_id}`` a názvem databáze a kontejneru v účtu Cosmos. Nejsou-li ``--container`` argumenty azadány,dotazbudeprovedenvevýchozídatabáziakontejneru.``--database`` [](#set-default-database-for-queries)
- Můžete spustit libovolný dotaz SQL, který je platný v Azure Cosmos DB. Text dotazu musí být na novém řádku.

Příklad: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Spusťte ```%%sql?``` v buňce, abyste viděli dokumentaci k příkazu SQL Magic v poznámkovém bloku.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Spuštění dotazu a výstupu SQL do PANDAS dataframe

Výsledky ``%%sql`` dotazu můžete vyvýstupovat do [PANDAS dataframe](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Použijte syntaxi: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` Nahraďte ``{container_id}`` a názvem databáze a kontejneru v účtu Cosmos. Nejsou-li ``--container`` argumenty azadány,dotazbudeprovedenvevýchozídatabáziakontejneru.``--database`` [](#set-default-database-for-queries)
- Nahraďte ``{outputDataFrameVar}`` názvem proměnné datového rámce, která bude obsahovat výsledky.
- Můžete spustit libovolný dotaz SQL, který je platný v Azure Cosmos DB. Text dotazu musí být na novém řádku. 

Příklad:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Nastavit výchozí databázi pro dotazy
Můžete nastavit výchozí databázové ```%%sql``` příkazy, které budou použity pro Poznámkový blok. Nahraďte ```{database_id}``` názvem vaší databáze.

```bash
%database {database_id}
```
Spusťte ```%database?``` v buňce, abyste viděli dokumentaci v poznámkovém bloku.

## <a name="set-default-container-for-queries"></a>Nastavení výchozího kontejneru pro dotazy
Můžete nastavit výchozí příkazy kontejneru ```%%sql``` , které budou použity pro Poznámkový blok. Nahraďte ```{container_id}``` názvem vašeho kontejneru.

```bash
%container {container_id}
```
Spusťte ```%container?``` v buňce, abyste viděli dokumentaci v poznámkovém bloku.

## <a name="use-the-built-in-python-sdk"></a>Použití integrované sady Python SDK
V prostředí poznámkového bloku pro účet Cosmos je nainstalovaná verze 4 sady [Azure Cosmos DB Python SDK pro SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) .

Použijte vestavěnou ``cosmos_client`` instanci pro spuštění jakékoli operace sady SDK. 

Příklad:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Viz [ukázky sady Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Integrovaná sada Python SDK je podporována pouze pro účty rozhraní SQL (Core) API. U jiných rozhraní API budete muset [nainstalovat příslušný ovladač Pythonu](#install-a-new-package) , který odpovídá rozhraní API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Vytvoření vlastní instance``cosmos_client``
Pro větší flexibilitu můžete vytvořit vlastní instanci ``cosmos_client`` nástroje s cílem:

- Přizpůsobení [zásad připojení](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Spuštění operací na jiném účtu Cosmos, než v jakém jste

K připojovacímu řetězci a primárnímu klíči aktuálního účtu můžete přistupovat pomocí [proměnných prostředí](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Přístup ke koncovému bodu účtu a k proměnným ENV klíče primárního klíče
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Proměnné prostředí ``COSMOS_KEY`` a se vztahují pouze na rozhraní SQL API. ``COSMOS_ENDPOINT`` V případě jiných rozhraní API Najděte koncový bod a klíč v okně **připojovací řetězce** nebo **klíče** v účtu Cosmos.  

## <a name="next-steps"></a>Další kroky

- Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
- Další informace o sadě [Azure Cosmos DB Python SDK pro SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
