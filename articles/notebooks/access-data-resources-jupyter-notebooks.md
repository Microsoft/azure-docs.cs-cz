---
title: Přístup k datovým prostředkům z notebooků Jupyter v Azure
description: Jak získat přístup k souborům, rozhraním API REST, databázím a různým prostředkům Azure Storage z poznámkového bloku Jupyter.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: e1284072db8ca8aa7a49bd3c4d52f54e5d09420a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970258"
---
# <a name="access-cloud-data-in-a-notebook"></a>Přístup k datům v cloudu v poznámkovém bloku

Při provádění zajímavé práce v poznámkovém bloku Jupyter se vyžaduje data. Data jsou skutečně Lifeblood poznámkových blocích.

V případě potřeby můžete [importovat datové soubory do projektu](work-with-project-data-files.md), a to i pomocí příkazů, jako je `curl` z poznámkového bloku pro stažení souboru přímo. Je ale nejspíš potřeba pracovat s mnohem pokročilejšími daty, která jsou dostupná ze zdrojů, které nepatří do souborů, jako jsou rozhraní REST API, relační databáze a cloudové úložiště, jako jsou tabulky Azure.

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

## <a name="azure-sql-databases"></a>Databáze SQL Azure

K databázím SQL Server můžete přistupovat pomocí knihoven pyodbc nebo pymssql.

[Použití Pythonu k dotazování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) vám poskytne pokyny k vytvoření databáze obsahující data AdventureWorks a ukazuje, jak tato data dotazovat. Stejný kód se zobrazí v ukázkovém poznámkovém bloku tohoto článku.

## <a name="azure-storage"></a>Azure Storage

Azure Storage poskytuje několik různých typů nerelačních úložišť, v závislosti na typu dat a způsobu, jakým potřebujete přístup:

- Table Storage: poskytuje s nízkými náklady úložiště pro tabulková data, jako jsou shromážděné protokoly senzorů, diagnostické protokoly a tak dále.
- Úložiště objektů BLOB: poskytuje úložiště podobné souboru pro jakýkoliv typ dat.

Ukázkový Poznámkový blok znázorňuje práci s tabulkami a objekty blob, včetně způsobu použití sdíleného přístupového podpisu, který umožňuje přístup jen pro čtení k objektům blob.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB poskytuje plně indexované úložiště NoSQL pro dokumenty JSON. Následující články poskytují řadu různých způsobů, jak pracovat s Cosmos DB z Pythonu:

- [Vytvoření aplikace API SQL pomocí Pythonu](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Sestavení aplikace v baňce s rozhraním API Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Vytvoření databáze grafu pomocí Pythonu a rozhraní Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Vytvoření aplikace Cassandra pomocí Pythonu a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Vytvoření aplikace rozhraní API pro tabulky pomocí Pythonu a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Při práci s Cosmos DB můžete použít knihovnu [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) .

## <a name="other-azure-databases"></a>Další databáze Azure

Azure poskytuje řadu dalších typů databází, které můžete použít. Níže uvedené články poskytují pokyny pro přístup k těmto databázím z Pythonu:

- [Azure Database for PostgreSQL: použití Pythonu pro připojení a dotazování dat](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Rychlý Start: použití Azure Redis Cache s Pythonem](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: použití Pythonu pro připojení a dotazování dat](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Průvodce kopírováním pro Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Další kroky

- [Postupy: práce s datovými soubory projektu](work-with-project-data-files.md)
