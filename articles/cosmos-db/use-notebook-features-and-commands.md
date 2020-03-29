---
title: Použití integrovaných příkazů a funkcí poznámkového bloku v Azure Cosmos DB (preview)
description: Zjistěte, jak používat integrované příkazy a funkce k běžným operacím pomocí integrovaných poznámkových bloků Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513395"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Použití integrovaných příkazů a funkcí poznámkového bloku v Azure Cosmos DB (preview)

Integrované poznámkové bloky Jupyter v Azure Cosmos DB vám umožní analyzovat a vizualizovat data z webu Azure Portal. Tento článek popisuje, jak provádět běžné operace s využitím integrovaných příkazů a funkcí poznámkového bloku.

## <a name="install-a-new-package"></a>Instalace nového balíčku
Po povolení podpory poznámkových bloků pro vaše účty Azure Cosmos můžete otevřít nový poznámkový blok a nainstalovat balíček.

V nové buňce kódu vložte a ``PackageToBeInstalled`` spusťte následující kód a nahraďte požadovaný balíček Pythonu.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Tento balíček bude k dispozici pro použití z libovolného poznámkového bloku v pracovním prostoru účtu Azure Cosmos. 

> [!TIP]
> Pokud poznámkový blok vyžaduje vlastní balíček, doporučujeme přidat do poznámkového bloku buňku pro instalaci balíčku, protože balíčky budou odebrány při [resetování pracovního prostoru](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Spuštění dotazu SQL

Pomocí příkazu ``%%sql`` magic můžete spustit [dotaz SQL](sql-query-getting-started.md) proti libovolnému kontejneru ve vašem účtu. Použijte syntaxi:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Nahraďte ``{database_id}`` a ``{container_id}`` s názvem databáze a kontejneru v účtu Cosmos. Pokud ``--database`` argumenty a ``--container`` nejsou k dispozici, dotaz bude proveden na [výchozí databázi a kontejneru](#set-default-database-for-queries).
- Můžete spustit libovolný dotaz SQL, který je platný v Azure Cosmos DB. Text dotazu musí být na novém řádku.

Například: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Spuštěním ```%%sql?``` v buňce zobrazíte dokumentaci nápovědy pro příkaz SQL Magic v poznámkovém bloku.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Spuštění dotazu SQL a výstupu do datového rámce Pandas

Výsledky dotazu ``%%sql`` můžete vyvýstupu do [datového rámce Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Použijte syntaxi: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Nahraďte ``{database_id}`` a ``{container_id}`` s názvem databáze a kontejneru v účtu Cosmos. Pokud ``--database`` argumenty a ``--container`` nejsou k dispozici, dotaz bude proveden na [výchozí databázi a kontejneru](#set-default-database-for-queries).
- Nahraďte ``{outputDataFrameVar}`` názvem proměnné DataFrame, která bude obsahovat výsledky.
- Můžete spustit libovolný dotaz SQL, který je platný v Azure Cosmos DB. Text dotazu musí být na novém řádku. 

Například:

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
## <a name="upload-json-items-to-a-container"></a>Nahrání položek JSON do kontejneru
Příkaz ``%%upload`` magic můžete použít k nahrání dat ze souboru JSON do zadaného kontejneru Azure Cosmos. K nahrání položek použijte následující příkaz:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Nahraďte ``{database_id}`` a ``{container_id}`` s názvem databáze a kontejneru ve vašem účtu Azure Cosmos. Pokud ``--database`` argumenty a ``--container`` nejsou k dispozici, dotaz bude proveden na [výchozí databázi a kontejneru](#set-default-database-for-queries).
- Nahraďte ``{url_location_of_file}`` umístěním souboru JSON. Soubor musí být pole platných objektů JSON a měl by být přístupný prostřednictvím veřejného Internetu.

Například:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Pomocí výstupních statistik můžete vypočítat efektivní RU/s použité k nahrání položek. Například pokud 25 000 RU bylo spotřebováno během 38 sekund, efektivní RU/s je 25 000 RU / 38 sekund = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Nastavení výchozí databáze pro dotazy
Můžete nastavit výchozí ```%%sql``` databázové příkazy, které budou pro poznámkový blok používat. Nahraďte ```{database_id}``` se názvem databáze.

```bash
%database {database_id}
```
Spuštěním ```%database?``` v buňce zobrazíte dokumentaci v poznámkovém bloku.

## <a name="set-default-container-for-queries"></a>Nastavení výchozího kontejneru pro dotazy
Můžete nastavit výchozí ```%%sql``` příkazy kontejneru, které budou pro poznámkový blok používat. Nahraďte ```{container_id}``` název kontejneru.

```bash
%container {container_id}
```
Spuštěním ```%container?``` v buňce zobrazíte dokumentaci v poznámkovém bloku.

## <a name="use-built-in-nteract-data-explorer"></a>Použití integrovaného průzkumníka dat nteract
Vestavěný [průzkumník dat nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) můžete filtrovat a vizualizovat datový rámec. Chcete-li tuto funkci ``pd.options.display.html.table_schema`` povolit, nastavte možnost ``True`` na požadovanou hodnotu (můžete ``pd.options.display.max_rows`` nastavit tak, ``pd.options.display.max_rows`` aby ``None`` zobrazovala všechny výsledky).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![průzkumník dat nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Použití předdefinované sady Python SDK
Verze 4 [sady Azure Cosmos DB Python SDK pro rozhraní SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) je nainstalovaná a zahrnutá v prostředí poznámkového bloku pro účet Azure Cosmos.

Pomocí předdefinované ``cosmos_client`` instance spusťte libovolnou operaci sady SDK. 

Například:

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
> Integrovaná sada Python SDK je podporována pouze pro účty rozhraní API SQL (Core). Pro ostatní rozhraní API budete muset [nainstalovat příslušný ovladač Pythonu,](#install-a-new-package) který odpovídá rozhraní API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Vytvoření vlastní instance``cosmos_client``
Pro větší flexibilitu můžete vytvořit vlastní ``cosmos_client`` instanci, aby:

- Přizpůsobení [zásad připojení](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Spouštění operací s jiným účtem Azure Cosmos, než ve které se nacházejíte

Přístup k připojovacímu řetězci a primárnímu klíči běžného účtu můžete přistupovat prostřednictvím [proměnných prostředí](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Přístup ke koncovým bodům účtu a proměnným env primárního klíče
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Proměnné ``COSMOS_ENDPOINT`` ``COSMOS_KEY`` a prostředí jsou použitelné pouze pro rozhraní SQL API. Pro ostatní api najděte koncový bod a klíč v okně **připojovací řetězce** nebo **klíče** ve vašem účtu Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Obnovení pracovního prostoru poznámkových bloků
Pokud chcete pracovní prostor poznámkových bloků obnovit na výchozí nastavení, vyberte na panelu příkazů **obnovit pracovní plochu.** Tím odeberete všechny vlastní nainstalované balíčky a restartujte server Jupyter. Vaše poznámkové bloky, soubory a prostředky Azure Cosmos nebudou ovlivněny.  

![Obnovení pracovního prostoru poznámkových bloků](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Další kroky

- Informace o výhodách [notebooků Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Informace o [azure cosmos DB Python SDK pro SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
