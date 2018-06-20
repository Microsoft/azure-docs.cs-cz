---
title: Začínáme se službou Azure Table Storage a rozhraním Table API služby Azure Cosmos DB pomocí Pythonu | Microsoft Docs
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 42e7ef8699ee5f481c6a1c45832dfa3428e3baa8
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798198"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Začínáme se službou Azure Table Storage a rozhraním Table API služby Azure Cosmos DB pomocí Pythonu

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage a Azure Cosmos DB jsou služby, které ukládají strukturovaná data NoSQL do cloudu a poskytují úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že jsou služby Table Storage a Azure Cosmos DB bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům služby Table Storage a rozhraní Table API je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage nebo Azure Cosmos DB můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

### <a name="about-this-sample"></a>O této ukázce
Tato ukázka předvádí použití [sady Table SDK služby Azure Cosmos DB pro Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) v běžných scénářích služby Azure Table Storage. Název sady SDK značí, že je určená k použití se službou Azure Cosmos DB, ale stejně tak funguje i se službou Azure Table Storage, obě služby mají pouze jiný koncový bod. Tyto scénáře se zkoumají na příkladech v Pythonu a znázorňují následující postupy:
* Vytváření a odstraňování tabulek
* Vkládání a dotazování entit
* Úpravy entit

Při procházení scénářů v této ukázce můžete využít [referenční informace k sadě SDK služby Azure Cosmos DB pro rozhraní Python API](https://azure.github.io/azure-cosmosdb-python/).

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5, nebo 3.6
- [Sada Table SDK služby Azure Cosmos DB pro Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Tato sada SDK umožňuje připojení ke službě Azure Table Storage i k rozhraní Table API služby Azure Cosmos DB.
- [Účet služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) nebo [účet služby Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalace sady Table SDK služby Azure Cosmos DB pro Python

Po vytvoření účtu služby Storage je dalším krokem instalace [sady Table SDK služby Microsoft Azure Cosmos DB pro Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Podrobnosti o instalaci sady SDK najdete v souboru [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) v úložišti sady Table SDK služby Cosmos DB pro Python na GitHubu.

## <a name="import-the-tableservice-and-entity-classes"></a>Import tříd TableService a Entity

Při práci s entitami ve službě Azure Table Storage v Pythonu se používají třídy [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) a [Entity][py_Entity]. Importujte obě třídy přidáním tohoto kódu na začátek souboru Pythonu:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Připojení ke službě Azure Table Storage

Pokud se chcete připojit ke službě Azure Table Storage, vytvořte objekt [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) a předejte do něj název a klíč vašeho účtu služby Storage. Nahraďte `myaccount` a `mykey` názvem a klíčem vašeho účtu.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Připojení ke službě Azure Cosmos DB

Pokud se chcete připojit ke službě Azure Cosmos DB, zkopírujte z webu Azure Portal primární připojovací řetězec a s jeho použitím vytvořte objekt [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html):

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Vytvoření tabulky

Zavoláním metody [create_table][py_create_table] vytvořte tabulku.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Entitu přidáte tak, že nejprve vytvoříte objekt reprezentující entitu a tento objekt pak předáte do metody [TableService.insert_entity][py_TableService]. Objektem entity, který definuje názvy a hodnoty vlastností entity, může být slovník nebo objekt typu [Entita][py_Entity]. Každá entita musí kromě případných dalších vlastností, které pro ni definujete, obsahovat i požadované vlastnosti [PartitionKey a RowKey](#partitionkey-and-rowkey).

Tento příklad vytvoří objekt slovníku reprezentující entitu a pak ho předá do metody [insert_entity][py_insert_entity], která ho přidá do tabulky:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Tento příklad vytvoří objekt [Entity][py_Entity] a pak ho předá do metody [insert_entity][py_insert_entity], která ho přidá do tabulky:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey a RowKey

Pro každou entitu musíte zadat vlastnost **PartitionKey** i **RowKey**. Jedná se o jedinečné identifikátory entit a společně tvoří primární klíč entity. Pomocí těchto hodnot můžete provádět mnohem rychlejší dotazování než na jakékoli ostatní vlastnosti entit, protože pouze tyto vlastnosti se indexují.

Table Storage používá **PartitionKey** k inteligentní distribuci entit tabulky napříč uzly úložiště. Entity se stejnou hodnotou **PartitionKey** se ukládají na stejném uzlu. **RowKey** je jedinečné ID entity v rámci oddílu, do kterého patří.

## <a name="update-an-entity"></a>Aktualizace entity

Pokud chcete aktualizovat všechny hodnoty vlastností entity, zavolejte metodu [update_entity][py_update_entity]. Tento příklad ukazuje nahrazení stávající entity její aktualizovanou verzí:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Pokud aktualizovaná entita ještě neexistuje, operace aktualizace selže. Pokud chcete entitu uložit bez ohledu na to, jestli existuje nebo ne, použijte metodu [insert_or_replace_entity][py_insert_or_replace_entity]. V následujícím příkladu první volání nahradí stávající entitu. Druhé volání vloží novou entitu, protože v tabulce neexistuje žádná entita se zadanými hodnotami PartitionKey a RowKey.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Metoda [update_entity][py_update_entity] nahradí všechny vlastnosti a hodnoty stávající entity, což můžete využít i k odebrání vlastností ze stávající entity. Pomocí metody [merge_entity][py_merge_entity] můžete aktualizovat stávající entitu s použitím nových nebo upravených hodnot vlastností, aniž byste entitu museli zcela nahradit.

## <a name="modify-multiple-entities"></a>Úpravy více entit

Pokud chcete zajistit atomické zpracování požadavku službou Table Storage, můžete odeslat více operací společně v dávce. Nejprve pomocí třídy [TableBatch][py_TableBatch] přidejte několik operací do jedné dávky. Pak zavoláním metody [TableService][py_TableService].[commit_batch][py_commit_batch] odešlete operace v rámci atomické operace. Všechny entity, které se mají v dávce upravit, musí být ve stejném oddílu.

Tento příklad v dávce přidá dvě entity najednou:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Dávky je možné použít také se syntaxí správce kontextu:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Dotaz na entitu

Pokud chcete zadat dotaz na entitu v tabulce, předejte její PartitionKey a RowKey do metody [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Dotaz na sadu entit

Dotaz na sadu entit můžete zadat uvedením řetězce filtru v parametru **filter**. Tento příklad vyhledá všechny úlohy v Seattlu použitím filtru hodnoty PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity

U každé entity v dotazu také můžete omezit, jaké vlastnosti se vrátí. Tato technika, označovaná jako *projekce*, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit nebo sad výsledků. Použijte parametr **select** a předejte názvy vlastností, které chcete vrátit do klienta.

Dotaz v následujícím kódu vrátí pouze popisy entit v tabulce.

> [!NOTE]
> Následující fragment kódu funguje pouze pro službu Azure Storage. Emulátor úložiště ho nepodporuje.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Odstranění entity

Entitu odstraníte tak, že předáte její **PartitionKey** a **RowKey** do metody [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud už tabulku nebo jakékoli entity v ní nepotřebujete, zavoláním metody [delete_table][py_delete_table] trvale odstraňte tabulku ze služby Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy – Vývoj pomocí rozhraní Table API](https://docs.microsoft.com/azure/cosmos-db/faq#develop-with-the-table-api)
* [Referenční informace k sadě SDK služby Azure Cosmos DB pro rozhraní Python API](https://azure.github.io/azure-cosmosdb-python/)
* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [Průzkumník služby Microsoft Azure Storage:](../vs-azure-tools-storage-manage-with-storage-explorer.md) Bezplatná aplikace pro různé platformy umožňující vizuálně pracovat s daty ve službě Azure Storage v systému Windows, macOS a Linux.
* [Práce s Pythonem v sadě Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
