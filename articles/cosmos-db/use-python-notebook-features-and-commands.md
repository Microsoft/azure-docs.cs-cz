---
title: Použití integrovaných příkazů a funkcí poznámkového bloku v Azure Cosmos DB poznámkových blocích Pythonu (Preview)
description: Naučte se používat integrované příkazy a funkce k provádění běžných operací pomocí integrovaných poznámkových bloků Pythonu Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: b89fcf32ed033f359b4db601e36cc69bb899944d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165820"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Použití integrovaných příkazů a funkcí poznámkového bloku v Azure Cosmos DB poznámkových blocích Pythonu (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Integrované poznámkové bloky Jupyter v Azure Cosmos DB umožňují analyzovat a vizualizovat data z Azure Portal. Tento článek popisuje, jak pomocí integrovaných příkazů a funkcí poznámkového bloku provádět běžné operace v poznámkových blocích Pythonu.

## <a name="install-a-new-package"></a>Nainstalovat nový balíček
Po povolení podpory poznámkových bloků pro účty Azure Cosmos můžete otevřít nový Poznámkový blok a nainstalovat balíček.

Do nové buňky kódu vložte a spusťte následující kód, který nahraďte ``PackageToBeInstalled`` požadovaným balíčkem Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Tento balíček bude k dispozici pro použití z libovolného poznámkového bloku v pracovním prostoru účet Azure Cosmos. 

> [!TIP]
> Pokud váš Poznámkový blok vyžaduje vlastní balíček, doporučujeme přidat do svého poznámkového bloku buňku pro instalaci balíčku, protože balíčky se odeberou, když [pracovní prostor resetujete](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Spustit dotaz SQL

Pomocí ``%%sql`` příkazu Magic můžete spustit [dotaz SQL](sql-query-getting-started.md) pro libovolný kontejner ve vašem účtu. Použijte syntaxi:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Nahraďte ``{database_id}`` a ``{container_id}`` názvem databáze a kontejneru v účtu Cosmos. Nejsou ``--database`` -li ``--container`` argumenty a zadány, dotaz bude proveden ve [výchozí databázi a kontejneru](#set-default-database-for-queries).
- Můžete spustit libovolný dotaz SQL, který je platný v Azure Cosmos DB. Text dotazu musí být na novém řádku.

Například: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Spusťte ```%%sql?``` v buňce, abyste viděli dokumentaci k příkazu SQL Magic v poznámkovém bloku.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Spuštění dotazu a výstupu SQL do PANDAS dataframe

Výsledky dotazu můžete vyvýstupovat ``%%sql`` do [PANDAS dataframe](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame). Použijte syntaxi: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Nahraďte ``{database_id}`` a ``{container_id}`` názvem databáze a kontejneru v účtu Cosmos. Nejsou ``--database`` -li ``--container`` argumenty a zadány, dotaz bude proveden ve [výchozí databázi a kontejneru](#set-default-database-for-queries).
- Nahraďte ``{outputDataFrameVar}`` názvem proměnné datového rámce, která bude obsahovat výsledky.
- Můžete spustit libovolný dotaz SQL, který je platný v Azure Cosmos DB. Text dotazu musí být na novém řádku. 

Například:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Nastavit výchozí databázi pro dotazy
Můžete nastavit výchozí databázové příkazy, které ```%%sql``` budou použity pro Poznámkový blok. Nahraďte ```{database_id}``` názvem vaší databáze.

```python
%database {database_id}
```
Spusťte ```%database?``` v buňce, abyste viděli dokumentaci v poznámkovém bloku.

## <a name="set-default-container-for-queries"></a>Nastavení výchozího kontejneru pro dotazy
Můžete nastavit výchozí příkazy kontejneru, které ```%%sql``` budou použity pro Poznámkový blok. Nahraďte ```{container_id}``` názvem vašeho kontejneru.

```python
%container {container_id}
```
Spusťte ```%container?``` v buňce, abyste viděli dokumentaci v poznámkovém bloku.

## <a name="upload-json-items-to-a-container"></a>Nahrání položek JSON do kontejneru
Pomocí ``%%upload`` příkazu Magic můžete nahrávat data ze souboru JSON do zadaného kontejneru Azure Cosmos. Následující příkaz slouží k nahrání položek:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Nahraďte ``{database_id}`` a ``{container_id}`` názvem databáze a kontejneru v účtu Azure Cosmos. Nejsou ``--database`` -li ``--container`` argumenty a zadány, dotaz bude proveden ve [výchozí databázi a kontejneru](#set-default-database-for-queries).
- Nahraďte ``{url_location_of_file}`` umístěním souboru JSON. Tento soubor musí být pole platných objektů JSON a měl by být přístupný prostřednictvím veřejného Internetu.

Například:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
S využitím výstupních statistik můžete vypočítat efektivní RU/s, který se použije k nahrání položek. Například pokud 25 000 ru bylo spotřebováno více než 38 sekund, efektivní RU/s je 25 000 ru/38 sekund = 658 RU/s.

Soubory (například CSV nebo soubory JSON) můžete ukládat do místního pracovního prostoru poznámkového bloku. Pro uložení souborů doporučujeme přidat do svého poznámkového bloku buňku. Tyto soubory můžete zobrazit z integrovaného terminálu v prostředí poznámkového bloku. Uložené soubory můžete zobrazit pomocí příkazu ls. Tyto soubory se ale odeberou, když pracovní prostor resetujete. Proto doporučujeme místo místního pracovního prostoru použít trvalé úložiště, jako je GitHub nebo účet úložiště.

## <a name="run-another-notebook-in-current-notebook"></a>Spustit jiný Poznámkový blok v aktuálním poznámkovém bloku 
Pomocí ``%%run`` příkazu Magic můžete v pracovním prostoru spustit jiný Poznámkový blok z aktuálního poznámkového bloku. Použijte syntaxi:

```python
%%run ./path/to/{notebookName}.ipynb
```
Nahraďte ``{notebookName}`` názvem poznámkového bloku, který chcete spustit. Poznámkový blok musí být ve vašem aktuálním pracovním prostoru "moje poznámkové bloky". 

## <a name="use-built-in-nteract-data-explorer"></a>Použití integrovaného Průzkumníka dat nteract
Pomocí integrovaného [Průzkumníka dat nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) můžete filtrovat a vizualizovat datový rámec. Chcete-li tuto funkci povolit, nastavte ``pd.options.display.html.table_schema`` možnost ``True`` na ``pd.options.display.max_rows`` požadovanou hodnotu (můžete nastavit na hodnotu ``pd.options.display.max_rows`` ``None`` pro zobrazení všech výsledků).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="Průzkumník dat nteract":::

## <a name="use-the-built-in-python-sdk"></a>Použití integrované sady Python SDK
V prostředí poznámkového bloku pro účet Azure Cosmos je nainstalovaná verze 4 sady [Azure Cosmos DB Python SDK pro SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) .

Použijte vestavěnou ``cosmos_client`` instanci pro spuštění jakékoli operace sady SDK. 

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
> Integrovaná sada Python SDK je podporována pouze pro účty rozhraní SQL (Core) API. U jiných rozhraní API budete muset [nainstalovat příslušný ovladač Pythonu](#install-a-new-package) , který odpovídá rozhraní API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Vytvoření vlastní instance ``cosmos_client``
Pro větší flexibilitu můžete vytvořit vlastní instanci nástroje s ``cosmos_client`` cílem:

- Přizpůsobení [zásad připojení](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview)
- Spuštění operací s jiným účtem Azure Cosmos, než v jakém jste

K připojovacímu řetězci a primárnímu klíči aktuálního účtu můžete přistupovat pomocí [proměnných prostředí](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Přístup ke koncovému bodu účtu a k proměnným ENV klíče primárního klíče
Máte přístup k integrovanému koncovému bodu a klíči účtu, ve kterém je váš Poznámkový blok.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> ``COSMOS.ENDPOINT``Proměnné a ``COSMOS.KEY`` se vztahují pouze na rozhraní SQL API. V případě jiných rozhraní API Najděte koncový bod a klíč v okně **připojovací řetězce** nebo **klíče** v účtu Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Resetovat pracovní prostor poznámkových bloků
Pokud chcete resetovat pracovní prostor poznámkových bloků na výchozí nastavení, na panelu příkazů vyberte **resetovat pracovní prostor** . Tím se odeberou všechny vlastní nainstalované balíčky a restartuje se server Jupyter. Vaše poznámkové bloky, soubory a prostředky Azure Cosmos nebudou ovlivněny.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Resetovat pracovní prostor poznámkových bloků":::

## <a name="next-steps"></a>Další kroky

- Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
- Další informace o sadě [Azure Cosmos DB Python SDK pro SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)