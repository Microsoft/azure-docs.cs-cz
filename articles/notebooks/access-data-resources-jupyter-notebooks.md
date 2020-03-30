---
title: Přístup k datům v poznámkových blocích Jupyter – Azure Notebooks Preview
description: Zjistěte, jak získat přístup k souborům, rest api, databázím a různým prostředkům úložiště Azure z poznámkového bloku Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646343"
---
# <a name="access-cloud-data-in-a-notebook"></a>Přístup ke cloudovým datům v poznámkovém bloku

Dělat zajímavou práci v notebooku Jupyter vyžaduje data. Data jsou skutečně mízou notebooků.

Můžete určitě [importovat datové soubory do](work-with-project-data-files.md)projektu `curl` , a to i pomocí příkazů, jako je v rámci notebooku stáhnout soubor přímo. Je však pravděpodobné, že budete muset pracovat s mnohem rozsáhlejšími daty, která jsou dostupná z nesouborových zdrojů, jako jsou rozhraní REST API, relační databáze a cloudové úložiště, jako jsou tabulky Azure.

Tento článek stručně popisuje tyto různé možnosti. Vzhledem k tomu, že přístup k datům je nejlépe vidět v akci, můžete najít spustitelný kód v [ukázkách poznámkových bloků Azure – přístup k datům](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>Rozhraní REST API

Obecně řečeno, obrovské množství dat dostupných z Internetu není přístupné prostřednictvím souborů, ale prostřednictvím rozhraní REST API. Naštěstí vzhledem k tomu, že buňka poznámkového bloku může obsahovat jakýkoli kód, který se vám líbí, můžete použít kód k odesílání požadavků a přijímání dat JSON. Potom můžete převést, že JSON do libovolného formátu, který chcete použít, jako je například pandas datový rám.

Chcete-li získat přístup k datům pomocí rozhraní REST API, použijte stejný kód v buňkách kódu poznámkového bloku, který používáte v jakékoli jiné aplikaci. Obecná struktura pomocí knihovny požadavků je následující:

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

## <a name="azure-sql-databases"></a>Databáze Azure SQL

K databázím SERVERU SQL můžete přistupovat s pomocí knihoven pyodbc nebo pymssql.

[Pomocí Pythonu můžete zadat dotaz na databázi Azure SQL,](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) která vám poskytne pokyny k vytvoření databáze obsahující data AdventureWorks a ukáže, jak tato data zadat. Stejný kód je zobrazen v ukázkovém poznámkovém bloku pro tento článek.

## <a name="azure-storage"></a>Azure Storage

Azure Storage poskytuje několik různých typů nerelačního úložiště v závislosti na typu dat, která máte a jak k nim potřebujete získat přístup:

- Úložiště tabulek: Poskytuje levné velkoobjemové úložiště pro tabulková data, jako jsou shromážděné protokoly senzorů, diagnostické protokoly a tak dále.
- Úložiště objektů blob: poskytuje úložiště podobné souborům pro jakýkoli typ dat.

Ukázkový poznámkový blok ukazuje práci s tabulkami i objekty BLOB, včetně použití sdíleného přístupového podpisu, který umožňuje přístup jen pro čtení k objektům BLOB.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB poskytuje plně indexované úložiště NoSQL pro dokumenty JSON). Následující články poskytují řadu různých způsobů práce s Cosmos DB z Pythonu:

- [Vytvoření aplikace SQL API s Pythonem](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Vytvoření aplikace Flask s rozhraním API Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Vytvoření databáze grafů pomocí Pythonu a rozhraní Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Vytvoření aplikace Cassandra s Pythonem a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Vytvoření aplikace table api s Pythonem a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Při práci s Cosmos DB, můžete použít [knihovnu azure-cosmosdb-table.](https://pypi.org/project/azure-cosmosdb-table/)

## <a name="other-azure-databases"></a>Další databáze Azure

Azure poskytuje řadu dalších typů databází, které můžete použít. Níže uvedené články poskytují pokyny pro přístup k těmto databázím z Pythonu:

- [Azure Database for PostgreSQL: Použití Pythonu k připojení a dotazování dat](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Rychlý start: Použití Azure Redis Cache s Pythonem](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: Použití Pythonu k připojení a dotazování dat](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Průvodce kopírováním pro Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Další kroky

- [Postup: Práce s datovými soubory projektu](work-with-project-data-files.md)
