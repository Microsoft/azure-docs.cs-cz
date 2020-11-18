---
title: Přístup k datům v poznámkových blocích Jupyter – Preview Azure Notebooks
description: Přečtěte si, jak získat přístup k souborům, rozhraním REST API, databázím a různým prostředkům Azure Storage z poznámkového bloku Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: a833ff914c1ee53f024147371977ac1caa3800dc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842868"
---
# <a name="access-cloud-data-in-a-notebook"></a>Přístup ke cloudovým datům v poznámkovém bloku

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Při provádění zajímavé práce v poznámkovém bloku Jupyter se vyžaduje data. Data jsou skutečně Lifeblood poznámkových blocích.

V některých případech můžete [importovat datové soubory do projektu](work-with-project-data-files.md), a to i pomocí příkazů, jako `curl` je v rámci poznámkového bloku, ke stažení souboru přímo. Je ale nejspíš potřeba pracovat s mnohem pokročilejšími daty, která jsou dostupná ze zdrojů, které nepatří do souborů, jako jsou rozhraní REST API, relační databáze a cloudové úložiště, jako jsou tabulky Azure.

Tento článek stručně popisuje tyto různé možnosti. Vzhledem k tomu, že se přístup k datům nejlépe zobrazuje v akci, najdete spustitelný kód v [ukázkách Azure Notebooks – přístup k datům](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>Rozhraní REST API

Obecně řečeno, obrovské množství dat, která jsou k dispozici z Internetu, se k souborům nepoužívá, ale prostřednictvím rozhraní REST API. Naštěstí, protože buňka poznámkového bloku může obsahovat jakýkoli kód, který chcete, můžete použít kód k posílání požadavků a přijímání dat JSON. Pak můžete tento kód JSON převést do libovolného formátu, který chcete použít, například PANDAS dataframe.

Pro přístup k datům pomocí REST API použijte stejný kód v buňkách kódu poznámkového bloku, které používáte v jakékoli jiné aplikaci. Základní struktura pomocí knihovny požadavků je následující:

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

## <a name="azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database a SQL Managed instance

K databázím v SQL Database nebo spravované instanci SQL získáte přístup pomocí knihoven pyodbc nebo pymssql.

[Použití Pythonu k dotazování databáze SQL Azure](../azure-sql/database/connect-query-python.md) vám poskytne pokyny k vytvoření databáze v SQL Database obsahující data AdventureWorks a ukazuje, jak tato data dotazovat. Stejný kód se zobrazí v ukázkovém poznámkovém bloku tohoto článku.

## <a name="azure-storage"></a>Azure Storage

Azure Storage poskytuje několik různých typů nerelačních úložišť, v závislosti na typu dat a způsobu, jakým potřebujete přístup:

- Table Storage: poskytuje s nízkými náklady úložiště pro tabulková data, jako jsou shromážděné protokoly senzorů, diagnostické protokoly a tak dále.
- Úložiště objektů BLOB: poskytuje úložiště podobné souboru pro jakýkoliv typ dat.

Ukázkový Poznámkový blok znázorňuje práci s tabulkami a objekty blob, včetně způsobu použití sdíleného přístupového podpisu, který umožňuje přístup jen pro čtení k objektům blob.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB poskytuje plně indexované úložiště NoSQL pro dokumenty JSON. Následující články poskytují řadu různých způsobů, jak pracovat s Cosmos DB z Pythonu:

- [Vytvoření aplikace API SQL pomocí Pythonu](../cosmos-db/create-sql-api-python.md)
- [Sestavení aplikace v baňce s rozhraním API Azure Cosmos DB pro MongoDB](../cosmos-db/create-mongodb-flask.md)
- [Vytvoření databáze grafu pomocí Pythonu a rozhraní Gremlin API](../cosmos-db/create-graph-python.md)
- [Vytvoření aplikace Cassandra pomocí Pythonu a Azure Cosmos DB](../cosmos-db/create-cassandra-python.md)
- [Vytvoření aplikace rozhraní API pro tabulky pomocí Pythonu a Azure Cosmos DB](../cosmos-db/table-storage-how-to-use-python.md)

Při práci s Cosmos DB můžete použít knihovnu [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) .

## <a name="other-azure-databases"></a>Další databáze Azure

Azure poskytuje řadu dalších typů databází, které můžete použít. Níže uvedené články poskytují pokyny pro přístup k těmto databázím z Pythonu:

- [Azure Database for PostgreSQL: Použití Pythonu k připojení a dotazování dat](../postgresql/connect-python.md)
- [Rychlý start: Použití Azure Redis Cache s Pythonem](../azure-cache-for-redis/cache-python-get-started.md)
- [Azure Database for MySQL: Použití Pythonu k připojení a dotazování dat](../mysql/connect-python.md)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Průvodce kopírováním pro Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Další kroky

- [Postupy: práce s datovými soubory projektu](work-with-project-data-files.md)