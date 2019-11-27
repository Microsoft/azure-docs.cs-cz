---
title: Přístup k prostředkům data z aplikace Jupyter notebook v Azure
description: Jak přistupovat k souborům, rozhraní REST API, databáze a různé prostředky služby Azure Storage z poznámkového bloku Jupyter.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 23acdf99f6cb69f100e484e236580f3b2b43ba94
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277686"
---
# <a name="access-cloud-data-in-a-notebook"></a>Přístup k datům cloudu v poznámkovém bloku

Zajímavé práce v poznámkovém bloku Jupyter vyžaduje data. Data, je ve skutečnosti lifeblood poznámkových bloků.

V případě potřeby můžete [importovat datové soubory do projektu](work-with-project-data-files.md), a to i pomocí příkazů, jako je `curl` z poznámkového bloku pro stažení souboru přímo. Je pravděpodobné, ale, že je nutné pracovat s mnohem rozsáhlejší data, která je k dispozici z jiných souborů zdrojů, jako je rozhraní REST API, relačních databází a cloudového úložiště, jako jsou tabulky Azure.

Tento článek stručně popisuje tyto různé možnosti. Vzhledem k tomu, že se přístup k datům nejlépe zobrazuje v akci, najdete spustitelný kód v [ukázkách Azure Notebooks – přístup k datům](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>Rozhraní REST API

Ohromné množství dat, které jsou dostupné z Internetu obecně přistupuje, ne prostřednictvím souborů, ale přes rozhraní REST API. Naštěstí protože Poznámkový blok buňka může obsahovat jakýkoli kód vám vyhovuje, můžete použít kód pro odesílání požadavků a přijímání dat JSON. Tento kód JSON pak můžete převést do formátu, kterou chcete použít, jako je například pandas dataframe.

Pro přístup k datům pomocí rozhraní REST API, použijte stejný kód v buňkách poznámkového bloku kódu, které můžete použít v jakékoli jiné aplikace. Použití knihovny žádosti o obecnou strukturu vypadá takto:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL Database

Databáze systému SQL Server můžete přistupovat pomocí pomoc pyodbc nebo pymssql knihoven.

[Použití Pythonu k dotazování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) vám poskytne pokyny k vytvoření databáze obsahující data AdventureWorks a ukazuje, jak tato data dotazovat. Stejný kód je zobrazena ve vzorku poznámkového bloku pro účely tohoto článku.

## <a name="azure-storage"></a>Azure Storage

Azure Storage nabízí několik různých typů nerelační úložiště, v závislosti na typu dat, ke kterým máte a jak budete potřebovat pro přístup k ní:

- Table Storage: poskytuje úložiště s nízkými náklady, vysoký počet pro tabulková data, jako jsou protokoly služby sensor shromážděných, diagnostické protokoly a tak dále.
- Úložiště objektů blob: poskytuje soubor jako úložiště pro data libovolného typu.

Poznámkový blok ukázka ukazuje práci s tabulkami a objekty BLOB, jak povolit přístup jen pro čtení k objektům BLOB pomocí sdíleného přístupového podpisu.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB zajišťující NoSQL s dvojicí plně indexovaných dokumentů JSON). Následující články poskytují celou řadou způsobů, jak pracovat s Cosmos DB z Pythonu:

- [Vytvoření aplikace API SQL pomocí Pythonu](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Sestavení aplikace v baňce s rozhraním API Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Vytvoření databáze grafu pomocí Pythonu a rozhraní Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Vytvoření aplikace Cassandra pomocí Pythonu a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Vytvoření aplikace rozhraní API pro tabulky pomocí Pythonu a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Při práci s Cosmos DB můžete použít knihovnu [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) .

## <a name="other-azure-databases"></a>Ostatní databáze Azure

Azure nabízí celou řadu jiných typů databází, které můžete použít. Následující články poskytují pokyny pro přístup k těmto databázím z Pythonu:

- [Azure Database for PostgreSQL: použití Pythonu pro připojení a dotazování dat](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Rychlý Start: použití Azure Redis Cache s Pythonem](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: použití Pythonu pro připojení a dotazování dat](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Průvodce kopírováním pro Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Další kroky

- [Postupy: práce s datovými soubory projektu](work-with-project-data-files.md)
